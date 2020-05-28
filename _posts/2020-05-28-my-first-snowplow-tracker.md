---
layout: post
title: "My First Snowplow Tracker"
date: 2020-05-28 13:50
---

# Introduction

Now that I have [setup a simple Jekyll Blog]({% post_url 2020-05-28-setting-up-a-simple-jekyll-site %}) and [setup Snowplow Micro]({% post_url 2020-05-28-setting-up-snowplow-micro %}) I can create my first tracker.

I am going to use a JavaScript tracker to begin with and probably just set up a simple Page View tracker.

# Installing the JavaScript tracker

The first step is to install the JavaScript tracker to my site.
This is done by including the `sp.js` file asynchronously.
I will create a new include for this in my Jekyll site:

```html
<!-- Snowplow starts plowing -->
<script type="text/javascript">
 ;(function(p,l,o,w,i,n,g){if(!p[i]){p.GlobalSnowplowNamespace=p.GlobalSnowplowNamespace||[];
     p.GlobalSnowplowNamespace.push(i);p[i]=function(){(p[i].q=p[i].q||[]).push(arguments)
     };p[i].q=p[i].q||[];n=l.createElement(o);g=l.getElementsByTagName(o)[0];n.async=1;
     n.src=w;g.parentNode.insertBefore(n,g)}}(window,document,"script","{{site.baseurl}}/assets/sp.js","snowplow"));

 // Initialise the tracker
 window.snowplow("newTracker", "jonnobrow-tracker", "localhost:9090", {
     appId: "jonnoplow",
     platform: "web",
     context: {
         webPage: true
     }
 });

 // Enable Page View Tracking
 window.snowplow('trackPageView');
</script>
<!-- Snowplow stops plowing -->
```

I have set the URL for the script to a local copy of `sp.js`.
I have also pointed the `newTracker` to `localhost:9090` as this is where the snowplow micro service is running.

At first this didn't work, because the CORS request was being blocked by a browser addon I use. After allowing the request, there were no more errors in my JavaScript console and the `akka` logger showed a new connection.

# Testing the page view tracker

I now wanted to make sure the tracker was working properly.
I knew I had now installed the snowplow javascript tracker script to my site but hadn't seen any evidence it was working with the snowplow micro instance I had setup.

To test it I clicked around a few pages on the site and then made a request to `localhost:9090/micro/all` to see if there were any events recorded.
As expected the good event count corresponded to the number of pages I had clicked.

Next I wanted to see some more information about these events to get a better feel for the information that was being tracked.
Therefore I made a request to `localhost:9090/micro/good` and pleased to see that there was a record in the response JSON for each of the page views I had made.


The `event` property contained information about the page I had clicked on, information about my device and browser, the time, IP address and other information I can see being useful to calculate demographics for a website.
The `eventType` was `pv` for all of the events (assume this is an abbreviation of page view).
The `schema` and `context` were both `null`, presumably because I did not specify this.

# Specifying Schema and Context

After seeing the schema and contexts were blank in my events, I felt I wanted to try and cause these to be populated. 
The documentation on the page view tracker provided this so I copied it over to see if that worked.

It didn't, but the error messages from `localhost:9090/micro/bad` were useful in solving this problem.
It couldn't resolve the schema, then I realised that the documentation clearly contained dummy schema URLs.
I decided that I wanted to make this tracker a bit more useful so I hunted for schema on [iglucentral.com](http://iglucentral.com) that sounded appropriate and went from there.

## Implementing the `org.schema/WebPage` schema

My understanding of the schemas for contexts is that we can provide extra data (in the form of the the `data` field) about a page view.
Therefore I wanted to be able to share information like post tags, author and date created for my blog.
To do this I added the following `data` field to my `snowplow.html` include:

```json
{
  {% if author %}
  author: "{{author.name}} <{{author.email}}>",
  {% endif %}
  {% if page.genre %}
  genre: "{{page.genre}}",
  {% endif %}
  dateCreated: "{{page.date}}",
  dateModified: "{{page.date}}",
  datePublished: "{{page.date}}"
}
```

This didn't work either, I was now getting an error to do with extracting information from the context.
It turned out that the schema still wasn't being loaded correctly because I had used single quotes rather than double quotes.

After fixing this there was a new error to solve.
This was down to the date form, hopefully an easy fix in my Jekyll Liquid code.

```json
data: {
    {% if author %}
    author: "{{author.name}} <{{author.email}}>",
    {% endif %}
    {% if page.genre %}
    genre: "{{page.genre}}",
    {% endif %}
    {% if page.date %}
    dateCreated: "{{page.date | date: "%Y-%m-%dT%TZ"}}",
    dateModified: "{{page.date | date: "%Y-%m-%dT%TZ"}}",
    datePublished: "{{page.date | date: "%Y-%m-%dT%TZ"}}",
    {% endif %}
    inLanguage: "{{site.language}}"
}
```

This now created only `Good` events when I visited pages, therefore I assumed it was working.
Just to be sure I decoded the Base64 string in the `cx` parameter and out came the following JSON:

```json
{
    "schema": "iglu:com.snowplowanalytics.snowplow\/contexts\/jsonschema\/1-0-0",
    "data": [
        {
            "schema": "iglu:org.schema\/WebPage\/jsonschema\/1-0-0",
            "data": {
                "dateCreated": "2020-05-28T13:50:00Z",
                "dateModified": "2020-05-28T13:50:00Z",
                "datePublished": "2020-05-28T13:50:00Z",
                "inLanguage": ""
            }
        }
    ]
}
```

It works, we can now see more information about the post.

# Conclusion

Setting up a simple tracker and seeing results was fairly straightforward, adding in a custom context was a bit more work but now I understand what has to be done it makes sense.
I want to create more events now and I think I will start with a structured event.
