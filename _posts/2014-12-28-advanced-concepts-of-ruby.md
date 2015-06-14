---
layout: post
title: Advanced concepts of Ruby
description: "Discovering Ruby"
headline: "Let's do it"
categories: programming
tags: 
  - ruby
imagefeature: "website-speed.jpg"
imagecredit: spreadeffect.com
imagecreditlink: "http://www.spreadeffect.com/blog/improve-website-speed/"
comments: false
mathjax: null
featured: true
published: true
---

"Ruby""https://www.ruby-lang.org/en/about/ is a dynamic, open source programming language with a focus on simplicity and productivity. It has an elegant syntax that is natural to read and easy to write.

Everything in Ruby is an @Object@.
1.9.3-p429 :013 > Post.new.object_id
 => 84004800
1.9.3-p429 :014 > Post.object_id
 => 83290440

As the above example explains, you can run all the same methods on classes that you can run on object. @Post.new@ and @Post@ each have their own ID in the symbol table.

h4. Metaclasses

The term metaclass is supposed to mean "a class which defines classes." This definition doesn't really work with Ruby, though, since "a class which defines a class" is simply: a Class.

If you have came across something like @class << self@ then you have used or seen @Metaclass@.

A simple Person would demonstrate this.

bc. class Person
  class << self
    def name
      'Person'
    end
  end
end

Here we are declaring a Person and redefining itself with a class method @name@.
Calling the method name would return the string.

bc. 1.9.3-p429 :014 > Person.name
 => "Person"

You're constantly defining and redefining classes in Ruby. It's not meta, it's just part of the code. Classes hold methods. We can think of the Ruby metaclass as "a class which an object uses to redefine itself."

h4. Blocks, Procs and Lambdas

*Ruby Blocks*

Ruby Code blocks (called closures in other languages) are definitely one of the coolest features of Ruby and are chunks of code between braces or between do-end that you can associate with method invocations, almost as if they were parameters.

Code blocks are just chunks of code between braces or between do and end.
This is a code block:

bc. { puts "Hello" }

This is also a code block:
bc. do
  club.enroll(person)
  person.socialize
end

_Whats the difference?_

* They have different precedences. This means that {} has a higher precedence than do..end.
* Use cases differs as : braces for single-line blocks and do/end for multiline blocks.

*Procs*

Proc objects are blocks of code that have been bound to a set of local variables. Once bound, the code may be called in different contexts and still access those variables.

bc.. def gen_times(factor)
  return Proc.new {|n| n*factor }
end

_Calling a Proc_

You call a proc by invoking its methods call, yield, or []. The three forms are identical. Each takes arguments that are passed to the proc, just as if it were a regular method.

times3 = gen_times(3)
times5 = gen_times(5)

times3.call(12) # => 36
times5.call(5) # => 25

*lambda*

It’s a procedure (Proc) but with required parameter checking.

Also there is another difference between lambda and Proc. Proc's "return" will stop method and will return from Proc on the other hand lambda's "return" will only return it’s own value to the caller method.

bc.. l = lambda { |a, b| ... }
l.call('A')
#=> Error need to pass 2 arguments

def hello?
  l = Proc.new { return "World" }
  l.call
  return "World End"
end

#=> "World"

bq. Because Proc's return is just like method's own return.

Here's a nice article read about "What Is the Difference Between a Block, a Proc, and a Lambda in Ruby?":http://awaxman11.github.io/blog/2013/08/05/what-is-the-difference-between-a-block/

h4. Spat(*)

As of ruby 1.9, you can do some pretty odd things with array destructuring and splatting. Putting the star before an object invokes the splat operator, which has a variety of effects

bc. a, *b = 1, 2, 3 # a=1, b=[2, 3]
bc. a, *b = 1  # a=1, b=[]

If the splat is not the last left-value, then Ruby ensures that the left-value that follow it will all receive values from right-value at the end of the right side of the assignment—the splat left-value will soak up only enough right-value to leave one for each of the remaining left-value.

h4. Ruby's Garbage collector

Garbage collection (GC) finds unused data objects and reclaims that memory space for use by another process.
Garbage Collection in Ruby is managed by "GC module":http://ruby-doc.org/core-1.9.3/GC.html

Garbage collection in Ruby had evolved over the time.

The algorithms used were different in version 1.8, 1.9, 2.0 and 2.1

"Read More on Difference in algorithms":http://tmm1.net/ruby21-rgengc/
