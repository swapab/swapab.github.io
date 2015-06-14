---
layout: post
title: Optimize Prime
description: "Performace Tuning"
headline: "Performace Optimization"
categories: programming
tags: 
  - caching
  - database
imagefeature: "website-speed.jpg"
imagecredit: spreadeffect.com
imagecreditlink: "http://www.spreadeffect.com/blog/improve-website-speed/"
comments: false
mathjax: null
featured: true
published: true
---

h3. How to check Page performance?

Page/Site Performance should be improved when the load time increases above our expected becnhmark. Optimize only what's slow

For example: If we desire the overall load time to be 100ms but the load time has increased then we can analyze it using various online as well as offline tool.

* "Google Page speed insights":https://developers.google.com/speed/pagespeed/insights/
* "YSlow add-on":https://addons.mozilla.org/en-us/firefox/addon/yslow/
* "Rails Performance Testing":http://guides.rubyonrails.org/v3.2.13/performance_testing.html


h3. How to improve performance?

There are many ways of how you can boost performance of Ruby On Rails applications. Approaches might be different and depend on the application structure, size of the database and traffic intensity.

Some of the techniques and architectural solutions that will help you to improve performance of your applications could be:

* Use Caching
* Database Optimization
* Scaling Out Your Database
* Front-end optimization

h4. Use Caching

Fragment Caching is the widely used cahcing technique.
Unfortunately, caching the whole page is rarely possible when you’re developing dynamic web applications. Fragment Caching allows a fragment of view logic to be wrapped in a cache block and served out of the cache store when the next request comes in.

Rails has different stores for the cached data created by action and fragment caches. Page caches are always stored on disk. The default cache stores are MemoryStore, FileStore, DrbStore and Memcached store.

"More on Fragment Caching":/cache-it-and-sweep-it

h4. Database Optimization

Interacting with database is usually the slowest part of the application. Hopefully, there are many things you can do to improve the performance:

  *  Add all necessary indexes for primary(except ID) and foreign keys and for fields used in conditions for filtering
  *  Remove unused or ineffective indexes
  *  Revise SQL queries and optimize them (use the EXPLAIN command)
  *  Use eager loading of associations in Rails models
  *  Don’t use transactions if they are not necessary
  *  Denormalize some tables from 3-d form to 2-nd to avoid redundant joins
  *  Perform partitioning for large tables

Use query analyzer tools such as:

  *  "Bullet Gem":https://github.com/flyerhzm/bullet
  *  "Rails Analyzer":http://rails-analyzer.rubyforge.org/
  *  "New Relic is a must have":http://newrelic.com/

h4. Scaling Out Your Database

Hoziontally scaling database can be a huge performance gain, as it divides the data logically across tables.

With Rails you can use "Octopus Gem":https://github.com/tchandy/octopus.

Octopus provides:

  *  Sharding (with multiple shards, and grouped shards).
  *  Replication (Master/slave support, with multiple slaves).
  *  Moving data between shards with migrations.


*Database Replication*

It’s used to address concurrent access to the same data. Database replication enables us to load-balance the access to the shared data elements among multiple replicated database instances. In this way we can distribute the load across database servers, and maintain performance even if the number of concurrent users increases.

*Database partitioning/sharding*

Database shards/partitions enable the distribution of data on multiple nodes. In other words, each node holds part of the data. This is a better approach for scaling both read and write operations, as well as more efficient use of capacity, as it reduces the volume of data in each database instance.

h4. Front-end optimization

Users spend a lot of time on waiting browser to finish downloading all page components such as images, style sheets, scripts, etc. Reducing the amount of components will minimize the number of HTTP requests which will lead to the faster page loading.


h3. Analytics

Analytics also termed as "Web analytics":http://en.wikipedia.org/wiki/Web_analytics is a generic term meaning the study of the impact of a website on its users. Common use of Web analytics tools is to measure concrete details as how many people visited their site, how many of those visitors were unique visitors, how they came to the site (i.e., if they followed a link to get to the site or came there directly), what keywords they searched with on the site's search engine, how long they stayed on a given page or on the entire site and what links they clicked on and when they left the site.

Google Provides a vast range of "features":http://www.google.co.in/analytics/features/ to draw out analytics based on various factors
