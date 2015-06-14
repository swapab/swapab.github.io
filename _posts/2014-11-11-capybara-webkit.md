---
layout: post
title: Capybara-Webkit
description: "How it works and its anomolies"
headline: "Webkit <3"
categories: programming
tags: 
  - webkit
imagefeature: "website-speed.jpg"
imagecredit: spreadeffect.com
imagecreditlink: "http://www.spreadeffect.com/blog/improve-website-speed/"
comments: false
mathjax: null
featured: true
published: true
---

This post is to explain some insights of "capybara-webkit":https://github.com/thoughtbot/capybara-webkit and its functional setup.

*What is capybara-webkit ?*

Capybara-webkit is used for acceptance tests in our rails 3 application and it turned out to be non trivial, even though there are excellent tools out there, and they keep getting better.

WebKit is an open source web browser engine and capybara-webkit depends on a WebKit implementation from Qt, a cross-platform development toolkit.

Using capybara with the capybara-webkit driver, works great because it runs in headless mode, without annoying browser windows popping up.

*How to setup ?*

bc. gem "capybara-webkit"

Set javascript driver to webkit in your test helper

bc. Capybara.javascript_driver = :webkit

In RSpec, use the @:js => true@ flag.

That's it your test will now run with javascript/ajax.

*Anomolies*

At some point it may happen that your js tests no longer passes.
One of the reasons might be the transactional fixtures being turned on.
i.e. spec_helper.rb may look like this

bc. Rspec.configure do |config|
  config.use_transactional_fixtures = true
  ...
  ##rest of the configs
  ...
end

What is @config.use_transaction_fixures@ ? The name is misleading, it has nothing to do with fixtures instead this configuration runs every test in a transaction and rollbacks the transaction after every test so you have a clean database for further test cases.

_This possess a hurdle to our webkit driver._

Our capybara-webkit driver runs in a different process irrelevant to the rake_test environment. Hence the transactions happening in rake test are unknown to webkit and objects are not available for webkit.
There are 2 ways to solve this issue.

First, turn off transactional fixtures

bc. config.use_transactional_fixtures = false

and use a cleanup strategy provided by "database_cleaner":https://github.com/bmabey/database_cleaner

Second, share the active-record connection between rake_test and webkit

bc.. class ActiveRecord::Base
  mattr_accessor :shared_connection
  @@shared_connection = nil

  def self.connection
  @@shared_connection || retrieve_connection
  end
end
ActiveRecord::Base.shared_connection = ActiveRecord::Base.connection

bq. Append your test helper with this patch. Now your tests will share the database connection if one exist.
A drawback of this patch is that it may not work for parallel testing, since only one connection will be available everytime.

*Conclusion*
I hope it helps. Also it boost the suite performance.
That's it!
Happy Testing :)