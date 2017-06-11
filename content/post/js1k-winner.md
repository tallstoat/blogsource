+++
date = "2010-09-28T15:40:24+05:30"
title = "JS1K Winner"
categories = ["code"]
tags = ["js"] 
+++
The JS1K winner has been announced. The entry is described in detail in a post by the programmer.

<!--more-->

This post desribes in detail the techniques used to whittle the size down. I found some of these interesting as well as amusing at times. Here is an excerpt :-

```
So how does the game know where the coins are, if it is not explicitly keeping state for them? Every block whose random height is divisible by 6 gets a coin, and when the player collects the coin, .1 is subtracted from the height, and the coin no longer shows up.
```
And here's another one which abbreviates long canvas context method names :-

```
At the start of the program there is a for/in loop that goes over the properties of the canvas context, and adds a new property, with a shorter name, for each of them. It took some experimenting to find an abbreviation algorithm that doesn't have clashes on any of the methods we use---I ended up using the first letter of the name plus the 7th letter, if any. So lineTo becomes l, and quadraticCurveTo becomes qt.
```
There are other gems in there. So, if packing more functionality into less code is your thing; have a gander.

The post is available [here](http://marijnhaverbeke.nl/js1k/). Enjoy!



