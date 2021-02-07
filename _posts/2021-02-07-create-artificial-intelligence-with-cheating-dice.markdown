---
layout: post
title:  "Create Artificial Intelligence With Cheating Dice"
date:   2021-02-07 10:22 +0200
categories: metamatic
---

Can you find these days *any* software developer
who hasn't implemented some sort of [Pac-Man](https://en.wikipedia.org/wiki/Pac-Man) game and then entered
the eternal loop of tweaking and improving the logic for the "pac-mouse" or "pac-monster"
to find his way through the maze?

**Hardly!**

And it is fantastic! Why? Because the scenario brings us to the very core
of thinking about what on earth this much-touted thing really is, in its purest
essence, this thing called AI (**A**rtificial **I**ntelligence).

## How You Got Lost In The Maze

Maybe you are one of those sad people who got stuck with all those tutorials
explaining all sorts of AI concepts, got tricked by them. You gradually degraded 
even deeper into the rat wheel, down to the point where you found yourself browsing
Wikipedia articles about established core concepts of AI,
such as deep neural networks, Q-learning and reinforcement learning,
Markov chains and so much more!

... And then, you stumbled with equations, got lost in the maze of
your thoughts, found yourself wondering how all these things can 
help your Pac-Man mouse find a way out of *his maze*.

Don't be ashamed. Do not be pressed down by sorrow and desperation. 
I got lost the very same way as you did. But I found the way out; 
there is light at the end of the tunnel. There is a path
for your Pac-Man mouse to find his way out of the maze!

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

We can compare a plant with ideas or thoughs. If an idea is  healthy, it survives and produces
offspring - some fruitful action and even better ideas. We humans, considering ourselves intelligent beings, don't actually
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

If you get hungry and just grab a chocolate bar from the grocery and *then run*, 
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

However, the task of going through all steps would be just too enormous for a brute-force search algorithm, but 
very doable for a *Q-learning like* approach. And not only for the mouse in the maze,
also for soccer coach when...

### Building A Soccer Team

A coach wants to build up a "dream-team" by starting from zero and 
expanding his team player by player. After adding each player, he stops
to think who would be the best addition for the currently existing
selection of players. Mathematically, adding those who have the best scores
would create the dream team that always wins. But in reality,
Theo plays miserably when Tim is in the team (maybe because Tim married his ex-girlfriend?) 
and Jack plays way better with his identical twin Joe and so on. For this reason, to find the best team you would
might want to occasionally try to add someone to the team that is
not the best option measured by plain score. There might be hidden
synergies!

## Cheating Dice To The Rescue

At this point I want to introduce you to a super simple
algorithm that I call **cheating dice** - The cheating dice are special dice fiddled 
to produce biased outcomes.

With cheating dice, you might actually most likely get - but not always - number six, 
and less likely number 5, and most unlikely number 1. Other configurations are possible
when fiddled differently, for example having the results biased toward 1. 

It's great for winning in board games, but it's  also *a fundamental principle for any intelligent behavior:*

Whatever you do, you'll be most likely to get the best *known* results by routinely choosing the best steps
you know from your experience and education to be the best ones... 

But sometimes you have to try something else to find even better ways. That applies to everything, beginning from choosing
the spice and ingredients for your soup to a mouse finding a path out of the maze, 
to everything that exists under the Sun - and maybe even beyond!

Anyway, I find this following algorithm handy yet easy-to-grasp thing 
to serve as a core component of an AI solution, which is about mostly selecting the nearly best option
but occasionally trying out a sub-optimal solution.

### Applying Onion Random to Mimic Cheating Dice

Let's implement something now that allows us to create the "cheating dice" effect.
I will call the following implementation "onion random" algorithm,
because it justs creates a random number within given range, and then randomly splits
the number into smaller numbers. Finally we get an outcome that is a random number
biased toward zero end of the range.

Since I like Ruby, I show my implementation in Ruby language:

{% highlight ruby %}
def onion_random(max_number_limit, bias_factor)
  bias_factor.times.reduce(max_number_limit) { |split_random|
    rand(split_random)
  }
end
{% endhighlight %}

What we get here is a fundamental function for artificial intelligence. It's a programmatic 
implementation of biased dice. With this core AI function, my friend, you'll get far in your life!

This function returns you a random integer number that is always smaller than 
the **max_number_limit** that you give as a parameter. The other parameter,
**bias_factor** is also an integer, telling how many times you randomly
reduce the max number. The bigger cheat_factor is, the more likely the function is to produce
outcomes biased toward zero. But no matter how high the cheat_factor is, the
change will always still exist that the highest possible nubmer, which is max_number_limit - 1.

for example:
{% highlight ruby %}
result = onion_random(100, 4)
{% endhighlight %}

You will get an outcome that is quite often zero or close to zero, but you may still
occasionally get even 99.

Needless to say, you are getting quite sexy distribution curves with cheating dice. 

Your mouse in his maze will absolutely love this. 

### Better Implementation With Easing Functions

Now, afer hyping up this super cool onion random algorithm, I must admit that
I am a bit scared that all mathematicians reading this will totally laugh at it.
A smoother solution than nesting random function calls would be just taking 
one random between 0 and 1 and pass it to a basic [mathematical function](https://en.wikipedia.org/wiki/Function_of_a_real_variable). 

I'll be back to this and even more exciting topics. Cheers!
