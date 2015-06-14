---
layout: post
title: Integration Testing
description: "Integration Testing"
headline: "Integration Testing"
categories: programming
tags: 
  - tdd
imagefeature: "website-speed.jpg"
imagecredit: spreadeffect.com
imagecreditlink: "http://www.spreadeffect.com/blog/improve-website-speed/"
comments: false
mathjax: null
featured: true
published: true
---

h4. What does Integration testing mean ?

Wiki says: Integration testing is the phase in software testing in which individual software modules are combined and tested as a group. "Read More":http://en.wikipedia.org/wiki/Integration_testing

When a software grows(may it be written in Ruby or Java or any other language for that matter), stability can become an issue as the application evolve and grow. Integration tests provide a great way to perform end-to-end tests that validate the application is performing as expected.

The BDD-TDD life cycle goes as follows:

!http://keyholesoftware.com/wp-content/uploads/BDD.jpg(TDD-BDD lifecycle)!

Integration relates more towards BDD, so let's briefly get to know how BDD works.

When your development is Behavior-driven, you always start with the piece of functionality that’s most important to your user. This phase should be considered as, taking the developer hat off and putting the user hat on. Once you’ve specified the user needs, you put the developer hat back on and implement your specification.

Integration or Acceptance test are high level (typically user-level) tests done using tools like

* "Test::Unit":http://ruby-doc.org/stdlib-2.1.1/libdoc/test/unit/rdoc/
* "MiniTest":http://www.ruby-doc.org/stdlib-1.9.3/libdoc/minitest/unit/rdoc/
* RSpec and Capybara
* Rspec and Cucumber or Selenium

h4. RSpec and Capybara

We'll first have a look at how to write integration tests with Rspec and Capybara. When writing integration tests, try to model the test around an actor (user of the system) and the action they are performing.

bc.. # spec/features/user_log_in_spec.rb
require 'spec_helper'

feature 'User Log In' do
  scenario 'with valid email and password' do
    log_in_with 'hello@example.com', 'password'

    expect(page).to have_content('hello@example.com')
    expect(page).to have_content('Log out')
  end

  scenario 'with invalid email' do
    log_in_with 'invalid_email', 'password'

    expect(page).to have_content('Log in')
  end

  scenario 'with blank password' do
    log_in_with 'hello@example.com', ''

    expect(page).to have_content('Log In')
  end

  def log_in_with(email, password)
    visit new_user_session_path
    fill_in 'Email', with: email
    fill_in 'Password', with: password
    click_button 'Log In'
  end
end

bq. *Running the integration tests*

bc.. $ rspec -fd
User Log In
  with valid email and password
  with invalid email
  with blank password

Finished in 0.35837 seconds
3 examples, 0 failures

bq. Here's a nice slideshare on "rspec and capybara":http://www.slideshare.net/bsbodden/rspec-and-capybara do go through it.

h4. Rspec and Cucumber

"Cucumber":http://cukes.info/ helps to collaborate and communicate stake-holders with delivery team. With Cucumber one can simply describe the expected scenario or test case, it uses a simple language for describing scenarios that can be written and read with ease by both technical and nontechnical people. These scenarios represent customer acceptance tests and are used to automate the system we’re developing.

Cucumber is an amazing integration testing tool, with its simple DSL it adds up to the power. The syntax used in Cucumber is called as "Gherkin syntax":https://github.com/cucumber/cucumber/wiki/Gherkin. We'll have a look at the syntax in following example of user_log_in_spec

*Installation*

We'll be using "cucumber-rails":https://github.com/cucumber/cucumber-rails that works great with rspec.

bc. rails generate cucumber:install --rspec

Will create the required directories and files(@features/step_definitions@ and @features/support@) under @spec/@ folder.

*It works in 3 steps:*

1: Describe behaviour in plain text
2: Write a step definition in Ruby(we use rspec)
3: Run and watch it fail/pass

Our user_log_in_spec can be written in Cucumber as :

*1. Describe behaviour in plain text*

The behaviour or scenario to be tested is written in "Gherkin syntax":https://github.com/cucumber/cucumber/wiki/Gherkin. Scenarios are concrete examples of how we want the software/application to behave. They are more explicit than some traditional ways to describe requirements and help us raise and answer questions that we might miss otherwise. Consider this requirement: _it should be possible for a user to login with valid credentials_.

bc.. Feature: User Log In
 Scenario: with valid data
   When I log in with "hello@example.com" and "password"
   Then I should be logged in 

bq. *2. Write a step definition in Ruby*

Step definitions are Cucumber's equivalent of method definitions or function declarations in a conventional programming language. In our example we have define them in Ruby and they are invoked when Cucumber parses steps in the plain-text features(i.e. step #1).

bc.. # Cucumber step definition
 When /^I log in (?:with|as) "(.*)" and "(.*)"$/ do |email, password| 
   visit new_user_session_path 
   page.should have_css("input[type='email']") 
   fill_in "Email", :with => email 
   fill_in "Password", :with => password 
   click_button "Log In" 
 end

bq. *3. Run and watch it fail/pass*

To execute the cucumber examples simply run:

bc. $ rake cuccumber

The output should be something like:


bc.. Feature: User Log In
 Scenario: with valid data
   When I log in with "hello@example.com" and "password"
   Then I should be logged in 

1 scenario (1 passed)
1 step (1 passed)
0m0.002s

bq. To learn more on cucumber, "here's a healthy wiki for blog posts"::https://github.com/cucumber/cucumber/wiki/tutorials-and-related-blog-posts
