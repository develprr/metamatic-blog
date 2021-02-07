---
layout: post
title:  "Create Your AI With Onion Random"
date:   2021-02-07 07:44 +0200
categories: metamatic
---

Can you find these days *any* software developer
who hasn't implemented some sort of [Pac-Man](https://en.wikipedia.org/wiki/Pac-Man) game and then entered
the eternal loop of tweaking and improving the logic for the "pac-mouse" or "pac-monster"
to find his way through the maze?

**Hardly!**

And it is fantastic! Why? Because the scenario brings us to the very core
of thinking about what on earth this much-touted thing really is, in its purest
essence, this thing called *artificial intelligence*.

## How You Got Lost In The Maze

Maybe you are one of those sad people who got addicted to all those
shady online videos. You watched some Youtube tutorials
explaining all sorts of AI concepts, got tricked by them. You gradually degraded 
even deeper into the rat wheel, down to the point where you found yourself browsing
Wikipedia articles about established core concepts of AI,
such as deep neural networks, Q-learning and reinforcement learning,
Markov chains and so much more!

... And then, you stumbled with equations, got lost in the maze of
your thoughts, found yourself wondering how all these things can 
help your Pacman monster find a way out of *his maze*.

Don't be ashamed. Do not be pressed down by sorrow and desperation. 
I got lost the very same way as you did. But I found the way out; 
there is light at the end of the tunnel. There is a path
for your Pac-Man to find his way out of the maze!

## The Core Essence Of Intelligence 

At the end of the day, isn't all intelligence made out of the same matter? It's
about the will to find a better way to achieve something valuable, 
and it's about the curiosity to try alternative paths to find a 
better way. All animals are inherently smart. They exercise this same behavior. 
They mostly repeat the same pattern of doing these things they need to do,
to survice and reproduce. 

But they are unpredictable. They do unexpected things.
Sometimes their foolish play causes them trouble, but sometimes 
they really find something different that helps them adapt and survive.
That's why there are city foxes tattering garbage bins - hardly any ancient
behavior. 

The animals, they *explore*. 

### Physical Intelligence

Now, there are many levels of exploration. The most primitive level is to
really physically do things, both genial and crazy, common in the nature. 
If a plant seed *randomly* ends up on a road by the quirk of a whirlwind, 
it dies and doesn't reproduce. But if it lands in the fertile soil, 
however, its offspring will be numerous. 

That is physical intelligence.

### Neural Intelligence

We can compare a plant with ideas or thoughts. If they are healthy, they survive and produce
offspring. We humans, considering ourselves intelligent beings, don't actually
need to physically do a stupid thing to live out its consequences. We can sit and *think*
what would happen if "I... ehm... just snatch a chocolate bar from grocery to fill my stomach... Nope."

Of course there are some exceptions!

### The Dawn Of Computer Intelligence 

Similarly to humans, computers can evaluate "ideas" and pick the best
ones. Back in 1997, world chess champion [Garry Gasparov was beaten by IBM
supercomputer Deep Blue](https://en.wikipedia.org/wiki/Deep_Blue_versus_Garry_Kasparov).
The chess program used a "brute-force" search algorithm to find the best moves.

Later it turned out, brute force search does not take AI really far,
since the possible combinations typically grow exponentially by every step.
Therefore, it became obvious that "cheaper" search algorithms
were to be invented. 

### Sparing Search Algorithms

Better search algorithms were invented, such as [Q-learing algorithm](https://en.wikipedia.org/wiki/Q-learning).
The core idea is that instead of totally evaluating all possible combinations,
each evaluation of the path *usually* chooses the best next step,
but not always! It *explores*. That means, it occasionally chooses
a next step that isn't the obviously the best one. This is because in a computer maze,
just as in the life in general, the obviously most rewarding next step
often isn't the best path in the long term!

If you get hungry and just grab chocolate bar from the grocery and *then run*, 
you'll be doing fine for five to twenty minutes, but not so much anymore when the police car
stops in front of you when you sit in the park with your hands and face smeared
brown from the chocolate you just ate.

## Back To The Maze

But now let's get real and go back to our original problem of helping your Pac-Man mouse navigate
through the maze... The point here is that if your mouse has a "GPS", 
which equals a reward function to tell how far he is from the salvation 
measured as a linear distance from maze's exit door, then actually a step
further away from the exit door may be the right solution when 
consequent steps are iterated, although it does not appear to be so right away. 

However, the task of going through all steps would be just too enorm for a brute-force search algorithm, but 
very doable for a *Q-learning like* approach. And not only for the mouse in the maze,
also for any soccer coach...

### Building A Soccer Team

A Coach wants to build up a "dream-team" by starting from zero and 
expanding his team player by player, after adding each player stopping
to think who would be the best addition for the currently existing
selection of players. Mathematically, adding those who have the best scores
would create the dream team that always wins. But in reality,
Theo does not play well when Tim is in the team and Jack plays better
with his twin Joe and so on. For this reason, to find the best team you would
might want to occasionally try to add someone to the team that is
not the best option measured by plain score. There might be hidden
synergies!

## Onion Random To The Rescue

At this point I want to introduce you to a super simple
algorithm that I call **onion random** - I really don't know 
if there is a better name for it, but I came up with this name because it somehow
both sounds cool and actually describes it quite accurately. 

Anyway, I find it really handy yet easy-to-grasp thing to serve as a core component of an AI solution,
which is about mostly selecting the nearly best option
but occasionally trying out a sub-optimal solution.

Since I am currently in love with Ruby, I show my implementation in Ruby language:

{% highlight ruby %}
def onion_random(max_random_integer, amount_of_onion_layers)
  amount_of_onion_layers.times.reduce(max_random_integer) { |reduced_random_integer, iteration|
    rand(reduced_random_integer)
  }.to_i
end
{% endhighlight %}

With this core AI function, my friend, you'll get far in your life!

This function returns you a random integer number that is always smaller than 
the **max_random_integer** that you give as a parameter. The other parameter,
**amount_of_onion_layers** is also an integer, telling how many times you randomly
reduce the max number. The result of the function is a random number that is more
often close to zero but can be also near the max value you gave, but 
rather rarely. You'll get quite sexy distribution curves with this one. The bigger value
you give as **amount_of_onion_layers**, the steeper the curve will be toward zero.

Your mouse in his maze will absolutely love this. 

I'll be soon back to this and maybe even more exciting topics. Cheers!
