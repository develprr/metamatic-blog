---
layout: post
title: Many to Many Relationships with MongoDB 
date:   2023-04-09 05.01.00 +0300
categories: Metamatic Systems
---

Welcome to the future! Hey visitor, time traveller, do you come from the 
dark ages of the last decade where giant relational databases ruled?
Don't worry, it's year 2023 now we have moved on: we have something 
better for you. Check out document databases! 

The best document databases that we have here in the future allow you to 
insert different kinds of complex objects into your ~~tables~~ collections 
without the headache casting fixed schemas on your tables, and still have all the benefits
of an SQL database - indexing, sophisticated queries, joins and still supporting
the timeless relational model. Isn't it fantastic to live in the future?

Let's dive deeper!

But let me ask first, 

## Is the relational model going away?

Now, this is deep. Are the relational databases going
away? Are they some ancient artifacts of the past? I don't think so. Let me explain why!
A database that supports the relational database model is a database
that supports relational concepts like one-to-one, one-to-many and many-to-many.

This is a really natural way to see the world. Actually,
I can hardly think about the world without relations at all! Can you?

For example, think about a mother. Think about her children. A mother can have one
or many children. It's safe to say that a mother and a child has a one-to-many 
relationship. It doesn't work otherwise. If you have a mother without children,
it's not a mother. It's something else. The way we humans see the world,
it's all about relations. Relational databases organize object similarly
into object types that are stored in their own groups, or "tables". These
databases offer a simple and powerful concept to organize the relations 
between these objects in a way that is very natural, mimicking the way 
we see the world as well. Therefore, the relational databases will hardly go away at all!

## In a way, MongoDB is a relational database

It's a quite common to think that a relational database equals an SQL database.
SQL is a query language used to make database queries to relational databases.
I challenge this view because MongoDB, not being an SQL database since it doesn't
support SQL queries, makes it possible to combine data in a way very
similar to typical relational databases. Therefore, I would still call MongoDB a relational
database - since it makes possible to form relations between objects similarly
to SQL-based relational databases. If you wish!

## Getting hands dirty

That's all about philosophy for now. I'll show next how to make the most the 
relational databases with MongoDB, while still enjoying the fruit of flexibility of a document
based database. Let's get started and implement...

## Many-to-many models in MongoDB!

Another practical example of a many-to-many scenario in the real world would
be a school that can have many courses and pupils. Yet each pupil can attend 
many courses and each course will be attended by many pupils.

To start with, let's describe the scenario first with TypeScript models. 
This makes sense, because we will be soon loading data from MongoDB into those
TypeScript objects:

```TypeScript
interface ISchool {
    _id: string;
    name: string;
    courses?: ICourse[];
    pupils?: IPupil[]
}

interface IPupil {
    _id: string;
    schoolId: string;
    name: string;
    school?: ISchool;
    courses?: ICourse[]
}

interface ICourse {
    _id: string;
    schoolId: string; 
    name: string;
    school?: ISchool;
    pupils?: ICourse[];
}
```

Note that I added a prefix "I" to each model here! Why did I do it?
I am just using here a convention that separates data objects from their
actual handlers. If I don't mix methods into the data objects but keep the
data objects separate and plain, it will be a lot easier to serialize them
directly into JSON objects. And JSON objects can be quite handily transferred
over REST (or other) APIs over the Internet. Not to forget how this approach can boost
your unit tests when you have a clear separation between data objects and their handlers.

To put it short, I added the "I" prefix to these data objects so I can
have separate handler objects such as *School*, *Pupil* and *Course*. 

Next time, let's check out how to build and keep relationships between these 
objects on database level when working with MongoDB.

Cheers!
