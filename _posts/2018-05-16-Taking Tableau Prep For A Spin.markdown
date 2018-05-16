---
layout: post
title:  "Taking Tableau Prep For A Spin"
date:   2018-05-16 00:01:00 -0500
categories: tableau
published: true
---

A new product from [Tableau](https://www.tableau.com). Having a track record like Tableau's makes these announcements exciting. Is it going to be revolutionary? Will it need us to re-think how we prepare answers? This has all happened before. In the past, Tableau has put much pressure into many a software vendor. I am looking forward to finding more about Tableau Prep but figured relaying a first impression is worth my time.

Using an a data export from an old race, [Tableau Prep](https://www.tableau.com/products/prep) makes it easy to get a feel for the data we have.  

In the screenshot that follows, I am selecting a group on a dimension and Tableau Prep is dynamically highlighting the subsets in other dimensions that are inclusive of my selection. This can go on forever to provide a clear picture, in this case, of the type of race this was.  

This tool feature would lend itself very well for cohort analysis. I think this would also be useful in pointing out anomalies in our data. Neat.

<img src="/assets/tableau_prep/screen_1.PNG" alt="" width="95%">

For the Division selected, M4044 (40-44 year old men; that is me!), we can see how these records are distributed over sex, age, city, pace in seconds, distance in seconds and duration in seconds.  

Next up, I tried to enrich some raw data with an additional data file. In this case, I grabbed two files. The first, a Country/IP Address CSV with the following information: Start IP, End IP, Country Code, Country, City. The other file just has two digit country codes and country names. My intent is to assert that the simple join and merge of files is feasible visually.

<img src="/assets/tableau_prep/screen_2.PNG" alt="" width="95%">

Without much fuss, but a bit slower, I was able to join the files and combine information from both into one set. This is the same way this would be done in Tableau Public. Good job. Performance is greatly dependent on the size of the data understandably. Still, designing a reusable workflow in T.P. feels very reasonable. Good job.

Adding an output step is trivial. Here you can see such step in progress generating a merged file as intended.

<img src="/assets/tableau_prep/screen_3.PNG" alt="" width="95%">

In typical Tableau fashion, the product looks very polished. It is worth pointing out that this is not a free product like their public version of desktop. Because of this, it has no data source limitations, and you can save work locally.  

I think that this is the type of product bigger software vendors initially ignore at their own peril. Tableau Prep is so far out of focus for them that this will, most likely, be ignored in the beginning. The customer, however, will notice immediately and demand parity or seriously consider making Tableau's Products part of their toolset. This has all happened before :)  

Hopefully, I will have some time before my trial runs out to go deeper into T.P. as it seems like another neat tool from Tableau.