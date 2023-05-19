---
layout: post
title: How to Lubricate Your Data Flow With Kafka and Web Sockets
date:   2023-05-19 15.00.00 +0300
categories: Metamatic Systems
---

Hey you right there on the other side of the display! I congratulate you for being
chosen by the Algorithm to view this particular blog post, because
today I am going to rumble like thinder about how you can make your data 
flow through your systems smoothly as silk and and fast as lightning!

Refering to my previous blog posts that discussed different database solutions
to store and access huge amounts of data in databases without choking them,
let's touch the other side of the coin now.

## Who needs a lubricated system?

Namely, it is not going to be helpful to access and store your data easily
if you can't make it smoothly flow to every place where it is needed.
Just as engine needs to be lubricated with the best synthetic oils to work properly and smoothly, 
also every business system also needs to be lubricated the same way in order 
to serve as relieable cash cows.  

When something is changed in a database, we want that change to timely propagate from the database into all systems
that depend on that data. Minimizing organization's systems to communicate with each
other with minimal delays is good for almost any business, regardless of 
which business we talk - banking, finance, insurcance, just name it!

## The problem

In our imaginary use case, let me talk about a laundry service corporation that
operates a huge cluster of laundry machines. To be able to serve their customers
well, the laundry machines need to be monitored. Let's say that each laundry
machine generates a continuous flow of log data, reporting their current
temperature, load, program and power consumption. We store all the data into 
a huge database. Every time when a log entry is written to the database, 
we want it immediately to propagate to the browser based user interface that
the the personnel monitors. We want the data flow to be it event-based. 
Each client node of the data flow system should not be doing expensive continuous short polling to 
its server part. Rather, the event should be pushing the changes to the client part.

So what to do?

## The solution

Clearly, there are at least two interface sections where data needs to jump
from one system to another system. Closest to the human user is browser based UI
that retrieves data from the server endpoint. And then the server endpoint needs to retrieve
data from the database upon change. So there are two intersections that need an 
event driven data transfer solution! Let's look at both of these!

### Web sockets for the user interfaces

To implement an even driven architecture in the UI, we need to be writing
a browser-based user interface application that *listens* for the data changes provided
by its web server endpoint. Therefore the API for the UI cannot be a classic REST API, 
since REST API supports only query-response based scenarios. When you are implement an API that
serves the UI, we are talking about a *backend-for-frontend*, or **BFF**. This API
can't be a universal or generic API, it must not be designed to potentially serve
different kinds of clients. It's a dedicated single purpose API to serve the one and only UI - 
its endpoints are optimized for the particular UI. When you want it to behave in an event-driven
fashion and immediately to push a laundry log entry to the UI once it enters to the endpoint 
from the database, web sockets are the best way implement it.

### Use Kafka and web sockets to write two-way event driven server endpoint!

Now let's write am eventd riven backend endpoint that is event driven in both ways - 
it gets the data event in an event driven way from the database - and further on, it then pushes
the data to the UI as an event as well. Two-fold event-driven. Let's write this
magic NodeJS endpoint right now! Let us make it subscribe to Kafka to listen for data events.
When an event enters the endpoint, it just calmly pushes the event further over 
a web socket to the UI:

```typescript
import express, { Express } from 'express';
import * as http from 'http';
import * as WebSocket from 'ws';

const app: Express = express();

const socketServer = http.createServer(app);
const wss = new WebSocket.Server({ server: socketServer });

wss.on('connection', (ws: WebSocket) => {
  // connection is up, let's make the web socket to listen for Kafka events
  LaundryEventKafkaConsumer.addEventListener((data) => {
    console.log("got a log event from laundry service", data);
    ws.send(data.value);
  })
});

socketServer.listen(process.env.SOCKET_PORT, () => {
  console.log(`Server started on port ${process.env.SOCKET_PORT} :)`);
});

```

Quite mind-blowing how little code we needed for this, isn't it? 
Alright folks, stay tuned, next time I will reveal to you how I implemented my secretive
*LaundryEventKafkaConsumer* that listens to Kafka service that delivers
laundry machine log entries from the deep databases of our mighty
Laundry service corporation!
