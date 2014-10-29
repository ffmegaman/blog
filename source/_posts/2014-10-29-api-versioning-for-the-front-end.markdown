---
layout: post
title: "API versioning for the front end"
date: 2014-10-29 16:33:58 -0700
comments: true
categories: ruby rails api versioning angular codefellows
---

First of all, this is not a post comparing the different types of strategies. While there are many types of API versioning, and a great blog post about each of those strategies have been discussed by [Michael Pratt](http://urthen.github.io/2013/05/09/ways-to-version-your-api/), I'm just going to focus on the strategy that I have chosen for my project. <!-- more -->

I'm currently working on an Angular.js project here at Code Fellows, and in order to get Angular working with Rails, we need to talk about API's. In that sense, we need to talk about an API versioning strategy. Since this project is small and the only client that will be talking to my backend API is the front-end Angular app, it more or less narrowed down my choices.

Adding a /api_v1/ to my URI is the simplest. While this could be a headache when you are changing versions regularly, it isn't much for me when I am the only client consuming the data. For projects with multiple API consumers, you have to keep multiple versions of the API so you don't break them. In rails, by embedding the api version into the URI, you may end up having a ton of api version controllers, and that can be a pain!

In my case, since I am in full controll of both the front-end and the back-end, changing versions and breaking people's projects is not an issue for me. For all I know, I'll be on /api_v1/ for a very long time.
