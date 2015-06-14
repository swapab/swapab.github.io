---
layout: post
title: Why TDD & How TDD
description: "Why and How to write tests"
headline: "Feeling Confident"
categories: programming
tags: 
  - tdd
  - rails
imagefeature: "website-speed.jpg"
imagecredit: spreadeffect.com
imagecreditlink: "http://www.spreadeffect.com/blog/improve-website-speed/"
comments: false
mathjax: null
featured: true
published: true
---

*Why write tests ?*

Some answers might be
_Because I have been told by my manager_
Some could be
_yay! let's bolt on writing tests because it's cool_

But some meaningful reasons could be
_Test-Driven Development is a developer practice that involves writing 
tests before writing the code being tested. Begin by writing a very 
small test for code that does not yet exist. Run the test, and, 
naturally, it fails. Now write just enough code to make that test pass. 
No more._

_It gives a strong base for you project or library or even a framework 
to build upon *flawlessly*_

_Prominent Design_

Over the time every code base increases in size, eventually most of the 
time is consumed by the refactoring step. The design is constantly 
evolving and under constant review, though it is not predetermined. 
Testing gives a prominent design approach and is one of the most 
significant by-products of Test-Driven Development.

_Change Fearlessly_

Having a sufficient test suite will give your the confidence to making a 
change and ensuring the build is green before deploying your change.

*How to write tests ?*

The basic idea behind testing(with rspec or any other tool) is, it 
should be seen as design tool rather than a testing tool. When you write 
a test case it resembles your design and how the design works. Instead 
of adding more code, document the next responsibility in the form of the 
next test. Run it, watch it fail, write just enough code to get it to 
pass, review the design, and remove duplication. Now add the next test, 
and repeat the cycle which we refer as _red/green/refactor_

*Lets take an example of @Article@ class*

We'll use rspec for testing, setup of which is out of scope for this topic.

bc.. require 'spec_helper'

describe Article do

end

bq. This is how we start with a describe block. Our parameter to 
describe explains what we're testing: this could be a string, but in our 
case we're using the class name. "Read More on 
describe":https://www.relishapp.com/rspec/rspec-core/v/3-0/docs/example-groups/basic-structure-describe-it

bc.. before :each do
@article = Article.new "Title", "Author", :category
end

bq. We'll begin by making a call to before we pass the symbol :each to 
specify that we want this code run before each test (we could also do 
:all to run it once before all tests). Use before and after hooks to 
execute arbitrary code before and/or after the body of an example is 
run. "Read 
More":https://www.relishapp.com/rspec/rspec-core/v/3-0/docs/hooks/before-and-after-hooks

bc.. describe "#new" do
it "takes three parameters and returns an Article object" do
@article.should be_an_instance_of Article
end
end

bq. We have our first test ready. Our test simply confirms that we 
indeed made an Article object. We're using a nested describe block here 
to say we're describing the actions of a specific method. You might have 
noticed the string "#new"; it's a convention in Ruby to refer instance 
methods like this: ClassName#methodName. Since we have the class name in 
our top-level describe, we're just putting the method name here.

Now fire up a terminal cd to the project directory and run @rspec spec@
You should see output saying something about "uninitialized constant 
Object::Article"; this just means there's no Article class. Let's fix that.

As per TDD, we only want to write enough code to fix this problem. In 
the article.rb file, that would be this:

bc.. class Article

end

bq. Re-run the test (rspec spec), and you’ll find it's passing fine. Green

Some more tests for @Article@

bc.. describe "#title" do
it "returns the correct title" do
@article.title.should eql "Title"
end
end

describe "#author" do
it "returns the correct author" do
@article.author.should eql "Author"
end
end

describe "#category" do
it "returns the correct category" do
@article.category.should eql :category
end
end

bq. These will fail, as the Article object doesn't respond to title, 
author, category. So here’s the code for Article to make them pass:

class Article
attr_accessor :title, :author, :category
def initialize(title, author, category)
@title = title
@author = author
@category = category
end
end

bq. We can take this further to create a @Magazine@ of Articles. Where a 
magazine would comprise of many articles.
And we could write up more tests and add a lot of other functionality, 
but we'll confine it here.
