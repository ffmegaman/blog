---
layout: post
title: "Opening up Pundit"
date: 2014-10-05 21:24:02 -0700
comments: true
categories:
---

I recently used [Pundit](https://github.com/elabs/pundit) as an authorization tool as part of my assignments at Code Fellows. I've been told that it is extremely lightweight, but I had no idea just how lightweight it was until I opened it up and went through the code line by line.

Pundit provides a `module Pundit`, with all the logic in it. There is also a `class PolicyFinder` within the module that gives Pundit its capability to match controller actions and objects to its proper class policies.<!-- more -->

Probably the best way to understand Pundit is to start with one of the helper methods that it provides and follow along with its code executions. The most commonly used method that Pundit provides is `authorize`. Let's take a look at it. I've added comments to the original code to give some additional information.

`authorize` is defined as:
``` ruby
def authorize(record, query=nil)
  query ||= params[:action].to_s + "?" # Appends ? to name of controller action
  @_policy_authorized = true # Flag to be used in #verify_authorized

  policy = policy(record) # calls policy with the record object and through other helper_methods, it instantiates the class policy. For example, ArticlePolicy.new(user, record)

  unless policy.public_send(query) # sends method? to the class policy. For example, it sends #create? to ArticlePolicy. If it is false, raise errors, otherwise return true.
    error = NotAuthorizedError.new("not allowed to #{query} this #{record}")
    error.query, error.record, error.policy = query, record, policy

    raise error
  end

  true
end
```
Authorize takes a parameter, record, which is an object that needs to be checked for authorization. The first two lines are self explanatory. The meat of it is when it does `policy = policy(record)`. the #policy(record) method is another helper method defined as:
``` ruby
def policy(record) # Accepts the record and calls singleton #policy! on Pundit.
  @_policy or Pundit.policy!(pundit_user, record)
end
```
Here, it uses either an already provided policy (`@_policy`), or it will call the singleton method `#policy!(pundit_user, record)` on the `module Pundit`. `pundit_user` is simply defined as `current_user`. Let's take a look at the singleton method `#policy!`
``` ruby
class << self # Opens up Pundit's singletons. All methods inside are singletons

  #... other code ...

  def policy!(user, record) # Finds the class policy and calls #new. Example: ArticlePolicy.new(user, record) and then admin's codes take over.
    PolicyFinder.new(record).policy!.new(user, record)
  end
```
It instantiates `PolicyFinder` with the `record` argument, calls `#policy!`, and then it instantiates the result of that. Yes, it is a bit confusing if we stop here. Essentially, what it is doing is that the `PolicyFinder`, a class inside the Pundit module, is tasked with the sole purpose of finding a matching class policy. For example, if the record object is an `@article` object that is called within the `ArticleController`, `PolicyFinder.new(@article).policy!` will find the corresponding `ArticlePolicy`. Once it is found, this will occur: `ArticlePolicy.new(user, @article)`. `ArticlePolicy` is where all of the rules will be custom defined by the administrator of the Rails app.

If you don't believe me, we can dive deeper by taking a look at the `#policy!(user, record)` inside the `class PolicyFinder`:
``` ruby
module Pundit
  class PolicyFinder

  # ... Other method definitions....

    def policy! # Calls policy if it exists or raise error
      policy or raise NotDefinedError, "unable to find policy #{find} for #{object}"
    end

  # ... other definitions ...
```
Again, `#policy!` is another helper method to raise an error if there is no policy, otherwise call `#policy`, which is also found inside the `class PolicyFinder`:
``` ruby
module Pundit
  class PolicyFinder

  # ... Other method definitions ...

    def policy # Finds the class and then returns the class policy.
      klass = find
      klass = klass.constantize if klass.is_a?(String)
      klass # Returns policy class. Ex) ArticlePolicy
    rescue NameError
      nil
    end

  # ... other method definitions ...
```
Here, it uses the `find` method to find the name of the class policy and if it is a string, it will constantize it. Otherwise it returns nil if it could not return the name of the class policy.

The `find` method is defined as the following (I added in a lot of comments to help you along the way):
``` ruby
module Pundit
  class PolicyFinder

    # ... Other method definitions ...
    # The first two conditions will fail if policy_class is not defined in the model. Policy_class is used when you want to have an object associated with a differently named policy.

    # The following conditions will prepend the class name to the policy. For instance, Article will be prepend into 'Policy' making it ArticlePolicy.
    def find
      if object.respond_to?(:policy_class)
        object.policy_class # Returns WhateverPolicy if policy_class is defined in the object's model.
      elsif object.class.respond_to?(:policy_class)
        object.class.policy_class # Returns WhateverPolicy if policy_class is defined in the object's model.
      else  # These conditions below is for objects queried
        klass = if object.respond_to?(:model_name)
          object.model_name # model_name is a method in rails.
        elsif object.class.respond_to?(:model_name)
          object.class.model_name # model_name is a method in rails.
        elsif object.is_a?(Class)
          object
        elsif object.is_a?(Symbol)
          object.to_s.classify
        else
          object.class
        end
        "#{klass}Policy"
      end
    end
  end
end
```
Basically, all it is doing is appending 'Policy' to the name of the object. The administrator may have put in different types of objects as an argument, or perhaps the administrator wanted a different policy for that object, the multiple conditions are done to check for them. In the end, if the object is @article, it finds the class of that, which is Article, and appends it to ArticlePolicy.

Now back to the singleton method `#policy(user, record)`!
``` ruby
class << self # Opens up Pundit's singletons. All methods inside are singletons

  #... other code ...

  def policy!(user, record) # Finds the class policy and calls #new. Example: ArticlePolicy.new(user, record) and then admin's codes take over.
    PolicyFinder.new(record).policy!.new(user, record)
  end
```
If we are doing `Article`, `PolicyFinder.new(record).policy!` returns `ArticlePolicy` which is then instantiated with `new(user, record)`. The contents of `ArticlePolicy` should be defined by the administrator inside the Rails app itself. The last and final step is to go back to the `authorize` method and check to see if the code defined by the administrator evaluates to true or false:
``` ruby
def authorize(record, query=nil)
  query ||= params[:action].to_s + "?" # Name of controller action
  @_policy_authorized = true # Flag to be used in #verify_authorized

  policy = policy(record) # calls policy with the record object and through other helper_methods, it instantiates the class policy. For example, ArticlePolicy.new(user, record)

  unless policy.public_send(query) # sends method? to the class policy. For example, it sends #create? to ArticlePolicy. If it is false, raise errors, otherwise return true.
    error = NotAuthorizedError.new("not allowed to #{query} this #{record}")
    error.query, error.record, error.policy = query, record, policy

    raise error
  end

  true
end
```
Policy is the instance of ArticlePolicy and unless `policy.public_send(query)`, for example if query is `create?`, is false, it raises an unauthorized error. Otherwise, it returns true and allows the user to perform the action.

There are two other commonly used methods provided by Pundit, they are `policy(record)` and `policy_scope(scope)`. Both of the methods follow a similar path as the `authorize` method, with only a few deviations. Otherwise, the meat of pundit has been covered.
