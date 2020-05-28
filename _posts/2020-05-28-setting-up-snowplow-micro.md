---
layout: post
title: "Setting up Snowplow Micro"
date: 2020-05-28 12:35
---

# Introduction to Snowplow Micro

You can find a lot of information and the source for [snowplow-micro here](https://github.com/snowplow-incubator/snowplow-micro/).

# Attempt 1

My first attempt and installing and hosting Snowplow Micro will be to try and get it running on Gitlab pages.
Since its hosted on docker hub I am hopeful I can pull this off, otherwise I will revert to locally hosted and just use the local version of this blog to experiment and test with the trackers.

_I quickly learnt that snowplow micro was really not intended for anything other than local use and therefore the effort involved in the setup for gitlab pages was more than it was worth._

# Attempt 2

Now that I have ruled out hosting Snowplow Micro I can make sure everything runs properly on my local machine.

## Configurations

I had a brief read through the `micro.conf` and `iglu.json` files and nothing jumped out at me in terms of needing to be changed at this stage. 

## Running the Docker image

Docker was already installed on my machine as I do a fair amount of messing about with server side stuff and therefore I copied and pasted the command from the README and changed the bind mount source path to `configurations`:

```shell
docker run --mount type=bind,source=$(pwd)/configurations,destination=/config -p 9090:9090 snowplow/snowplow-micro:latest --collector-config /config/micro.conf --iglu /config/iglu.json
```

After pulling the image the logger showed that the `REST interface bound to /0.0.0.0:9090` and no errors displayed.
To further ensure I had everything setup correctly I navigated to `localhost:9090/micro/all` in my browser and was pleased to see that the expected response displayed:

```json
total: 0
good: 0
bad: 0
```

# Conclusion

This was simple and painless as advertised. 
I can now move on to setting up my very first tracker.
