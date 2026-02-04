---
layout: post
title: What is Skodjob performance hub?
date: 2024-01-15 16:00:00
categories: performance
image: /assets/perf-intro.jpg
---

## What is our goal?
Performance is and has always been something cursed in some way. Everyone knows that it should be important
but nobody tries to do it. Well, we would like to **change this**!

Performance hub is the source of all tooling that we are working on. We hope that these tools will
give you enough confidence to start **performance tuning** your software. Well as we are mostly practical folks
lets move to what is currently included in [performance hub](https://github.com/skodjob/database-performance-hub).

### Database Manipulation Tool
Imagine the situation when you are working on a product that supports multiple database engines and if you
would like to perform some tuning. Well, that sucks! You have to create different loads because
most of the databases use different dialects. This is certainly not something that will encourage
you in this action. Believe us we have been in this situation. Gladly we have a solution for you!

[DMT (database-manipulation-tool)](https://github.com/skodjob/database-performance-hub/tree/main/database-manipulation-tool)
is a Quarkus application that allows you to specify data load in `JSON` and the tool will take care of the rest.
So far we have implemented `PostgreSQL, MongoDB, and MySQL` dialects and we aim for more!
Don't worry we didn't forget about the perf part. This tool is currently developed in two modes
performance and production. The performance one is stripped of all things that could influence
The results of the performance tests or tuning. Production one is more user-friendly, specifically the purpose of
integration tests.

### Load generator
Well, distributing load into databases is one thing but where we will get the load? Fortunately, this
topic is also something that we thought about. We have come up with [Load Generator](https://github.com/skodjob/database-performance-hub/tree/main/load-generator).
This generator will effectively create load in the selected pattern. Sometimes you would like **constant load**, like 500 requests per second
for 1 hour. Other times you need to create **peak load**, like when you want to simulate traffic on the website
when tickets to a new concert are released, and many more. Currently, we have only some of these but we will
extend the functionality in no time! Database manipulation tool and Load generator are using same JSON schema which means
they can and should be used together!

### Automation
Besides the previous two applications, we are also storing all the [automation](https://github.com/skodjob/database-performance-hub/tree/main/ansible-automation) we prepare during
our performance tuning and testing. Currently, you might find the following helpful ansible scripts there:

- DMT deployment
- MySQL database deployment and auto-tune
- [NetData](https://github.com/netdata/netdata) parents and children nodes deployment

In addition to that we have already process automation scripts for PostgreSQL and MongoDB databases.

### Monitoring
Lastly and maybe most importantly we are trying to find the most effective solution when it comes to monitoring
applications during performance testing or tuning. You might think that this is easy, deploy Prometheus, and problems are solved. This time **we will prove you wrong!** Most of the monitoring solutions are scraping all the metrics
each second (minimal value) and this is certainly not fast enough! Most of the problems happen **below one second** interval
and you will **lose** all that information because of the scraping.

This problem is not easy to solve and it is surely something we would like to solve in the **future**.


## Join us!
All our projects are **open-source** under MIT licenses and we are more than happy to welcome new
**contributors** from all around the globe! Doesn't matter where are you working or how skilled you are
simply **open a pull request, issue, or write a blog post, we appreciate everything**.
