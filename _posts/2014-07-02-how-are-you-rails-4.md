---
layout: post
title: How Are You Rails 4
description: "What came IN and What went OUT in Rails 4"
headline: "How Are You Rails 4"
categories: programming
tags: 
  - analytics
imagefeature: "website-speed.jpg"
imagecredit: spreadeffect.com
imagecreditlink: "http://www.spreadeffect.com/blog/improve-website-speed/"
comments: false
mathjax: null
featured: true
published: true
---

h3. Rails 4.0 brings in following upgrades
* Ruby 2.0 preferred; 1.9.3+ required.
* ActionPack page and action caching extracted to a separate gem.
* ActiveRecord observers extracted to a separate gem.
* ActiveRecord session store extracted to a separate gem.
* ActiveModel mass assignment protection - Rails 3 mass assignment protection is deprecated. Instead, use strong parameters.
* ActiveResource is extracted to a separate gem.
* vendor/plugins removed : Use a Gemfile to manage installed gems.

"Have a look at the major features of Rails 4":http://guides.rubyonrails.org/4_0_release_notes.html#major-features

We'll go through the notable and advanced features of Rails 4. Such as

* Routing concerns
* Live Streaming
* Declarative ETags
* New Postgres Features
* New ActiveRecord Features


h3. Features


h4. ActionPack

* *Strong parameters - Only allow whitelisted parameters to update model objects (params.permit(:title, :text)).*

Rails 3 way to white list attributes

bc. class Person < ActiveRecod::Base
  attr_accessible :name, :age
end

Rails 4 way to white list attributes

bc.. class PeopleController < ApplicationController
  def create
    Person.create(person_params)
  end

  private

  def person_params
    params.require(:person).permit(:name, :age)
  end
end

bq. "Read More on Strong Params - Rails Api":http://edgeapi.rubyonrails.org/classes/ActionController/StrongParameters.html

* *Routing concerns*

Routing Concerns allows you to declare common routes that can be reused inside other resources and routes

Example:
_The Rails 3 Way_

bc.. Older::Application.routes.draw do
  resources :projects do
    resources :comments
  end

  resources :tasks do
    resources :comments
  end

  resources :articles do
    resources :comments
  end
end

bq. _The Rails 4 Way_

bc.. Newer::Application.routes.draw do
  concern :commentable do
    resources :comments
  end

  resources :projects, concerns: :commentable
  resources :tasks, concerns: :commentable
  resources :articles, concerns: :commentable
end

bq. Using the routing method concern, we can now define common routes. All routes within a concern will not be included in your routes by default. The concern routes will only be included within a resource by passing it to the routing option :concerns. The :concerns option can accept one or more concerns.

* *"Live Streaming":http://edgeapi.rubyonrails.org/classes/ActionController/Live.html with @ActionController::Live@*

Rails 4.0 gives the ability to treat the response object as an I/O object, and have the data to be written immediately is available to the client. Essentially, the ability to deliver whatever data I want, whenever I want.

First we'll have a look at what is non-live streaming or Response Streaming

The first thing we'll add is a “stream” object to the response. This object is where data will be buffered until it is sent to the client. The stream object is meant to quack like an IO object. For example:

bc.. class MyController < ActionController::Base
  def index
    100.times {
      response.stream.write "hello world\n"
    }
    response.stream.close
  end
end

bq. To make live streaming work, we need to added a module called ActionController::Live. If you mix this module in to your controller, all actions in that controller can stream data to the client in real time. We can make the above MyController example live stream by mixing in the module like so:


bc.. class MyController < ActionController::Base
  include ActionController::Live

  def index
    100.times {
      response.stream.write "hello world\n"
    }
    response.stream.close
  end
end

bq. The code in our action stays exactly the same, but this time the data will be streamed to the client as every time we call the write method.

* *Declarative ETags*

ETag or entity tag is part of HTTP that is assigned by a web server to a specific version of a resource found at URL. In other words, ETAG is a key we use to determine whether a page has changed.

!http://www.bootyard.com/post_images/Etags/how-etags-work.png(Etags in Action)!

Here's a breakdown of how ETags works in Rails:

*First Request*

     #1 Render the entire response body

     #2 Create an ETag by doing a MD5 hash on the entire response body:

bc. header[‘Etag’] = Digest::MD5.hexdigest(body)

     #3 Send back to the client the response body and ETag included in the response

*Second Request on the same page*

     #1 Render the entire response body

     #2 Create ETag by doing a MD5 has on the entire response body

bc. header[‘Etag’] = Digest::MD5.hexdigest(body)

     #3 Compares ETag with what was sent over and what it generated

     #4 If ETags match then the response body is not included in the response, only the 304(Not Modified) response code


Rails 4 allows you to set controller-wide ETag suffixes. This technique is extremely useful when an action depends on an authenticated user. ETags (entity tags) make it even easier to ensure you’re taking advantage of HTTP freshness. Consider the following example:

Read more : "How ETags work in Rails 3 and Rails 4":https://tech.octanner.com/index.php/2013/06/18/browser-cache-how-etags-work-in-rails-3-and-rails-4/

h4. New Postgres Features

PostgreSQL has some pretty awesome "data types":http://www.postgresql.org/docs/9.3/static/datatype.html
Rails 4 has support for native datatypes in Postgres and we’ll show two of these here.
We can store arrays in a string-type column and specify the type for hstore.

