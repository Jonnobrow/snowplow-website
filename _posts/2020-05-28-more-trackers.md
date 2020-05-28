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

Since it is a structured event I defined it as follows:

```javascript
// Custom author view tracking
{%raw%}
{% if page.collection == "authors" %}
{% assign author = site.authors | where: 'nickname', page.nickname | first %}
window.snowplow("trackStructEvent", "Authors", "Viewed", "{{ author.name }}", '', '0.0');
{% endif %}
{%endraw%}
```

This generates another event every time an author page is visited.
This would be useful for see which authors were most popular on the site.

# Conclusions

My research into custom events has led me to believe that I have explored as much as would be realistic for a simple blog.
Without creating a schema and defining custom contexts I think what I have done is all that is possible.
