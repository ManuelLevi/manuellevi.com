+++
author = "Manuel Levi"
categories = ["python", "rant"]
date = 2022-06-19T15:30:17Z
description = ""
draft = true
slug = "why-i-hate-scientific-notation"
tags = ["python", "rant"]
title = "Why I HATE numpy's scientific notation and how to fix it"
+++


⚠️

This is a RANT. If you're a math nazi, you'll be offended by this. Scientific notation is **rarely** useful. If you understand anything about statistics and the english language, you understand the difference between me saying **rarely useful**, and **never useful**. Are there tons of uses for scientific notation? Sure. Do they represent the majority? Not even close. Is scientific notation overused? Definitely.

When you're working with Python, numpy, and any sort of normalized data, it's very common to get an array like this:

`[1.02425171e-01, 1.46210406e-01, 0.00000000e+00, 3.90839180e-04, 5.85902134e-01]`

Even though I work with numbers **literally every single day (**yes, even on vacation and weekends) I never got used to this, and probably never will.

Here's a table of advantages for scientific notation:

| Advantages | Argument against |
| --- | --- |
| Good for massive numbers | Most times you won't be working with massive numbers - there's different units for that |
| Good for tiny numbers | Most times you won't be working with tiny numbers - there's different units for that |

> "But I can't say the moon is 384,400,000 meters away from the Earth!"

First of all, yes, yes you can. Second of all, you can say 384.400 kilometers.

> But Km's doesn't work with larger numbers!

Use light years, fool.

My point is, if you're working with very large number of very small numbers, you can likely change the units to something a bit more sensible.

A Zero Fetish
-------------

Just look at that fugly "0.00000000e+00". Yes, that is the same as "0.0", or better yet "0".

**0 reads "zero", not "zero point zero zero zero zero zero zero zero zero times ten to the power of zero".**

Why the hell, would someone write "0.00000000e+00" instead of plain and simple "0"? Beats me.

Who does that?! Why would you do that? Why do you hate the world so much? Why do you want to see it burn?

Terrible comparisons
--------------------

Now, take a look at those two numbers: 3.90839180e-04 and 5.85902134e-01. Without reading them again, tell me: Which one is greater, the first or the second one?

Did you answer correctly? You had to look back, didn't you? And perhaps, if you don't work with scientific notation daily, it took you a second or two to realize there was a `-` hidden in there, right between the `e` and the `0`. And **yes**, if you're wondering, **that zero is completely unnecessary too**.

The truth is, those numbers hurt our eyes so much, our brain just wants to skim over them asap, wishing it will all be over soon.

Now look at these two numbers: 0.00039084, 0.58590213. Which one is the larger the number? Or better yet: 0.00039 and 0.58. Now that was easy, wasn't it?

(Not) Efficient
---------------

Have a look at this number:  
`0.00007041000000000473` (22 characters)  
For numpy this is how it should be printed  
`7.041000000000473e-05` (21 characters)  
Great, right? We just saved one character.

Could we save the same number of characters if we removed the leading 0?  
`.00007041000000000473` (21 characters)  
Yes. Would that be easier to read and compare? Also yes.

Difficult to compress
---------------------

A lot of us use CSV's to transfer data from one place to the other. It's definitely not the most efficient thing to do, but the reality is, it's one of, if not the most, common.

CSV is easily understood and can be opened by multiple software, besides not having any kind of proprietary restrictive license.

Because CSV files are text based, most people would understand why compression is so important and typically compression algorithms work pretty well with this kind of file.  
However, because of the way most compression algorithms work, having a `e` and a `-` mixed in there with the numbers will increase the size of your compressed file, considerably.

Suggestion #1 : Make it worse
-----------------------------

I have a great suggestion for the person who thought this was a good idea.

Since you hate so many people, why not make it worse? Expand the numbers even further, this way we can convert something like 96,734,147 into:

9(107) \+ 6(106) \+ 7(105) \+ 3(104) \+ 4(103) \+ 1(102) \+ 4(101) \+ 7(100) = 96,734,147

What do you think? Great uh?

The magic solution
------------------

Memorize this or write it somewhere, it contains the code for both pandas and numpy.

    import numpy as np
    import pandas
    
    np.set_printoptions(suppress=True)
    pd.set_option('display.float_format', lambda x: '%.10f' % x)
    

To enable again:

    np.set_printoptions(suppress=False)
    pandas.reset_option('display.float_format')
    

According to the [documentation](https://numpy.org/doc/stable/reference/generated/numpy.set_printoptions.html), this is what `suppress` does:

> If True, always print floating point numbers using fixed point notation, in which case numbers equal to zero in the current precision will print as zero. If False, then scientific notation is used when absolute value of the smallest number is &lt; 1e-4 or the ratio of the maximum absolute value to the minimum is &gt; 1e3. The default is False.

I've also added this to my Python checklist:

[Python Checklist<br>Thoughts, stories, and ideas mostly on Data Science, Artificial Intelligence and Crypto.<br>![](https://manuellevi.com/favicon.ico)Manuel LeviManuel Levi](https://manuellevi.com/python-checklist/)