* *Arrays*

You can use them with Rails 4 now, they should work mostly out of the box.

In your migrations you need to do:

bc.. t.string :tags, array: true, default: []
...
# and use your model
Post.create!(title: "PostgreSQL", tags: ["pg","rails4","rails"])
...
# and query it:
Post.where('tags @> ARRAY[?]', ['pg', 'rails4'])

bq. We can scope it in Post model as:

bc.. class Post < ActiveRecord::Base
  scope :any_tags, -> (tags){where('tags && ARRAY[?]', tags)}
  scope :all_tags, -> (tags){where('tags @> ARRAY[?]', tags)}
end

bq. We have used two operators to implement tagging:

bc.    A @> B: Does A contain all of B?

bc.    A && B: Does A overlaps any of B?


If you add "postgres_ext":https://github.com/dockyard/postgres_ext/ gem to your Gemfile, you can query it with "nicer syntax":https://github.com/dockyard/postgres_ext/blob/master/docs/querying.md#arrays.

Don’t just jump to using arrays instead of associations, it is a neat tool but performance implications and bad database designs are likely to happen if you overuse it.

* *Hashes*

"Hstore":http://www.postgresql.org/docs/9.3/static/hstore.html is data type that behaves like Ruby’s hash.

bc. #In you migrations
execute 'CREATE EXTENSION hstore'
...
# in create_table
t.hstore :dictionary
...
# And use it in your model
Post.create!(title: "foo", dictionary: {author: "Jim Kerry", topic: "Rails web development"})
...
# And query it
Post.where("dictionary -> 'author' = 'Jim Kerry'")

h4. New ActiveRecord Features

* *Null Object Pattern*

Being introduced in Rails 4 is ActiveRecord::QueryMethods.none, which implements the null object pattern. It is to be used in instances where you have a method which returns a relation, but there is a condition in which you do not want the database to be queried.

Example:

bc. Post.none.where(title: 'Rails 4 Demo')
# => #<ActiveRecord::Relation []>

This is useful in scenarios where you need a chainable response to a method or a scope that could return zero results.

For Example:

bc.. # The visible_posts method below is expected to return a Relation.
@posts = current_user.visible_posts.where(name: params[:name])

def visible_posts
  case role
  when 'Country Manager'
    Post.where(country: country)
  when 'Reviewer'
    Post.published
  when 'Bad User'
    Post.none # => returning [] or nil breaks the caller code in this case
  end
end

bq.

* *Scopes require a callable object*

Scopes have always been an area of frustration for new Rails developers. The biggest culprit of issues has to do with the eager-evaluation of scopes defined without a lambda. An example of this is time based conditions being eagerly-evaluated:

bc. scope :recent, where(published_at: Time.now - 2.weeks)

In Rails 4, all scopes must use a callable object such as a Proc or lambda:

Any future call to the recent scope, would always be 2 weeks before its initial evaluation.

bc. class Post < ActiveRecord::Base
  scope :recent, -> { where(published_at: Time.now - 2.weeks) }
end

* *destroy!*

Active Record now has a #destroy! method to compliment #create!. If a record cannot be destroyed, an @ActiveRecord::RecordNotDestroyed@ exception will be raised.

* *Default ordering added to first*

By calling first in previous versions of Rails, a simple LIMIT 1 would be added to the generated SQL query. This does not guarantee the record with the lowest primary key is returned.

Calling first in Rails 4 will add an implicit order to the SQL query. This ensures the record with the lowest primary key is returned first.

bc. Team.first
Rails 3: SELECT "teams".* FROM "teams" LIMIT 1
Rails 4: SELECT "teams".* FROM "teams" ORDER BY "teams"."id" ASC LIMIT 1

* *References*

The .includes query method is meant for the eager loading of associations. Some developers use the .includes method to perform some LEFT OUTER JOIN queries on associations using string SQL snippets. As of Rails 4, adding a string condition of the included association results in a deprecation warning.

Here is an example that selects all Teams which have a member named Batman:

bc.. Team.includes(:members).where('members.name = ?', 'Batman')

# Rails 3
SQL (0.7ms)  SELECT "teams"."id" AS t0_r0, "teams"."name" AS t0_r1, "teams"."created_at" AS t0_r2, "teams"."updated_at" AS t0_r3, "members"."id" AS t1_r0, "members"."name" AS t1_r1, "members"."bio" AS t1_r2, "members"."team_id" AS t1_r3, "members"."created_at" AS t1_r4, "members"."updated_at" AS t1_r5 FROM "teams" LEFT OUTER JOIN "members" ON "members"."team_id" = "teams"."id" WHERE (members.name = 'Batman')

# Rails 4
DEPRECATION WARNING: It looks like you are eager loading table(s) (one of: teams, members) that are referenced in a string SQL snippet.
...

bq. While the query still executes in Rails 4, it is recommended to include the new query method ActiveRecord::QueryMethods.references instead:

bc. Team.includes(:members).where("members.name = ?", 'Batman').references(:members)

* *Not Equal support for Active Record queries*

Example

Here's an example of executing the new not query method. First we will look at how it was done in Rails 3, and then look at how Rails 4 handles the same problem. All examples will be querying against an Article model with a title field. The examples below will be using a SQLite database.

The Rails 3 Way

bc. Article.where("title != ?", params[:title])
