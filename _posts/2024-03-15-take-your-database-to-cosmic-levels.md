---
layout: post
title: Take Your Database to Cosmic Levels
date:   2024-03-15 11.12.00 +0200
categories: MongoDB CosmosDB Azure
---

![onboarding a space rocket]({{ site.baseurl }}/assets/onboarding-rocket.png)

Hello again and have a rainy Friday on 15th March Anno Domini 2024! Rain is like the subtle touch of the Creator on the surface of the Earth. 
Wherever the rain touches the surface of our planet, it transforms death into life, melts the ice, greens the ground. Life always finds its ways. 
It may already be secretly hitchhiking aboard rockets to flood the Cosmos, like a passenger on an ancient Ark floating toward a strange land across the eternal ocean.

So why not take our databases to the Cosmos as well? Luckily,
it's easier than ever! To fully apply [MongoDB](https://www.mongodb.com/) to secure rainy days in your future,
enabling you to build a robust AI infrastructure with a scalable vector
search capability, full text search, graph data, streaming data, geospatial data and more,
you need to start hosting your MongoDB in Microsoft's ground-breaking [CosmosDB](https://learn.microsoft.com/en-us/azure/cosmos-db/introduction).

## Your Roadmap to the Cosmos 
To get serious with a scalable MongoDB in CosmosDB you need to find a concrete starting point. In this
article I want to be practical as usual and point out the right spot as I see it. 
For starters, instead of painting with a large brush, let's drill down into the details - that's the way you do it! 
I recommend these three steps to start with:

* First, create your first Web API with ASP.NET
* Then, learn to configure your Web API to use a local MongoDB
* Tune your API to use cloud based CosmosDB instead

To successfully walk through this road to the Cosmos, I recommend to start with [this tutorial](https://learn.microsoft.com/en-us/aspnet/core/tutorials/first-web-api?view=aspnetcore-8.0&amp;tabs=visual-studio-code) to implement your first ASP.NET based web API.
Then deepen your ASP.NET API skills by implementing a MongoDB API [here](https://learn.microsoft.com/en-us/aspnet/core/tutorials/first-mongo-app).
When you are done with these two steps, continue working on the API you wrote first and enhance it by adding some MongoDB controller to it as well.
Make sure that your API works. You will greatly benefit by configuring [Swagger](https://swagger.io/) for for your API. [Here](https://learn.microsoft.com/en-us/aspnet/core/tutorials/web-api-help-pages-using-swagger?view=aspnetcore-8.0) is a good tutorial for configuring Swagger for your ASP.NET based web API.

If you stumble in these steps, have a look at [my example implementation "StoreApi"](https://github.com/develprr/StoreApi) to make your first web API to interact with MongoDB!

## Onboarding the Rocket
No need to be a rocket scientist to take your database to CosmosDB! As your final steps before fastening the seat belts, Read [this
manual](https://learn.microsoft.com/en-us/azure/cosmos-db/mongodb/quickstart-dotnet?tabs=azure-cli%2Cwindows) how to start the rocket engine.
When you are done, just push the button! See you up there!


