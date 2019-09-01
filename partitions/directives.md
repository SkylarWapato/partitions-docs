# Using directives

Partitions ships with 3 directives to help with common use patterns.
* @sortable
* @increment
* @geo

## Sort directive

Some access patterns require objects to be sortable by one of their property values.  To enable sorting by a propery value add the sortable directive to a primitave type (String, ID, Float, Int and Boolean).

<pre><code class="language-graphql">
type SortableType {
  string: String! @sortable
  int: Int! @sortable
  float: Float! @sortable
}
</code></pre>

Adding the directive above allows to sort by any of the fields endowed with the ```@sortable``` directive. Now, a sort argument will appear for the ```SortableType``` on its plural query.

<pre><code class="language-graphql">
{
  sortableTypes( sort: string_ASC ){
    id
    string
    int
    float
  }
}
</code></pre>

ASC is one of many sort options.  I recommend using a tool like [Grapql Playground](https://electronjs.org/apps/graphql-playground) to explore your schema once you've created it.
You are permitted 6 string sortable fields and 3 number sortable fields per type definition.  See the further reading of this section to understand that limitation.

### Further reading

You may be asking "Why am I limited at all when it comes to sorting?" or "Why use a tool that limits what I can sort".  The truth is, sorting has always been limited.  CPUs across the world pay the price for the complex sorting offered by SQL solutions.

The spirit of NoSQL is to remove the stress from the CPU.  This means that instead of sorting the data upon retrieval, we instruct dynamo to maintain a partition that is already sorted to our needs.  Responses are lightning quick because there is no computation necessary.  The data already exists in a partition, sorted to our needs.  

We accomplish this using Global Secondary Indexes. 

#### Global Secondary Indexes

Now you might be asking "How can I have 9 sortable fields per type definition when AWS limits me to 20 overall" or "If I have 1000 type definitions all with 9 sortable fields that would be 9000 GSIs.  Well above the 20 cap AWS enforces".

Rest assured.  Partitions uses only 9 GSIs.  This is accomplished using a pattern dubbed [Index Overloading](https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/bp-gsi-overloading.html).

#### Index Overloading

A denormalized schema will store data of any shape in the same table.  It is not required for items to share any properties.  But, if data with disparate properties share the GSI property they can be sorted in thier own partition (based on partition and gsi sort key).  We simply map your properties to one of the 9 GSIs.  Since each type definition stores data to a different partition, the partitions will be sorted independently based on the ```@sortable``` directive.  Making data items that have different property values (and exist in different partitions) share the gsi property is referred to as [Index Overloading](https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/).

## Increment Directive

The increment directive will make a property behave as an auto-incrementing id.

<pre><code class="language-graphql">
type Foo {
  name: String!
  key: Int! @increment
}
</code></pre>

Key's with this directive will not need to be set on creation mutations.

<pre><code class="language-graphql">
mutation {
  createFoo(data: {
    name: "foo"
  })
}
</code></pre>

Instead, the ```key``` property will be auto-incremented upon creation.

| WARNING: High traffic create mutations can cause duplicate auto-incrementing fields due to race conditions ! |
| --- |

Even so, you can still utilize the ```@increment``` and ```@sortable``` directives to implement complex pagination(even with duplicate increment fields).

## Geo Directive
A fan favorite, the ```@geo``` directive binds data to to a geological location.

<pre><code class="language-graphql">
type GeoType @geo {
  name: String!
  number: Int!
}
</code></pre>

Once the ```@geo``` directive has been implented, the object will require a (latitude, longitude) pair in order to be created.

<pre><code class="language-graphql">
mutation{
  createGeoScopedGeoType(data: {
    name: "NYC",
    number: 1
  },
  latitude:40.712776,
  longitude: -74.005974)
}
</code></pre>

Now, you can search the data by (lat, lon) and proximity (kilometers).  This is a search for NYC from my hometown Okmulgee. 

<pre><code class="language-graphql">
{
  geoTypesByLocation(
    latitude: 36,
    longitude: -95,
    proximity: 1900
  ) {
    id
    name
    number
  }
}
</code></pre>

My hometown is just under 1900km from New York City.  So we get the entry we stored previously.  Also the query 

<pre><code class="language-graphql">
{
  geoTypesByLocation(
    latitude: 36,
    longitude: -95,
    proximity: 1800
  ) {
    id
    name
    number
  }
}
</code></pre>
Will return no results because the search radius is too small.

### Further Reading

The ```geo``` directive utilizes [z-order indexing](https://aws.amazon.com/blogs/database/z-order-indexing-for-multifaceted-queries-in-amazon-dynamodb-part-1/) by mapping the (lat, lon) pair to a single queriable index.  Each (lat, lon) pair will be mapped to a single 32 bit hash string called a z-address.  Storing on this index is broadly referred to as [GeoHashing](https://en.wikipedia.org/wiki/Geohash).  Each item's index under this mapping is broadly referred to as it's geohash.