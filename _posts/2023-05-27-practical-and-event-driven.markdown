---
layout: post
title: Practical and Event Driven
date:   2023-05-17 05.38.00 +0300
categories: Metamatic Systems
---

What is the greatest benefit of simple solutions? Well, literally, it's
their simplicity!

## Stupid is as stupid does

Now this thing I just wrote may also sound the stupidest thing you possibly heard today -
just because, well, actually, why? Because it is obvious, self-evident
and simple!

So let me add another principle to the mix. To me it comes as self-evident that the 
most obvious solution is usually the best solution, and as a bonus 
it's also usually the simplest solution. How about you?

## Thinking too big

Yet this is also the reason why start-ups still have a great chance
to survive and become successful in the footsteps of already established
tech giants - namely, it is too easy to forget that the simplest solution
is usually the best one - if you design your software complicated and "scalable"
too early, meaning that you try to implement a *big* and *scalable* architecture
already at the point when you have about zero customers,

you are probably thinking *too big*. Every solution should be goal oriented
and solve the problems and challenges that you have right now. For
example, typically you should not try to implement "localization support" to translate
your app into all possible languages when you don't have a single customer etc.
It's almost always the best option to find a practical solution to a practical
problem that you have right now.

## Software is like clay

It is a common misconception that you can't modify your software later 
when real scalability requirements emerge - people who are not fluent
coders tend to think that software is similar to physical items - 
by design, you can't just refactor an existing tractor to a main battle tank.
This physical world thinking is quite often erratically mirrored into software business.

But in reality, software is like clay - you can just endlessly keep modifying and reforming it
to match the changed requirements (if you can code, of course). There is no
need to throw spent millions out of the window and start everything *from scratch*.
You could - and most often, *should* keep modifying your product - especially if it's already
making some real money - instead of possibly going to point zero all over again.
 
Okay, now, this was the prelude. So let me get practical and describe the
simplest possible solution to implement an event-driven application.

## Going event driven easy and cheap

Now, let's say that we need an event driven web application. Every time there's 
change in database, the change should immediately be reflected to the UI.

To go event-driven you just need a simple JavaScript
vanilla code snippet and basic understanding what JavaScript's readily available
*EventSource* object can do for you.

So let's naively implement the JavaScript based UI code to read events that the server will be pushing:

```javascript
 
const handleServerEvent = () => {
  const sseSource = new EventSource("http://127.0.0.1:8080/my-event-driven-endpoint");
 
  sseSource.onmessage = function (event) {
	const dataElement = document.getElementById("data"); // a DOM element in the UI.
	const { content } = JSON.parse(event.data);
	dataElement.textContent = content;
  };
  const closeStream = () => sseSource.close(); 
}	 
```

And now let me show the server endpoint implemented in NodeJS to push the event
to the UI every time there's data available:

And the backend endpoint written for Express server in NodeJS: 

```javascript
app.get('/my-event-driven-endpoint', async (req: Request, res: Response) => { {
    
    // set response headers to enable event driven communication:
    
    res.statusCode = 200;
    res.setHeader("Cache-Control", "no-cache");
    res.setHeader("connection", "keep-alive");
    res.setHeader("Content-Type", "text/event-stream");

    // a mimicry interval, replace this with a database listener in real app:
    setInterval(() => {
      const data = JSON.stringify({ content: dataSource });
      res.write(`id: ${(new Date()).toLocaleTimeString()}\ndata: ${data}\n\n`);
    }, 100);
  } 
};
```

## Moving on from here

The solution above effectively enables event driven communication between your UI client app
and your web server endpoint. To proceed from here to a plain, simple, cheap and
effective working solution, connect your server endpoint to listen to changes a real database,
such as MongoDB (or whatever). Possibly also wrap your client-side updater function
into a React app. And deploy the web server endpoint to the AWS cloud in an EC2 container.

How about the endpoint scalability? You don't need to worry about it until you 
have thousands of customers - you'll be doing fine with a single naive Node web server 
in its happy container for quite some time - go for Lambdas or Kuberneter clusters
only when you really have the customers and huge number of online users.


I'll be back generating this stuff directly out of my biological Human GPT.

Cheers!



