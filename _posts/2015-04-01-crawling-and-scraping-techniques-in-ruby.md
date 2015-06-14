---
layout: post
title: Crawl -> Scrape -> Bite
description: "Crawling and Scraping Techniques in Ruby"
headline: "Crawl - Scrape - Bite"
categories: programming
tags: 
  - html
  - xml
imagefeature: "website-speed.jpg"
imagecredit: spreadeffect.com
imagecreditlink: "http://www.spreadeffect.com/blog/improve-website-speed/"
comments: false
mathjax: null
featured: true
published: true
---

_Problem case to solve_ : Crawl any given site(url), scrape each page uniquely for content from given selector or Xpath

*Available options for Crawling and Scraping :*

1. With "libxml2":http://xmlsoft.org/ "Nokogiri":https://github.com/sparklemotion/nokogiri is one of the fastest HTML/XML parser. Almost all of the scraping tools written in Ruby such as Mechanize, Wombat, Anemone, etc. uses Nokogiri as there base DSL for sraping.

2. "Mechanize":http://mechanize.rubyforge.org/ - Is a beast in this category. It efficiently uses Nokogiri for HTML scrapping. Do see Abi's post on "Automating browser navigation's through Script":/mechanize-automating-browser-navigation-s-through-script

3. "Wombat":https://github.com/felipecsl/wombat takes the scraping term to a next higher level, where we can specify the xpath along with CSS from which the content need to be extracted. The eye catcher from wombat is the structured data that it returns as a result post scraping i.e. is a Hash of links, subheadings and all the labels that we specified in the Crawl block. Simple example "here":https://github.com/felipecsl/wombat#scraping-a-page

4. Next is "Upton":https://github.com/propublica/upton - much similar to what Wombat does, Upton has an additional feature of saving the results of scrape in a file(.csv or any other desired format).

5. "Anemone":http://anemone.rubyforge.org/ is a simple Ruby library, that does crawls and scrapes websites in a multi-threaded fashion and thus is fast. The unique feature of Anemone is that it allows us to store data in one of the storage options such as
  Redis
  MongoDB
  TokyoCabinet
  PStore
Anemone also gives an option to ignore a path from the website

bc. skip_links_like

Hope this helps someone who is looking for crawling or scraping technique