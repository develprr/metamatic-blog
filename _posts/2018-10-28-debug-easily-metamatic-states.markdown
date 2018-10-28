---
layout: post
title:  "Debug Easily Metamatic States Inside Browser"
date:   2018-10-28 20:03:00 +0300
categories: metamatic
---

When you implement your application using the Metamatic framework, you will get a robust and clear data management scheme that is 
very easily debuggable and viewable using your browser's default developer tools.

This is possible because Metamatic provides an embedded managed state container that is based on browser's default data storages, 
namely, [local storage and session storage](https://en.wikipedia.org/wiki/Web_storage#Local_and_session_storage).

For example, if you use Chrome browser and your Metamatic app is configured to use local storage and you want to see your current 
Metamatic state containers (or just states, to put it that way), just open the Application tab in your DevTools console and on the left side
select "LocalStorage". That opens all current Metamatic states in a nice list and you can then view each state and attributes within each state
as exapandable nested objects. Very practical indeed! 

Similar tools to view states inside App's localStorage are also provided by all other major browsers, such as Firefox, Safari, IE and many others.

No need to install any buggy and shady third party plugins to view your Metamatic states!

![Debugging Metamatic States]({{ site.baseurl }}/assets/debugging-metamatic-states.png)

Metamatic uses by default browser's Local Storage for persisting state container's states. This is practical because objects serialized
inside LocalStorage will survive page reloads so it is quite advantageous saving from a lot of coding and renewed data loading, enabling
effortless interlinking inside app even using traditional hard links that cause a page reload - if you wish so. But it also provides an advantage
that when the user refreshes the page the app will remember its exact states - which tabs were open, what user had already typed in forms and so on.

But when you want to use some other persistence mechanism than local storage, such as session storage or memeory storage,
just configure Metamatic to do so when you initialize your app:

{% highlight javascript %}
import {useSessionStorage} from 'metamatic'

useSessionStorage();
{% endhighlight %}

Or memory storage:
{% highlight javascript %}
import {useMemoryStorage} from 'metamatic';

useMemoryStorage();
{% endhighlight %}
