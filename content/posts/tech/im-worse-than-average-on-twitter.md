+++
author = "Manuel Levi"
date = 2022-06-09T23:41:48Z
description = ""
draft = false
image = "../images/twitter_me_vs_others.png"
slug = "im-worse-than-average-on-twitter"
title = "I'm worse than average (on Twitter)"
+++

## The exponential power of social networks


Since I did my analysis on [my Twitter](https://twitter.com/mlevif) followers and their Twitter followers (which you can read [here](https://manuellevi.com/network-analysis-on-twitter/)) I wanted to focus on other stuff but there was this one thing that kept in my head.

There was one user that I couldn't analyze, this user was [@betsofbitcoin](https://twitter.com/betsofbitcoin). Even though it hasn't been active, that account has over 12k followers and in the Twitter space, that's not even close to the top.

This got me thinking about how the number of followers distributes itself in such a non-uniform way.

So I decided to plot a histogram of my number of followers. With a single image, you will understand what I mean.

![](../images/twitter_followersfollowers.png)

Hard to read, right?

What is interesting about this is that if you sum up all the followers of these accounts (even ignoring the massive overlap!), this account would represent **47%** of all follower accounts!

I don't like pie charts to analyze data, but they can certainly be dramatic:

![](../images/twitter_bets_pie.png)

I mean, there's no competition there. **47% of all followers, including overlaps on the others!**

You might be thinking:

> Is this only true for my network?

No. And I'll prove it to you later. But for now, let's find a way to visualize this a bit better.

![](../images/twitter_followersfollowers_log.png)

Now that's much easier to read, isn't it?

## Exponential Nature

I told you this was not specific to my network and for these accounts. I won't go into the theoretical explanation of why, there are many lectures online with that info, instead I want to show you another example, and we don't need to go too far.

This is the network of my followers and their followers. I've set up the background to blue and my followers in orange to increase the contrast, otherwise, you wouldn't even see it.

My network of followers represents only thin orange lines in those charts.

![](../images/twitter_stacked_1st_2nd.png)

These are the statistics:

| My Followers | Mean (1st degree) | Median (1st Degree) | Mean (2nd degree) | Median(2nd degree) |
| --- | --- | --- | --- | --- |
| 55  | 483.3 | 146.0 | 22434.8 | 549 |

You can see that not only am I below average, but most of my followers are also below average!

  
You can also see, through the large difference between the mean and the median that the number of connections is definitely not distributed uniformly.

## I'm worse than average


More than 50% of people who follow me have more than 145 followers, and more than 50% of the people who follow them have at least 549 followers.

I have 55 (one more since my last post, but hey, that's almost a 2% growth!).

55 is also 10 times lower than 550 (the median of the larger network), ins't that interesting?

![](https://manuellevi.com/twitter_me_vs_others.png)

The red vertical line is my number of followers, blue is the distribution of the number of my direct followers and their followers (2nd degree).

So here we go. That's the current number of my Twitter followers against my 1st-degree followers and their followers.

I wonder if and how this number will change in the future.

## Other Interesting things


**Extremes:**

* My largest follower is @betsofbitcoins with 12123 followers.
* @BarackObama follows @vascopatricio. Want to tell me something Vasco?
* @JacquesWhales is followed by @garyvee.

Some **interesting coincidences**:

* @benlandis follows both @andreesg and @ticklishart (different networks) - he is following 1.5M accounts and has 2.9M followers.
* @jlbribeiro is followed by @thebeatles and @LILBTHEBASEDGOD (who also follows @scottwernerd - different network)
* @rjvitorino is followed by @foofighters and @alispagnola (who also follows @GrimGreysson - different network)

It's clear to me some of these accounts are using the old "Let me follow tons of people and hopefully some of them will follow me back" hack. It seems to have worked!

Many of these accounts have hundreds of thousands if not millions of followers 🤔.

This kind of thing could help someone with only 55 get to 550. To think about.

Or perhaps they are being honest and they just follow back the majority of people that follow them, **manually** 🤷.

### Help me get to mediocrity!


> **Don't compare yourself to others.** There's always someone smarter, someone cuter, someone richer, someone with a larger...nose. People tend to compare themselves with others while looking at a very limited number of factors. This is not fair. It's more productive to compare yourself to your potential.

If you're reading this and found this interesting, and you want to help me be mediocre consider following me on [Twitter](https://twitter.com/mlevif), [LinkedIn](https://www.linkedin.com/in/manuellevi/), and [Instagram](https://www.instagram.com/manuel.levi/) for more. Also, feel free to reach out to me through any of those channels :)

K-Bai.

* * *

😏

Like what you see? 

[Call me, maybe](https://calendly.com/manuellevi-eai/30min)
