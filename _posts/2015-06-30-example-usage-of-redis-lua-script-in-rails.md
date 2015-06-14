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
---

This topic is about using Redis and LUA script in Rails.

Before proceeding, you may like to read " Introduction to LUA script with Redis":/introduction-to-lua-script-with-redis


Redis doesn't have any command to calculate total number of keys

We'll use "zrangebyscore":http://redis.io/commands/zrangebyscore to find number of members between index range.

Instal "redis-rb":https://github.com/redis/redis-rb

Start `rails console`

*Prepare a dataset based on timestamp*

bc. > t10 = 10.minutes.ago
=> 1386935151
> t20 = 20.minutes.ago
=> 1386934568
> t30 = 30.minutes.ago
=> 1386933971
> t40 = 40.minutes.ago
=> 1386933374

_This is non-realistic data, may vary for you._

*Populate redis with Sorted Set*

bc.. > redis = Redis.new # initialize redis
=> <Redis #123>

# zadd - Add one or more members to a sorted set.
> redis.zadd 'T_KEY', t10, 'time-10-ago'
=> true
> redis.zadd 'T_KEY', t20, 'time-20-ago'
=> true
> redis.zadd 'T_KEY', t30, 'time-30-ago'
=> true
> redis.zadd 'T_KEY', t40, 'time-40-ago'
=> true

# zrangebyscore - Return a range of members in a sorted set, by score.
> redis.zrangebyscore('T_KEY', 1386933900, 1386936000)
=> ["time-30-ago", "time-20-ago", "time-10-ago"]

# with ruby
> redis.zrangebyscore('T_KEY', 1386933900, 1386936000).size
> 4

#Total count using LUA script
> redis.eval("return #redis.call('zrangebyscore', 'T_KEY', 1386933000, 1386936000)")
=> 4

bq. Done. You have the count between a range of sorted set without using ruby to do the extra efforts.
With LUA script you can do the heavy calculation on redis server.