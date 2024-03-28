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
No need to be a rocket scientist to take your database to Cosmos DB! As your final step after fastening the seat belts, read [this manual](https://learn.microsoft.com/en-us/azure/cosmos-db/mongodb/quickstart-dotnet?tabs=azure-cli%2Cwindows) about how to start the rocket engine.
When you are done, just push the button! See you up there! ![build-and-run-store-api]({{site.baseurl }}/assets/build-and-run-store-api.gif)

Just kidding, it isn't *that* easy. This article's narration is only starting to approach its turning point. We are coming to the question, why *exactly* would I want to configure
a database *at all* in the first place? Let's get the big picture!

## Getting Down to Business

To understand what we want to achieve with all this vanity under the sun, let's imagine that you are
a start-up entrepreneur full of enthusiasm and you want to start selling something - let's say food - in your brand new online store. Ideally, you want to name your new company as Kalabaw Foods Ltd. After all, ~~no steak tastes as good as a real kalabaw rib~~ no animal symbolizes diligence better than the kalabaw.

So you need to have three main components in your business system:

1. A Database to store the business data (subscriptions, products, users etc.)
2. A Store API to handle requests by the application and interact with the database
3. A web application for the store (made with React, Angular, Vue or why not with Blazor)

When our imaginary kalabaw entrepreneur is done with the first step on the list and deployed his food store database to Azure cloud, then it's time to proceed over to the second step. These days
you never know how much demand there will be for the delicious products by Kalabaw Foods Ltd.
So you don't want your servers to crash immediately when everybody rushes to your online store.
Luckily, *Azure functions* are a great way to create an API that will scale up to the rush on our online store!

## Your Roadmap to Azure Functions Development

A good starting point for Azure functions devlopment on Visual Studio Code is to 
install Microsoft's Azure tools extension.

![the-cosmos]({{site.baseurl }}/assets/install-azure-tools-to-vscode.gif)

From there, I recommend to proceed to installing [Azure functions core tools](https://github.com/Azure/azure-functions-core-tools/blob/v4.x/README.md#linux), which is quite easy. On Ubuntu Linux operating system it just requires to copy paste fouyyr commands.

Firstly, add the Microsoft repository GPG key:
```
curl https://packages.microsoft.com/keys/microsoft.asc | gpg --dearmor > microsoft.gpg
sudo mv microsoft.gpg /etc/apt/trusted.gpg.d/microsoft.gpg
```
Then, update your sources list:
```
sudo sh -c 'echo "deb [arch=amd64] https://packages.microsoft.com/repos/microsoft-ubuntu-$(lsb_release -cs)-prod $(lsb_release -cs) main" > /etc/apt/sources.list.d/dotnetdev.list'
```
Update your APT source:
```
sudo apt-get update
```
Finally, install the core tools package:
```
sudo apt-get install azure-functions-core-tools-4
```

Check out also the comprehensive Microsoft's tutorial to Azure functions local development [here](https://learn.microsoft.com/en-us/azure/azure-functions/functions-run-local?tabs=linux%2Cisolated-process%2Cnode-v4%2Cpython-v2%2Chttp-trigger%2Ccontainer-apps&pivots=programming-language-csharp).

![install-azure-functions-core-tools]({{site.baseurl }}/assets/install-azure-functions-core-tools.gif)

Cheers!
