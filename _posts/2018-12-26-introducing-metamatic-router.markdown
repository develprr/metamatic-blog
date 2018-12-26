---
layout: post
title:  "Introducing the Metamatic Router"
date:   2018-12-26 16:48:12 +0300
categories: metamatic
---

The [Metamatic framework](https://www.npmjs.com/package/metamatic) now provides an out-of-the-box embedded router feature. 

When developing JavaScript web apps, there are multiple options available for enabling client-side routing in browser-based UI software. 
When coding with Metamatic and React, you can use many available router libraries to enable routing.

But some libraries require you to wrap your application inside a certain "router provider". That can be understood as an anti-pattern especially
if you use a router library that expects you to add Router as a JSX component in the style of a <SomeRouter> tag that then wraps around the application-related
JSX elements. It can be considered an anti-pattern because you are first supposed to use JSX for defining visual rendering hierarchy of elements 
only and then you mess this clean system by injecting programmatic non-visual elements. 

You end up having a sort of not-really-visual yet not-really-programmatic-either mishmash tag-monster.

Metamatic provides a simplistic out-of-the-box router solution for those that don't like provider tags.

The Metamatic Router was created just because it was a quite easy thing to create this kind of a thing on top of the core Metamatic framework.

## Installing Metamatic Router

There are two options to install the Metamatic Router. The most obvious one is installing the Metamatic framework just like before:

```js
npm i --save metamatic
```

You can also install just the Metamatic Router as a standalone feature without state management features:
 
```js
npm i --save @metamatic.net/metamatic-router
```

## Real Demo of Router in Action

To understand how to harness the Metamatic Router, the most obvious thing to do is to check out the [Metamatic Router Demo](https://github.com/develprr/metamatic-router-demo).
There is also a hosted version of the example showing the built and deployed [router demo running real time here](https://metamatic-demo.herokuapp.com/router).

## Configuring Metamatic Router

In addition to including the Metamatic router into your *package.json* file there is little to do when getting started with the Metamatic Router.
First, Connect your apps's main component to Metmatic Router similarly as done in this [example](https://github.com/develprr/metamatic-router-demo/blob/master/src/App.js).

It requires you to connect the component to router with **connectToRouter** upon mounting:

```js
componentDidMount = () => connectToRouter(this, () => this.setState({}));
```

Secondly, define which components are rendered for which URLs with **matchRoute** function invocations in *render* function:

```js
render = () => (
  <div className="meta-lang">
    {matchRoute('/', <Header/>)}
    {matchRoute('/language', <LanguageView/>)}
    {matchRoute('/vocabulary', <VocabularyView/>)}
    {matchRoute('/exercises', <ExerciseView/>)}
  </div>
) 
```

## Redirecting and Listening for Url Changes

To see a practical example of using Metamatic to redirect to different URL paths inside your web app and reacting to URL changes, check a 
[practical example here](https://github.com/develprr/metamatic-router-demo/blob/master/src/layout/header/NaviBar.js).

The principle is that when using the Metamatic Router, you invoke **redirectTo** function to change your app's URL path: 

```js
redirectTo(someUrlPath);
```

Similarly, if you want to make any component aware of the URL changes invoked by Metamatic *redirectTo* function, use *connectToRouter* again:
 
```js
componentDidMount = () => connectToRouter(this, (path) => this.setState({path}));
```

## Deploying Your Metamatic React App into Custom Folder

When it's time to think about the production (or test environment) deployment and if you want to deploy your Metamatic app into another place than
your domain root **https://yourdomainwhatever.com** for instance https://yourdomainwhatever.com/**your/custom/path** you must remember to configure
the Metamatic Router to use that base path with **configureBaseRoute** function as shown in the [demo app example here](https://github.com/develprr/metamatic-router-demo/blob/master/src/index.js) 
such as:

```js
configureBaseRoute('/your/custom/path');
```

That's all folks!
