---
layout: post
title:  "URL-based application states on Metamatic"
date:   2018-10-28 20:03:00 +0300
categories: metamatic
---

A very typical situation in JavaScript based web apps is that they use client-side urls, 
where application's specific sub-urls are handled by the JavaScript client in the way
that ReactJS router does.

For example there is a use case that your application provides the user with a possibility to 
select some option and when the user makes their choice, the JavaScript app should then load
the relevant data snippet into browser's memory over Ajax request.

That kind of use case would be for instance a situation that the user is asked to [select a car model](https://metamatic-car-app.herokuapp.com/cars)
from a list. In the example behind the link, the JavaScript application loads a details data sinppet
for the selected car upon click and then redirects to [another view that renders the loaded data]()
