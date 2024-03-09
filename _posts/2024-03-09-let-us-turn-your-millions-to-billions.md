---
layout: post
title: Let's Turn Your Millions into Billions
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

## The trade-off

If your objective is to serve billions, not millions, queries to your data during a certain time unit, or
if you want to access collections with billions, not millions entries, it is likely that you should cache and combine the data
into all-in-one collections and do whatever you can to prevent lookups from occuring.

However, optimizing queries for speed means sacrificing flexibility. If your data is not *really big* and the
performance isn't the bottle neck but instead you need to provide a flexible way to allow queries to look at the
data creatively from different angles, a relational model is the best fit. This is the trade-off between the
relational and document-like ways to see the world.

## Two worlds 

Now the fantastic thing about MongoDB is that it allows you to take both approaches. A very typical scenario is
that some of your data is "big" and some is "small". Let's say that you have some measurement instruments or server logs
that produce huge amounts of data. Obviously, a document database model is the best match to make queries to this kind of data.
On the other hand, your employee or customer data is different. Typically this kind of data contains a limited number of entries 
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

## It's all about caching
Denormalizing data from multiple sources into collections unarguably causes data duplication - it is actually all about caching. 
Doing data denormalization to achieve velocity means sacrificing storage space to save processing time.
To query data from collections with billions, not millions of entries, we want to avoid complex lookups and SQLish approach to queries.
To learn more about strategies for reducing lookup operations, I recommend to have a look at [this article](https://www.mongodb.com/docs/atlas/schema-suggestions/reduce-lookup-operations/).

## How to start getting cloudy with MongoDB
If you are a hands-on person (like me) and can't wait to actually start getting productive with database development, the next 
thing that probably crosses your mind is where should I start right now? Depending on your prerequisites, there are a few alternatives. 
For starters, you have to make up your mind on which "planet" you want to live on. The major ecosystems to choose from at the time
being are Amazon AWS or Microsoft Azure. Amazon AWS has been longer around than Microsoft Azure and is therefore somewhat more widely adopted. However, Microsoft Azure ecosystem is rapidly catching up with Amazon AWS and is only slightly behind in terms of market share. 

In general, Amazon AWS has a wider range of services. It may sound good but in reality it is actually far from ideal. It turns out that
Amazon AWS has many partially or fully overlapping services and tools, making it confusing to figure out the right stack. Also the AWS
service family is quite fragmented and does not provide a clearly opinionated pathway. The same applies to the documentation which is quite
decentralized. AWS web console is also confusing and far from intuitive to use. On the other hand, Microsoft Azure appears far more focused and provides a family of services that is a well thought out complete product with all parts playing well together. Having worked on both environments, I would recommend to go for Microsoft Azure and .NET if you don't need to carry any AWS based legacy "lambda" liabilities on your shoulders.

My personal opinion as a freelance developer is that by choosing Microsoft Azure, you will get your web services developed, deployed, up and running in a fraction of the time that would be required when working on AWS. The complexity and fragmentation of the AWS ecosystem means that your company needs a dedicated "cloud architect" to navigate in this mess. On Microsoft Azure, on the other hand, the things seem properly streamlined and orchestrated by definition, which means that normal "off-the-shelf" developers will be able to construct a robust and maintainable systems - just by following Microsoft's instructions written in cleartext.

Now, back to MongoDB. If you want to go for MongoDB and wonder how to implement your cloud-based business system on top of it, let's examine how to possibly go about it in each mentioned cloud enviroment. 

## Amazon AWS
If you have to choose AWS, just go ahead and install [AWS SAM tool](https://docs.aws.amazon.com/serverless-application-model/latest/developerguide/install-sam-cli.html) - as suprising as it may sound. Namely, this tool isn't anything Mongo-specific. So let me explain my thinking!

When you want to start using MongoDB Atlas Data API on AWS, you need to use [AWS API Gateway](https://aws.amazon.com/api-gateway/).
You can configure AWS API Gateway directly on [AWS console](https://docs.aws.amazon.com/apigateway/latest/developerguide/getting-started.html)
but if you have at typical coder mindset, this approach probably starts causing some headache even before you do anything. 
A classical coder of course wants to see everything as *code*, not as some complex menus on a confusing web console. 
You want to write your lambdas as *code* on *your* machine and neatly deploy your creation to the cloud from the command line. 
To create your first API Gateway application with SAM and *Python* language, have a look at this [workshop](https://catalog.workshops.aws/serverless-app-with-sam/en-US). You can develop Lambda functions with many languages, such as Java, JavaScript, Ruby, TypeScript, GO etc. 
However, given that I don't know you, anonymous reader, statistically speaking you most likely want to write your functions with
Python - because it just happens to be the most popular programming language these days.

## Microsoft Azure
To get started implementing scalable cloud services on Microsoft Azure, I recommend to start with this sane and clear article on implementing your [first Mongo app on ASP.net core](https://learn.microsoft.com/en-us/aspnet/core/tutorials/first-mongo-app). Have a good journey. I'll be be writing about
this and other cool topics maybe even sooner than you realize!

