---
layout: post
title: Keep Mocking
description: "Mocking as I know"
headline: "Mocking as I know"
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

*What is mock ?*

A mock is an object which we can use on behalf of another object.

In short, mocking is creating (fake) objects that simulate the behavior of Real Objects.

An object under test may have dependencies on other (complex) objects. To isolate the behavior of the object you want to test you replace the other objects by mocks that simulate the behavior of the real objects.

*When to mock ?*

Mocking is required when real objects are impractical to incorporate into the unit test. Most of the time external services or methods we have to mock.  Once mocked, the dependencies behave the way we defined them. With the dependencies being controlled by mocks, we can easily test the behavior of the method that we coded.

Mocking particularly has effect when we are following "Behavior Driven Development BDD":http://en.wikipedia.org/wiki/Behavior-driven_development. Where a component is looked from Outside-In, visualizing the higher level of architecture and then moving into granular components of it.

Stubbing and Mocking makes your component examples independent of other components. You can stub methods on objects to let them return whatever you like. And you can use mock objects to replace instances of other classes.

*What is Stub?*

First thing to know about stub is: *They are not mocks*, and they arguably precede mocks in the history of TDD tooling. You can consider stub came before mock. We'll differentiate them shortly.

Stub provides a predefined response/result to a particular method call of an object.

Let's look at a simple example. @Receiver#message@ returns a hypothetical value. But we don't want to test actual behaviour of the method @message@, instead just verify @Receiver@ responds to it or not.

bc..  it "returns the specified value" do
    receiver = Receiver.new
    receiver.stub(:message).and_return(:return_value)
    receiver.message.should eq(:return_value)
  end  

bq. *Mock vs Stub*

This is one of the most discussed topic in TDD community and like wise has debatable answers.

An interesting "SO post to read":http://stackoverflow.com/questions/3459287/whats-the-difference-between-a-mock-stub highlights the theoretical differences between Mock and Stub.

*Ways of mocking and stubbing*

* @mock_model@ is rspec's generic way of mocking a model.
* Test doubles
* Method stubs

We'll consider a Project model which belongs to a user.

bc.. class Project < ActiveRecord::Base
  belong_to :user
  attr_accessible :name

  def name
    read_attribute(:name).presence || "#{user.fullo_name}'s project"
  end
end


bq. *mock_model*

We can use the @mock_model()@ method to provide a mock object that is configured to respond in this context as though it were an @ActiveRecord@ model.

bc.. describe Project do
  it 'should be named after the user if no name is set' do
    user = mock_model(User, full_name: 'Rob Stark')
    project = Project.new
    project.user = user
    project.name.should == "Rob Stark's project"
  end
end

bq. In the above example, we have mocked the User model and facilitated it to respond to full_name which we are calling in @Project#name@. As the execution will progress, @project.user@ call will actually return user object created through mock_model.

*Test Double*

A test double is an object that stands in for another object in an example

bc.. describe Project do
  it 'should be named after the user if no name is set' do
    project = Project.new
    project.stub user: double(full_name: 'Rob Stark')
    project.name.should == "Rob Stark's project"
  end
end

bq. This is a variant of the same test case, instead of mock_model we are using @double@ method of rspec. But we are not directly assigning the double object to user as it would give a type mismatch error(that means an ActiveRecord object is expected). Hence we need to stub the user call with @double(full_name: 'Rob Stark')@.

*Method stubs*

A method stub is a method that we can program to return predefined responses during the execution of a code example

bc.. describe Project do
  it 'should be named after the user if no name is set' do
    user = double('user')
    user.stub(:full_name).and_return('Rob Stark')
    project = Project.new
    project.stub(user: user)
    project.name.should == "Rob Stark's project"
  end
end

bq. This example specifies that the user object generates a @full_name@ in stub. The user double stands in for a real User @User@ object. Read More on "Method-Stubs":https://www.relishapp.com/rspec/rspec-mocks/v/2-3/docs/method-stubs


*Summary*
This concludes my article on *When to mock and How to mock*. If you still have a question like which way of 'mocking and stubbing' is prefered in what situation, then just follow the thumb rule:

If you are testing a higher level entity and are least worried about its specific behaviour use Mock.
And if you wish to test the details of a component and want to ignore the interactions with outside world, then go with stubbing.

Eventually you can have your own choice of doing things.
