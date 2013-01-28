---
layout: post
title: "Random numbers in MATLAB"
description: ""
tagline: "Issues with random numbers in MATLAB and how to overcome them"
category: articles
tags: [article, random numbers, MATLAB, gotchas]
---
{% include JB/setup %}

*(Mandatory references for articles about randomness: [[1](http://dilbert.com/strips/comic/2001-10-25/)] [[2](http://xkcd.com/221/)])*

## The problem

Every time we start MATLAB and run

```matlab
>> rand

ans =

    0.8147
```

we’ll get the same result. We close it, start again, it’s

```matlab
>> %-- new session --%
>> rand

ans =

    0.8147
```

Same is true for `randn` and `randi`:

```matlab
>> randn

ans =

    0.5377
```


Why is that? By default, the random number generator (rng) in MATLAB is seeded with a constant (it’s `5489` or `0` depending on context for reasons unknown).

When doing simulations, however, we might want to change this as not to get the same outcome of our experiments each and every time. (Unless, of course, that outcome is a fixpoint in our whole calculation.)

### The correct way

If you’re just looking for a how-to:

```matlab
rng('shuffle')
```

done!

If this doesn’t work or you want to have more information about what’s going on, please read on.

### The wrong way

Many online tutorials present the following lines of code as solution to the problem:

<span class="label label-important">Bad – don’t use this anymore</span>

```matlab
rand('seed', sum(100*clock));
randn('seed', sum(100*clock));
```

These lines seem reasonably intuitive, containing the name `'seed'`. `clock` returns a vector with current time and date which we also multiply with 100 in order to get a new number every 1/100 of a second.

This, however, does not really do what we expect from it. While the snippet seeds the rng with the current time (which is what we wanted it to do) it also changes the *algorithm* of the random number generator (which we probably did not want). `'seed'` in this case represents the choice of the algorithm and it’s the most basic one MATLAB has to offer.

<small><span class="label label-info">Info</span> `'seed'` represents the default for MATLAB v4 and Wikipedia says this one came out in 1992. In 1996 v5 changed the default rng to `'state'` which is outdated as well. `'twister'` would be the ‘modern’ one to use in that syntax.</small>


## Explanation

### Since 2011a

MATLAB 2011a comes with a handy `rng` function which should be used to initialise the random number generator without resetting the algorithm.

```matlab
>> rng('shuffle')
>> rng

ans = 

     Type: 'twister'
     Seed: 411462746
    State: [625x1 uint32]
```

In order to initialise the rng with the specific seed `12345`.

```matlab
>> rng(12345)
>> rng

ans = 

     Type: 'twister'
     Seed: 12345
     State: [625x1 uint32]
```

In case we *do* want to change the algorithm being used, we need to specify it explicitly:

```matlab
>> rng('shuffle', 'v5uniform') % instead of 'shuffle' we could also use an integer seed
>> rng

ans = 

     Type: 'v5uniform'
     Seed: 411487724
    State: [35x1 double]
```

If we want to reset the state to MATLAB’s startup value, it’s as simple as:

```matlab
>> rng('default')
>> rng

ans = 

     Type: 'twister'
     Seed: 0
     State: [625x1 uint32]

>> rand

ans =

    0.8147
```

<small><span class="label label-info">Info</span> Note that while it shows the seed as `0`, we’ll get the same random numbers as if we used `5489` as a seed. Both `0` and `'default'` seem to be hardcoded to use that number internally.</small>


### Older versions

Until MATLAB version 2010b, one should use the more verbose

```matlab
s = RandStream('mt19937ar', 'Seed', 'shuffle'); % if 'shuffle' does not work, use the old sum(100*clock)
RandStream.setDefaultStream(s); % changed to setGlobalStream in later versions
```

This sets the global rng used by `rand`, `randn` and `randi`.

This technique is still available in later versions and allows for more control over random number generation that the simple `rng` function. Using `RandStream` it is possible to create several streams at the same time which can be used independently from each other. This could be used for random number generation in submodules where the global random number generator should not affected (perhaps to guarantee reproducability) or in oder to use a different algorithm in some parts of the program.

## When to use a fixed seed?

Fixing the seed may be useful in *some* unit tests where we like to get reproducable results and not want to have our test code failing just because of some more or less pathological choice of random numbers. But of course, it depends on the application: In many cases we actually *do* want to catch those pathological numbers and have our test code succeed no matter what the random numbers are.

In any case it is very good practice to actually log the seed before running tests or simulations (and the rng algorithm).

## More information

[MathWorks: Updating Your Random Number Generator Syntax](http://www.mathworks.de/help/matlab/math/updating-your-random-number-generator-syntax.html)

[Walking Randomly » Randomness in MATLAB](http://www.walkingrandomly.com/?p=2945)

