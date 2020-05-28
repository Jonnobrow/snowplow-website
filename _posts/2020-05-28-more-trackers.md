---
layout: post
title: "More Trackers"
date: 2020-05-28 17:30
author: jonnobrow
---

# Introduction

Having [used the auto-tracked page view event]({% post_url 2020-05-28-my-first-snowplow-tracker %}) I now feel that I have enough of an understanding to play around with some more complicated events.

# Looking at a custom structured event

Since I added the `author` field to posts in first tracker post I wanted to add a page to show some more information about the authors.
For example an avatar, social media, website and short description.
I first added the `authors` collection and the layout for an author page.

It looks like this: 
![Screenshot of the Author Page](/assets/author-page.png)

Now that the page is in place I want to trigger a special event when we visit that page.


