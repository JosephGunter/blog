---
layout: post
title: Solving a prisoner escape riddle
categories: python
date:   2012-10-15 23:25:51
---
I recently heard this riddle from a friend at a campfire. I wasn't smart enough
to come up with the answer, but when it was explained to me I wanted to know
how long it would take the prisoners to be released. In this post I'll describe
the riddle and the answer, and show how to use Python to set up an experiment,
use a Monte Carlo simulator to run the experiment repeatedly, and briefly discuss
some of the results.

The Riddle
==========

There are 50 prisoners, and one prison keeper. The prisoners will be placed in
separate cells, never able to communicate. Once per day, the prison keeper will
randomly select one prisoner and take him into a room with two levers. The levers
have two positions each: Up, and down. The prisoner may operate the levers however
he wishes, then he must return to his cell. (The levers don't do anything; they
just represent a binary state.) The prisoners have one chance to indicate to the
prison keeper whether all the prisoners have been in the lever-room. If they so
indicate too early, they will never be released. If they're correct (even if
they're late), they will be released immediately. The prisoners get one chance
to meet prior to imprisonment, to discuss strategy. How can they ensure their
release?

The Answer
========

Only one lever is needed. Elect one prisoner to be a 'counter.' When a prisoner
enters the lever room and the lever is in the 'down' position and he has never
moved the lever, then and only then will he move the lever to the 'up' position.
Only the 'counter' prisoner may return the lever to the 'down' position, and each
time he does so he increments a counter. When the counter reaches 50, he knows
that all prisoners have been into the lever room at least once. He can then inform
the prison guard, and all the prisoners will be released.

A Solution in Python
===================

I came up with the following model in Python to describe the prison scenario.
There are probably better and more optimized way to represent this situation, but
here's the first one I came up with.

{% highlight python %}
#Iteration 1: The basic scenario
import random
day_number = 0 
prisoner_designee = 1 # chosen by prisoners to be the official counter
counter = 0 # as tracked by the prisoner_designee
lever_position = 'down' # starting position of the lever
prisoners_who_have_operated_lever = []

while counter < 50: 
  prisoner_number = random.randint(1,50)    # Select a prisoner
  if not prisoner_number in prisoners_who_have_operated_lever:
    if lever_position == 'down':
      prisoners_who_have_operated_lever.append(prisoner_number)
      lever_position = 'up'
  if prisoner_number == prisoner_designee:  # If it's the designee...
    if lever_position == 'up':              # ...and the lever is up...
        lever_position = 'down'             # ...put the lever back down...
        counter += 1                        # ...and increment the counter.
  day_number += 1

print day_number
{% endhighlight %}

The above code, iteration 1, uses 'random.randint' to generate a random integer.
I used `if` statements to describe the logic, and printed out the result. Example:
{% highlight python %}
$ python prisoner1.py 
2536
{% endhighlight %}

This means that for this particular run, it would have taken the prisoners (or
rather, the prisoners' designated counter prisoner_designee) 2,536 days to count
50 'up' levers, proving that all prisoners had entered the lever room and
ensuring their release. That's just under seven years(!).

But this result depends on the order in which the prison keeper (in our case
represented by random.randint) chose the prisoners. Running this code at the
command line produces different results each time, usually in the range of
2500--3000 (days). To get a better picture of how long it would take a new
group of prisoners to earn their release we'd have to simulate the whole range
of possible outputs (or which there are nearly infinitely many) But I don't want
to run this manually dozens of times and tabulate the results by hand; I want
to make the code run the simulation thousands of times, with random inputs each
time, and then aggregate the results. That's the 
<a href="http://en.wikipedia.org/wiki/Monte_Carlo_method">Monte Carlo method</a>.

Implementing the Monte Carlo Method
================================

We're going to use a loop to run the experiment, so first, wrap the experiment
in a function:

{% highlight python %}
#Iteration 2: Wrapping the simulation in a function
import random

def run_simulation():
day_number = 0 
prisoner_designee = 1 # chosen by prisoners to be the official counter
counter = 0 # as tracked by the prisoner_designee
lever_position = 'down' # starting position of the lever
prisoners_who_have_operated_lever = []

while counter < 50: 
prisoner_number = random.randint(1,50)    # Select a prisoner
if not prisoner_number in prisoners_who_have_operated_lever:
if lever_position == 'down':
prisoners_who_have_operated_lever.append(prisoner_number)
lever_position = 'up'
if prisoner_number == prisoner_designee:  # If it's the designee...
if lever_position == 'up':              # ...and the lever is up...
  lever_position = 'down'             # ...put the lever back down...
  counter += 1                        # ...and increment the counter.
day_number += 1
return day_number

print run_simulation()
{% endhighlight %}

Running this code should produce the same type of output (with a different
result, of course):

{% highlight bash %}
$ python prisoner2.py 
2950
{% endhighlight %}

The next step is to write a loop that will call this function a thousand times:
{% highlight python %}
#Iteration 3: Loop it to run 1000 times
import random

def run_simulation():
counter = 0 
day_number = 0 
prisoner_chief = 1 
lever_position = 'down'
prisoners_who_have_operated_lever = []

while counter < 50: 
prisoner_number = random.randint(1,50) # Select a prisoner
if not prisoner_number in prisoners_who_have_operated_lever:
if lever_position == 'down':
prisoners_who_have_operated_lever.append(prisoner_number)
lever_position = 'up'
if prisoner_number == prisoner_chief:
if lever_position == 'up':
  lever_position = 'down'
  counter += 1
day_number += 1
return day_number 

simulation_results = []
simulations = 0 
while simulations < 1000:
simulation_results.append(run_simulation())
simulations += 1
print simulation_results
{% endhighlight %}

In this iteration we use a `while` loop to run the simulation 1000 times, storing
the results in the list called simulation_results. Here's an example of the output:
{% highlight bash %}
$ python prisoner3.py 
[2444, 2119, 2818, 2253, 2586, 2543, 2490, 3388, 2034, 2739, 2554, 2585, 2498, 2689, 3180, 2760, 3145, 2698, 2196, 2769, 2400, 2783, 3091, 2258, 2952, 1730, 2974, 2656, 3059, 2000, 3222, 2186, 3114, 2618, .... ]
{% endhighlight %}
(List truncated.)
The last step in the Monte Carlo method is to aggregate and interpret the results.
In this case, that means taking the average of all the simulation runs. A simple
way to do this with lists is to sum the list:
`average = sum(list)/len(list)`
A more elegant way is to use Numpy's built-in `mean` function, which requires
first converting the `list` to a Numpy `array`:

{% highlight python %}
import numpy
array = numpy.array(list)
average = numpy.mean(array)
{% endhighlight %}

The next iteration of the code will include this small refactorization.
Running this code produces the following result:

{% highlight bash %}
$ python prisoner3.py 
2720.895
{% endhighlight %}

Now, instead of one experiment, this result represents the average of 1000
experiments.

So what?
========

So on average, it would take 50 prisoners in this scenario 2721 days to earn
their release. Cool. Neat. But this raises so many more questions: Is this number
related to the probability of 1/50)x(1/50)? Where is this average value in the
range of values yielded from the 1000 results? If we halve the number of prisoners,
will the number of days go down by half? What does a histogram of the results look
like? The answers, as well as a more in-depth discussion of other statistics,
will be in the next post.
