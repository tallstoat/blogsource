+++
date = "2010-11-25T23:15:23+05:30"
title = "Picking a code editor"
categories = ["code"]
tags = ["code", "vi", "editor"]
+++
If you are a programmer, it would'nt be an exaggeration to say that at least half of your life is spent within an editor. You are in your element within the editor. It's the programmer's domain where he/she creates something out of nothing, in a manner of speaking. Given this, it is really really important for a programmer to be the master of the editor he/she works with on a daily basis.
<!--more-->

Despite editors being such indispensable tools for the art of progamming, there is not a lot of material out there which helps a programmer in choosing one; specially when the programmer is a novice and just starting out. More often than not, the programmer concentrates on the language he/she is going to be working with, googles for the __industry leading editor__ in that space, and picks the one at the top of the search results. 

If it's Java, the choice tends to be Eclipse or IntelliJ (if the employer is willing to foot the bill). If it's one of the M$ languages, the choice tends to be Visual Studio and so on. In effect, the programmer chooses an IDE (Integrated Development Environment) when the goal __should be__ to choose an editor. 

Most IDEs undoubtedly tend to be great at supporting templatized language features, build tool integration, refactoring, code completion etc. However, in my humble opinion, the choice of an editor by a programmer should be based primarily on the following basic criteria & not solely on all the bells and whistles provided by an IDE :-

* Support for code navigation (within the opened file)
* Support for editing code quickly with the least amount of keystrokes
* Support for creating one's own macros and commands

Most IDEs when it comes to the actual __code editing__ tend to be __clones of notepad__, others _assume_ that you would use a pointing device (that mousey thing) and yet others provide their own language of navigation. The problem with all this is that depending upon the IDE you have to wrestle with (on a given day), you have to either keep clicking with the mouse, train your muscles to learn the shortcuts all over again or in the case of the uninitiated keep tapping the arrow keys! 

Another and need I say, better option is to enable the _Vi_ plugin/mode (which exists for most of these IDEs) & all your built-up muscle memory can be put to good use.

So, now let's see how the code navigation language of __Vi__ (or its new avatar, __Vim__) works.

## Code navigation with Vi

Before we start, you need to be aware of the different modes of Vi. In brief, there are three.

* Command mode
* Insert mode
* Last line mode

There are various articles explaining how to go from one mode to the other. For the purposes of this article, you need to be in __Command mode__ (most of the time).

OK. So, I'll now explain with examples, what I mean by code navigation & learning a language for it. Let's first start with navigation within a line.

```
☛The quick brown fox jumps over the lazy dog
```

Look at the text above. Your cursor is at the start of the line (marked by ☛) and you want to go to the end of the line. Make sure you are in __Command mode__.

How do you do that today? Do you remember the IDE shortcuts (which change across operating systems)? Do you use a mouse? Do you use the arrow keys?

Well, in vi you would just press the __$__ key and...

```
The quick brown fox jumps over the lazy dog☚
```

Changed your mind? Want to go back to the start of the line? just press the __0__ key and...

```
☛The quick brown fox jumps over the lazy dog
```

... you're back where you started!

Well, that was too easy. Now, let's say you want to go to the start of the word __jumps__. In vi, you can get there in multiple ways. 

But before we go over that, you need to first understand which navigation technique to use when. That is really critical to get good at navigation. It involves taking in the whole line __visually__ and identifying what is in a way __unique__ about where you want to navigate to i.e. your cursor's next destination.

If you look at our example line, you can see that our intended cursor destination is the first and only occurence of the letter __j__ on this line. Once you realize this, a handy vi navigation directive can be used to get there. It's called __f__ which is short for __forward__. If you press __f__ followed by __j__ (which is the intended character to go to)...

```
The quick brown fox ☛jumps over the lazy dog
```

...you are there at the start of __jumps__!  

What if you were at the end of the line and wanted to get to the start of __jumps__? Easy. Use the __F__ directive (Notice the uppercase) which helps you go to characters backward. Press __F__ followed by __j__ (which is the intended character to go to) and you are there! Wasn't that cool?

What if you just feel a bit trigger-happy and want to __advance word by word__ to __jumps__? Well, just use the __w__ key; short for __moving forward word by word__. 

What if you are at the end of the line and want to __move backward word by word__? Use the __b__ key; short for 'moving backward word by word'. 

And if you want to move forward word by word but want the cursor to move to the end of the word? Use the __e__ key. Simple! 

At another level, you might have also realized that the word __jumps__ is the first and only occurence of this word on this line. That means that you can just jump to the __Last line mode__ by pressing __/__ and then typing the pattern which you want to search for and then press Enter key. In this case, it would be __/jumps__ and then Enter; and you are there!

So, that's multiple different ways to get there and all of them more efficient than what you would be used to if you havent used vi before. 

This is just a glimpse of what is possible with vi. This was just finding your way around a line of text. Vi has multiple other mechanisms to find your way quickly around paragraphs of text & code. 

I hope this article has tingled your curiosity enough to go check out vi in detail. Trust me if you are a developer and you don't know vi's language of navigation; you are missing something. 

Happy vi to you! :wq!
