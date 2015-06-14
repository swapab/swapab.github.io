---
layout: post
title: Cache it and Sweep it
description: "Fragment caching and sweeping strategy"
headline: "Give me the SPEED"
categories: programming
tags: 
  - caching
  - rails
imagefeature: "website-speed.jpg"
imagecredit: spreadeffect.com
imagecreditlink: "http://www.spreadeffect.com/blog/improve-website-speed/"
comments: false
mathjax: null
featured: true
published: true
---

*Rails provides three types of caching techniques:*

  * page caching
  * action caching
  * fragment caching

The "rails_guides_on_caching":http://guides.rubyonrails.org/caching_with_rails.html is the best place to know how it works.

This article is to demonstrate fragment caching the *Rails Way* with various data-stores and clearing those cached fragments on ActiveRecord callbacks on CRUD operations.

p. Rails "@ActiveSupport::Cache::Store@":http://api.rubyonrails.org/classes/ActiveSupport/Cache/Store.html is responsible to do all the read and write operations.

*1. The setup*

By default caching is disabled in rails. You will find the following @config@ commented out in production.rb

bc..  # Use a different cache store in production
   # config.cache_store = :mem_cache_store

bq. You can either enable the cache_store globally in @application.rb@ or keep it environment specific viz. development, production, staging. So you have a choice to select destination cache store for each environment.

We'll keep it default for all envs. and enable it in @application.rb@

bc..  # set default cache store as redis
   config.cache_store = :redis_store
   # OR
   # config.cache_store = :memcached_store
   # OR
   # config.cache_store = :dalli_store

bq. Alternatively on Heroku you have to specify the config URL that would be used to store Redis data.

bc. config.cache_store = ActiveSupport::Cache::RailsRedisCache.new(:url => ENV['REDISTOGO_URL'])

bq. Once you have decided the data-store, next enable caching.

bc. config.action_controller.perform_caching = true

_In Gemfile_

bc. gem "redis-store"

*2. Cache a fragment*

Here's an example to show _popular articles by an author_ using cache block.

bc.. <% cache [@author, 'popular_articles'] do %>
  render @popular_articles
<% end %>

bq. This works on cache fetch/miss concept, where every call to the cache method will first look for any existing store with the key(here its author_name:popular_articles). If its a miss(no data found) then it will cache the fragment in the block and return it. And the cycle goes on.

bq. Perfect, now you have successfully cached the above fragment. The rendered HTML would be something like

bc.. <p>Popular articles<p>
<ul>
 <li>Article 1</li>
 <li>Article 2</li>
 <li>Article 3</li>
</ul>

bq. *3. Sweeping strategy*

Every caching technique needs a sweeping or expiration system.
Rails "@ActionController::Caching::Sweeper@":http://apidock.com/rails/ActionController/Caching/Sweeping does the job for you.

Sweepers are the terminators of the caching world and responsible for expiring caches when model objects change. They do this by being half-observers, half-filters and implementing callbacks for both roles.

For our articles example we can implement our @ArticleSweeper@ as:

bc.. class ArticleSweeper < ActionController::Caching::Sweeper
  observe Article
 
  def after_save(record)
  expire_fragment [record.try(:author), 'popular_articles']
  end
 
  def after_destroy(record)
  expire_fragment [record.try(:author), 'popular_articles']
  end
end

bq. This would be activated during CRUD operation based on REST. Hence the controller should know which sweeper to look for.

bc.. class ArticlesController < ApplicationController
  cache_sweeper :article_sweeper

  ...
end