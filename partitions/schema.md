

## Building your schema

Here is an example of a basic Graphql type.  Defining this type in Partitions will give you create, update and delete mutations and read one or read many queries.

<pre><code class="language-graphql">
type BasicType {
  nonNullableFloat: Float!
  nonNullableInt: Int!
  nonNullableString: String!
  nullableFloat: Float
  nullableInt: Int
  nullableString: String
  stringArray: [String!]!
  intArray: [Int!]
  floatArray: [Float]
}
</code></pre>

Graphql provides String, ID, Float, Int and Boolean as primitive types(! marks field as required).  For complex relational data the developer can define types of his/her own.

<pre><code class="language-graphql">
type Child {
  name: String!
  key: Int!
}

type Parent {
  name: String! 
  key: Int! 
  child: Child!
}
</code></pre>

In the above sample, ```Parent``` has a relationship to child.  This enables the developer to connect children to parents or create a related child within the same mutation as the parent.

## Two way data binding

In complex schemas, you may find a need for a parent object to be queriable by it's children.  Partitions handles two-way relationships elegantly. Define the schema normally with each type related to the other.

<pre><code class="language-graphql">
type TwoWayRelationshipParent {
  name: String!
  child: TwoWayRelationshipChild!
}

type TwoWayRelationshipChild {
  name: String!
  parent: TwoWayRelationshipParent!
}
</code></pre>

The ```this``` keyword for id will connect a child type to it's parent type in a two-way relationship.

<pre><code class="language-graphql">
mutation {
  createTwoWayRelationshipParent(data: {
    name: "Dad",
    child: {
      create: {
        name: "You",
        parent: {
          create: {
            child: {
              connect: { id: "this" }
            }
          }
        }
      }
    }
  })
}
</code></pre>

You could also connect the child to any other parent type in your system.

Partitions exposes a number of [directives](directives.md) to fulfill more complex access patterns.
