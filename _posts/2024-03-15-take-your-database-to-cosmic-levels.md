---
layout: post
title: Take Your Database to Cosmic Levels
date:   2024-03-15 11.12.00 +0200
categories: MongoDB CosmosDB Azure
---

![onboarding a space rocket]({{ site.baseurl }}/assets/onboarding-rocket.png)

Hello again and have a rainy Friday on 15th March 2024! Let's check out how to take our databases to Cosmos DB! Luckily, it's easier than ever! To fully apply [MongoDB](https://www.mongodb.com/) to secure rainy days in your future, enabling you to build a robust AI infrastructure with a scalable vector search capability, full text search, graph data, streaming data, geospatial data and more,
you need to start hosting your MongoDB in Microsoft's ground-breaking [Cosmos DB](https://learn.microsoft.com/en-us/azure/cosmos-db/introduction).

## Your Roadmap to the Cosmos 
To get serious with a scalable MongoDB in Cosmos DB you need to find a concrete starting point. In this article I want to be practical as usual and point out the right spot as I see it.  For starters, instead of painting with a large brush, let's drill down into the details - that's the way you do it! I recommend these three steps to start with:

* First, create your first Web API with ASP.NET
* Then, learn to configure your Web API to use a local MongoDB
* Tune your API to use cloud based Cosmos DB instead

![roadmap-to-cosmos]({{site.baseurl }}/assets/roadmap-to-cosmos.png)

To successfully walk through this road to Cosmos DB, I recommend to start with [this tutorial](https://learn.microsoft.com/en-us/aspnet/core/tutorials/first-web-api?view=aspnetcore-8.0&amp;tabs=visual-studio-code) to implement your first ASP.NET based web API.
Then deepen your ASP.NET API skills by implementing a MongoDB API [here](https://learn.microsoft.com/en-us/aspnet/core/tutorials/first-mongo-app).
When you are done with these two steps, continue working on the API you wrote first and enhance it by adding some MongoDB controller to it as well.
Make sure that your API works. You will greatly benefit by configuring [Swagger](https://swagger.io/) for for your API. [Here](https://learn.microsoft.com/en-us/aspnet/core/tutorials/web-api-help-pages-using-swagger?view=aspnetcore-8.0) is a good tutorial for configuring Swagger for your ASP.NET based web API.

If you stumble in these steps, have a look at [my example implementation "StoreApi"](https://github.com/develprr/StoreApi) to make your first web API to interact with MongoDB! I also wrote a standalone project [CosmosApi](https://github.com/develprr/CosmosApi) to help you create your first MongoDB database instance in Cosmos DB and verify that you can interact with it.

## Onboarding the Rocket
No need to be a rocket scientist to take your database to Cosmos DB! As your final step after fastening the seat belts, read [this
manual](https://learn.microsoft.com/en-us/azure/cosmos-db/mongodb/quickstart-dotnet?tabs=azure-cli%2Cwindows) about how to start the rocket engine.

<vid:build-and-run-store-api>
When you are done, just push the button! See you up there!

![the-cosmos]({{site.baseurl }}/assets/the-cosmos.png)