
*Get the most out of your NoSQL DB*

  > GeoHashing

  > Index Overloading

  > Write Sharding

  > Z-Order indexing

  > Adjacency list pattern

# A Quick Prelude
  
NoSQL databases are often misunderstood.  Developers transfer relational database practices to their implentation of NoSQL schemas.  Thus, 
many applications do not benefit from NoSQL design as they should.  Most applications should only require a **single** denormalized table to handle
all of their access patterns.

I never much liked the terms denormalized or normalized for NoSQL.  Far more intriguing than whether or not the data is uniform,
are the partitions that NoSQL maintain at all times.  These partitions exist to be queried rapidly at massive scale while drawing minimal CPU usage.
While the the table as a whole may be "denormalized", often (in a well designed schema) many of the partitions will be normalized. Therefore, it
is important to understand **ALL** of an application's access patterns at the outset of a project.

This causes the following dilemma: 
1. Developer wants to utitlize NoSQL for rapid queries.
2. Developer wants to optimize for CPU usage (an expensive resource) rather than disk space (an inexpensive resource).
3. Developer wants scalability that is not easily attainable with conventional relational database models.

but..

1. Developer is not confident that their access patterns will not change.
2. Developer is not confident in his/her NoSQL schema design.

In order to solve this dilemma, I introduce Partitions.

# Partitions {docsify-ignore}
A Graphql engine driven by DynamoDB

Partitions is a GraphQL engine on top of DynamoDb that manages
- Global Secondary Indexes
- Partition Keys (essentially scopes)
- Shards (actually just an implementation of partition keys)
- Adjacency lists (NoSQL pattern for handling complex relational data)
- Zero order indexing (a sortable one-dimensional index derived from an n-dimensional data source to reduce the expense of a query)

and

- Geohashing (binding data to a geological location in order to execute efficient proximal queries)

And all you have to do is set up a GraphQL Type Definition file. [ Get Started ](quickstart.md)

