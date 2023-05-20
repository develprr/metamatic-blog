---
layout: post
title: Event Driven User Interfaces With React and Web Sockets
date:   2023-05-20 16.11.00 +0300
categories: Metamatic Systems
---

Do you want to implement a smooth browser based user interface that can deliver
your critical business data to your UI with a minimal delay? There are
countless use cases - perhaps you want to show stock market data
real time for your users. Or maybe you want to implement a display 
to show when the next train arrives. Or you have a news portal that
must propagate the news updates immediately to user's display when they become
available. 

# The bad old polling solution

The simple, old (and bad) solution would be to implement a UI that just executes
repetitive queries to the server endpoint to find whether there is something
new available. This approach is called [polling](https://en.wikipedia.org/wiki/Polling_(computer_science)).
It is a very commonly used approach. And it is a very bad solution because
you must set an interval for your queries to the server. This means
that there will always be an awkward delay between when new data becomes
available at the server and when it actually shows up in the UI. 

If you set the polling interval to one minute, you will
curse your users to get the data up to one minute later than they could.
If you set the interval to one second, then the delay is smaller but your 
UI client will be making so many queries that your server will be choking
under the load - you won't need many more than a couple of online users to
jam the entire server. Of course you can cope with this awkward situation by increasing capacity
by using [Lambda](https://aws.amazon.com/lambda/) endpoints - but you will
end up to pay a lot of money because of your unwisely implemented UI.

# The good new solution

The optimal way to solve the issue is to write an event driven UI - the UI
does not need to be polling the server manically for new data - you can use
[web sockets](https://developer.mozilla.org/en-US/docs/Web/API/WebSockets_API)
and make the UI just idly listen for server side changes - the 
server endpoint will push the data changes to the UI when they become available.
That's the right solution!

Last time I wrote how to implement an [event driven backend](https://www.metamatic.net/metamatic/systems/2023/05/19/how-to-create-event-driven-data-flow-with-websockets-and-kafka.html) that retrieves
data from [Kafka](https://kafka.apache.org/) service and pushes the data to the UI over a web socket.

Understandably, such solution is of no use if you don't have the actual UI
to receive the data events from the backend endpoint.

So let us take that missing step and implement a modern standalone [single page
application](https://en.wikipedia.org/wiki/Single-page_application) (SPA) 
with [React](https://react.dev/) library and [TypeScript](https://www.typescriptlang.org/)
programming language.  

To get started with React, I recommend you to use [create-react-app](https://create-react-app.dev/) NPM library
to initialize your React project. 

And finally, have a look at my UI-side web socket implementation for our
imaginary Laudry Service corporation - you will be getting your laundry machine
events to the UI in no time, I swear it!

```typescript
import React, { useState, useCallback, useEffect } from 'react';
import useWebSocket, { ReadyState } from 'react-use-websocket';
import './App.css';

const WS_URL = 'ws://localhost:3002';

export const App = () => {
  const [message, setMessage] = useState("Laundy Service UI");
  useWebSocket(WS_URL, {
    onOpen: () => {
      console.log('WebSocket connection established.');
    },
    onMessage: (message) => {
      setMessage(message.data);
    }
  });

  return (
    <div>{message}</div>
  );

}
```

Not at all too difficult, do you agree? :) If you are curious enough
to download my full Laundry UI, checkout the [GitHub repo](https://github.com/develprr/laundry-ui)!
