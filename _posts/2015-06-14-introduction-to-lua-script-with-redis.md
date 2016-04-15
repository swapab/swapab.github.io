---
layout: post
title: Introduction to LUA script with Redis
description: "Feel the power of LUA"
headline: "Let's Fire up the Engines"
categories: programming
tags:
  - redis
  - lua
imagefeature: "website-speed.jpg"
imagecredit: spreadeffect.com
imagecreditlink: "http://www.spreadeffect.com/blog/improve-website-speed/"
comments: false
mathjax: null
featured: true
published: true
---

*What is Redis ?*

We all might be familiar with [Redis](http://redis.io/){:target="_blank"}.
For those who are not - It's a **NoSQL** key-value data store. More precisely, it is a data structure server.
To read more - [Redis-Wiki](http://en.wikipedia.org/wiki/Redis_%28dbms%29){:target="_blank"}

Do [try redis](http://try.redis.io/){:target="_blank"} it has a nice interactive tutorial.

To install and setup redis on your machine see [how-to-install-and-use-redis](https://www.digitalocean.com/community/articles/how-to-install-and-use-redis){:target="_blank"}

*What is LUA ?*

LUA is an embeddable scripting language. About LUA - http://www.lua.org/about.html
Redis >= 2.6 supports embedded scripting language.

Lets give a try to it.

To run a script in redis we need to use [EVAL](http://redis.io/commands/EVAL command){:target="_blank"}.

A Lua script file should be saved with `.lua` extension

{% highlight ruby %}
local msg = "Hello, world!"
return msg
{% endhighlight %}

Save this file locally as `hello.lua` and run it like so:

`redis-cli EVAL "$(cat hello.lua)" 0`

Running this will print `Hello, world!`.

The first argument of EVAL is the complete lua script — here we’re using the cat command to read the script from a file. The second argument is the number of Redis keys that the script will access. Our simple **Hello World** script doesn’t access any keys, so we use **0**.