---
layout: page
title: About SKODJOB
permalink: /about/
---

<div class="about-hero">
  <h1>Welcome this little boy to the town</h1>
  <p class="about-subtitle">The Story Behind SKODJOB</p>
</div>
The whole idea started a few years back. Our main work consists of finding all sorts of defaults in applications running on top of Kubernetes. 
That itself is quite a challenge if we consider that systems like that could be distributed systems, integration of several different components, or even integration of several systems together.
Our experience was mostly about spinning up Kubernetes cluster, deploying our project, in our case [Strimzi](https://strimzi.io/), playing with it a little bit on the test level, and then evaluating the state with some results.
That was enough for testing most of the features we introduced. However, at some point, it started to be a little bit boring, so we were thinking about some new challenge.

## Codename Teal'c
During the Covid-19 pandemic, we spent a lot of time at home and during that time one of the best TV series was online - Stargate SG-1.
No worries, I am not going to promote the series or spoil the plot for you! 
However, within our team, we liked Teal'c character, and then it started - let's start to work on something, and lets called it Teal'c!

We started with an idea to test our projects in an environment that is more or less similar to what customers could be using.
Our existing approach was fine for verifying features, but it was usually done in a small scale. 
A lot of questions suddenly come to our minds like:
- how does the software behave when it will be running for several weeks/months?
- how we can easily automate the deployment and upgrade process?
- how upgrades of underlying architecture influences it?
- actually, how users are using it? :O
- what about monitoring?
- collecting logs?
- chaos?!

So we took that road and during free time we moved from regular verification to customer simulation with a lot of unknowns.

## Teal'c is dead, long live Skodjob
It took some time, but we started to reap the fruits of our labor. 
From quite complex (but still simple) testing we moved to continuous verification of several projects that were deployed for months!
Issues were found, motivation enlarged, and experience? Marvelous.

During the years we did several talks, wrote some blog posts about our work, and learned a lot of cool stuff. 
However, we were still missing something. 
The freedom of sharing our knowledge quickly and easily somewhere, where we could manage it on our own.
We wanted to write down our ideas and experience after a couple of years of experience without any limitations.

And here we are, on `skodjob.io`. 
It is a good feeling, and we all hope, that you will enjoy our posts where we will try to provide as much information as we may, and also information that we were missing in other posts that we came across in the past.
Happy reading!
