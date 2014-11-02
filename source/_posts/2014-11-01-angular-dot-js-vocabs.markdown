---
layout: post
title: "Angular.js vocabs!"
date: 2014-11-01 20:41:53 -0700
comments: true
categories: angular javascript
---

I have been studying Angular all week, and what better than to read the [docs](https://docs.angularjs.org/api) and summarize some of the most commonly used terms in Angular. <!-- more -->

Controller: The controller contains business logic used to populate the scope with functions and values.

Scope: Scope is a container of functions and values originally defined in the controller. These functions and values can be binded into the views, within the scoped areas.

Directive: Directives are markers placed within the DOM. When a specific directive is activated, it will perform the behaviour that was defined in it. Angular comes with a lot of built-in directives, for example: `ng-controller="SomeController"` will create a scope defined in SomeController within the nested DOM elements, making those functions and properties available for use. In addition, custom directives can be defined.

Data binding: This is one of Angular's most powerful selling points. Data are synchronized both ways between the views and the models.

Module: Modules are simply containers of code, used for organizational purposes.

Dependency injection: Functions from other models can be transferred to another module via Dependency Injection.

I have only scratched the surface of Angular. Hopefully I'll have more posts about Angular as I continue to work with it.
