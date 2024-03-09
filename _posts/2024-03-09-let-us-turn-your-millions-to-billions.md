---
layout: post
title: Let's Turn your Millions to Billions
date:   2024-03-09 11.11.00 +0200
categories: Metamatic Systems MongoDB 
---

Hello again and shiny month of March 2024! 

Last time I wrote an article to showcase how MongoDB can be used in a relational way. To continue from that point,
I want to stop here for a second to ask you this question. What is the right database for you?

In my previous article I showed that MongoDB can be used similarly to an SQL
database. Some readers might think these days it's a severe sin (or at least horrific abuse) to use MongoDB in a relational
way. 

Now, let's not be too single-minded even here, because it all depends!

If your objective is to serve billions, not millions, queries to your data during a certain time unit, or
if you want to access collections with billions, not millions entries, it is likely that you should cache and combine the data
into all-in-one collections and do whatever you can to prevent lookups from occuring.

However, optimizing queries for speed means sacrificing flexibility. If your data is not *really big* and the
performance isn't the bottle neck but instead you need to provide a flexible way to allow queries to look at the
data creatively from different angles, a relational model is the best fit. This is the trade-off between the
relational and document-like ways to see the world.

Now the fantastic thing about MongoDB is that it allows you to take both approaches. A very typical scenario is
that some of your data is "big" and some is "small". Let's say that you have some measurement instruments or server logs
that produce huge amounts of data. Obviously, a document database model is the best match to make queries to this kind of data.
On the other hand, your employee or customer data is different. Typically this kind of data contains a limited numberof o entries 
that isn't accumulating at a huge speed. Obviously, you need two different database engines for these two
domains. You need something like AWS DynamoDB for measurements or logs and something like Postgres SQL database for eveything
else. 

This may be a source of quite some extra headache because you have then two different databases for two different type of
data, that require you to have two different systems for these two different things. You have to pair two systems, two worlds, 
two groups of engineers, two different platforms etc. Now the fantastic thing about MongoDB is that you can use this one database 
management system for both big data and not so big data. 

In my previous article I wrote about writing lookups for MongoDB, which enables you to use it somewhat similarly to SQL
databases. However, if your intention is to provide a low latency system that allows to perform super fast queries
into huge data sets, obviously lookups aren't the ideal way to go. When you want to provide a rapid access to data sets,
MongoDB provides a fantastic architecture to combine data into collections that already are the perfect match for their queries.
This approach serves well scenarios where it can be assumed that there is a rather fixed set of queries that clients will be 
constantly executing. 

Denormalizing data from multiple sources into collections unarguably causes data duplication - it is actually all about caching. 
Doing data denormalization to achieve velocity means sacrificing storage space to save processing time.
To query data from collections with billions, not millions of entries, we want to avoid complex lookups and SQLish approach to queries.
To learn more about strategies for reducing lookup operations, I recommend to have a look at [this article](https://www.mongodb.com/docs/atlas/schema-suggestions/reduce-lookup-operations/).

I'll be back writing more about the topic. Let's stay tuned and look at the world from different angles. 
The truth is out there, or somewhere between!




