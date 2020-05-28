<!-- PROJECT LOGO -->
<br />
<p align="center">
  <a href="https://snowplow.jonnobrow.co.uk">
    <img src="https://gitlab.com/Jonnobrow/snowplow-website/-/raw/master/_includes/logo.png" alt="Logo" width="80" height="80">
  </a>

  <h3 align="center">Jonnoplow</h3>

  <p align="center">
    a simple jekyll blog used to experiment with the snowplow micro trackers
    <br />
    <a href="https://snowplow.jonnobrow.co.uk">Read the posts I made along the way</a>
  </p>
</p>

<!-- TABLE OF CONTENTS -->
## Table of Contents

* [About the Site](#about-the-site)
  * [Built With](#built-with)
* [Tracker](#tracker)
  * [Events](#events)
* [Usage](#usage)
* [Final Thoughts](#final-thoughts)

<!-- ABOUT THE PROJECT -->
## About The Site

[![Product Name Screen Shot][product-screenshot]](https://example.com)

### Built With

* [Ruby Jekyll](https://jekyllrb.com/)
* [Minima Theme](https://github.com/jekyll/minima)
* [Snowplow Micro Trackers](https://github.com/snowplow-incubator/snowplow-micro/)

## Tracker

I chose to use the JavaScript tracker as it would fit easily into the simple Jekyll blog.

### Events

#### Page View
The page view event was the easiest to setup, however I chose to add a custom context to it so that more information about the blog post was shared when a page was visited.
This was a valuable learning experience and helped me gain familiarity with the trackers and event concepts and also with the entire process.
I used a predefined schema as my blog was quite simple.

#### Custom Structured Author Event
In addition to the page view event, I experimented with the structured events.
I thought it would be an interesting metric to see which authors on the blog were most popular.
Therefore I added an event to see when the author page was viewed.
Given more time I would be interested to improve this metric further and use link click tracking and activity tracking to further understand engagement of users with certain authors and their posts.

#### Custom Self Describing Events
I did not implement one of these. However this was only as I felt I would be repeating what was already possible with predefined events, given the limited functionality of the blog.
For my personal website I am currently developing some Git integration for the repositories of my most notable projects.
I feel that custom self describing events would be useful when tracking interactions with that functionality as keeping a record of the state of a repository at the time of the interaction would be useful.

## Usage

In order to use my application you will need:
* Ruby
  * The `jekyll` gem
  * The `bundler` gem
* Git
* Docker

1. Clone the repository, it contains the entire site and the configuration files for the snowplow-micro docker image
2. Run the site using `bundle exec jekyll serve`, it will be available on `localhost:4000`
3. Run the snowplow-micro docker image using the following command:
```shell
docker run --mount type=bind,source=$(pwd)/micro-config,destination=/config -p 9090:9090 snowplow/snowplow-micro:latest --collector-config /config/micro.conf --iglu /config/iglu.json
```
4. Ready to trigger some events :)

## Final Thoughts

The Snowplow JavaScript tracker was very easy to integrate with my simple site and I was quickly able to get useful data out through the Snowplow Micro collector.
I would be very interested in working on more complex events in larger scale projects.
