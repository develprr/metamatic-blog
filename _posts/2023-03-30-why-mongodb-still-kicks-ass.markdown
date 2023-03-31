---
layout: post
title: Why MongoDB Still Kicks Ass
date:   2023-03-30 09.18.00 +0300
categories: Metamatic Systems
---

The spring is coming. So it's more relevant than ever to start thinking 
how to manage your many-to-many relationships!

## The problem with relational databases

Now, if you ever worked with relational databases, this is rather trivial. 
But I know that many people hate relational databases because of their rigid schema 
requirements on your tables. 

Having a fixed predefined schema can be good or bad, depending on the case.
If you have a huge amount of data coming in in the form of logs, 
containing entries with potentially varying properties, there's a problem. 

If it's expected that your data may not be uniform and your data entries
may significantly vary, then defining all properties that a table may contain as a
fixed number of nullable columns can be quite awkward, resulting in a huge number
of mostly empty columns in your table. And if you store the objects
just as blobs, then SQL's query capabilities become quite useless.

Also, when your table grows huge (millions of rows), your database
starts choking. 

## Why MongoDB?

MongoDB, being a document database, has a certain edge over 
relational databases because it doesn't require a schema for each table.

Having worked for years on projects that used relational databases,
it was really inspiring to start using MongoDB.  

Namely, MongoDB is a document database, meaning that you don't have to define the schema
for each table (known as "collection" in MongoDB) and you can really
easily insert a lot more complex objects into the database than you would
be able to do with relational databases - and still execute fine-grained
queries to your data almost as if t was a relattional database.

MongoDB has good query and aggregation features, making
agile and combinatory queries possible similarly to SQL. With MongoDB,
you can do inner joins and combine data from multiple collections (tables),
similarly to SQL. Also, with MongoDB you can create pipelines out of 
your aggregation queries, storing results into transitional collections.
This aggregation mechanism allows quite complex data transformations.

This is a concept that SQL databases don't know and it makes
MongoDB a very powerful tool.

## How about big data?

When there is really lots of data in the database (and more coming in)
then an essential question is, how can MongoDB handle that!

My experience is that when the collection size grows over 5 million
entries, then it starts to impact MongoDB's capability to perform
queries efficiently.

## Solving big data through partitioning

Now we are approaching a key point here. Creating collections in MongoDB
is really easy and cheap. In MongoDB, you can have 28 000 collections in
one database! And remember that "database" here is just a namespace 
to group collections. Therefore you can have practically an unlimited
number of collections. This means that when you use MongoDB, you can *and you should*
split data into multiple collections when your data grows big.

## How does this help my laundry service corporation?

Let's say that you have a laundry service corporation and you are storing
all log data from all your machines into MongoDB.

Now, if you used just one table to store all logs that each
laundry machine is generating, your table would blow up very soon.
Luckily, with MongoDB you would easily make your laundry machine corporation
scalable by storing each laundry machine's log data into a separate table (collection),
so the number of your laundry machines would equal the number of your tables.

Now, this might sound like a heresy in the old SQL world, but it's a quite
obvious solution in the world of MongoDB. You could then further partition
the laundry machine data by year, having each year's log data in their
own collection that is year specific: 

```
 collection_name = "#{machine_id}:#{year}"
```

## Why not just use DynamoDB?

Amazon's DynamoDB is a relatively new kid on the block. Similarly it's a document DB
that does not have schema for the table.

DynamoDB promises that you can use just one table and store everything into
that table. No need to have multiple tables and you will still have fast access
to your data, no matter how big it grows!

## It's still partitioning

When you look carefully into how DynamoDB actually works, it turns out 
that what it does is actually the same partitioning approach that I just described
with MongoDB. Namely, each *partition key* in DynamoDB's table actually
defines another collection, similarly like you would split data into multiple collections
in MongoDB.

## So which is better?

When you use DynamoDB, you marry yourself to the Amazon family.
This is entirely fine. The difference is that with DynamoDB,
you *must* be on Amazon. With MongoDB, you *can* be on Amazon.

The other difference to DynamoDB is that DynamoDB makes it harder to execute more complex combinatory queries 
to your data. You will need to define your tables from early
on to serve a certain query approach. If you want to make different queries
later, you may be facing a major problem. With MongoDB's powerful queries, you
don't have that problem.

## Not all data is big

In any laundry service corporation, there is a need for both big data
and *not so big* data. Actually, it may be the case that the only real need
for supporting big data is when you are dealing with laundry machine logs.
You also have lots of other data that isn't so huge and isn't also expected
to grow very fast. This data could something like the customer data,
employee data, financial data, inventory data etc. All kinds of other data
that isn't growing rapidly. For this kind of data, DynamoDB isn't very suitable.
You should not try to solve every problem with the same tool. 

For the big data, such as laundry machine logs, you might want to use DynamoDB
although that is also manageabe with MongoDB, but for the not so big data,
you would be better off using some other database solution that is more competent
in making agile queries, such a MongoDB. In those cases, 
you might want to consider even more traditional SQL
databases, such as PostGRE and even some MySQL type of thing.

Just saying!

Cheers!
