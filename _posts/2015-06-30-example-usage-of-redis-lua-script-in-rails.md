---
layout: post
title: Redis + LUA + Rails = Love
description: "Illustrative example"
headline: "This is FUN"
categories: programming
tags:
  - redis
  - lua
  - rails
imagefeature: "website-speed.jpg"
imagecredit: spreadeffect.com
imagecreditlink: "http://www.spreadeffect.com/blog/improve-website-speed/"
comments: false
mathjax: null
featured: true
published: true
future: true
---

This topic is about using Redis and LUA script in Rails.

Before proceeding, you may like to read [Introduction to LUA script with Redis](/programming/introduction-to-lua-script-with-redis)


Redis doesn't have any command to calculate total number of keys

We'll use [zrangebyscore](http://redis.io/commands/zrangebyscore){:target="_blank"} to find number of members between index range.

Instal [redis-rb](https://github.com/redis/redis-rb){:target="_blank"}

Start `rails console`

#### Prepare a dataset based on timestamp

{% highlight ruby %}
> t10 = 10.minutes.ago
=> 1386935151
> t20 = 20.minutes.ago
=> 1386934568
> t30 = 30.minutes.ago
=> 1386933971
> t40 = 40.minutes.ago
=> 1386933374
{% endhighlight %}

_This is non-realistic data, may vary for you._

#### Populate redis with Sorted Set

{% highlight ruby %}
> redis = Redis.new # initialize redis
=> <Redis #123>
{% endhighlight %}

#### zadd - Add one or more members to a sorted set

{% highlight ruby %}
> redis.zadd 'T_KEY', t10, 'time-10-ago'
=> true
> redis.zadd 'T_KEY', t20, 'time-20-ago'
=> true
> redis.zadd 'T_KEY', t30, 'time-30-ago'
=> true
> redis.zadd 'T_KEY', t40, 'time-40-ago'
=> true
{% endhighlight %}

#### zrangebyscore - Return a range of members in a sorted set, by score.

{% highlight ruby %}
> redis.zrangebyscore('T_KEY', 1386933900, 1386936000)
=> ["time-30-ago", "time-20-ago", "time-10-ago"]
{% endhighlight %}

#### with ruby

{% highlight ruby %}
> redis.zrangebyscore('T_KEY', 1386933900, 1386936000).size
> 4
{% endhighlight %}

#### Total count using LUA script

{% highlight ruby %}
> redis.eval("return #redis.call('zrangebyscore', 'T_KEY', 1386933000, 1386936000)")
=> 4
{% endhighlight %}

> Done.

- [x] You have the count between a range of sorted set without using ruby to do the extra efforts.

- [x] With LUA script you can do the heavy calculation on redis server.