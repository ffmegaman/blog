---
layout: post
title: "Moved to Octopress on Dokku!"
date: 2014-04-26 20:10:41 +0900
comments: true
categories: ruby, octopress, jekyll
---

So I have moved away from Wordpress and now on the hacker-friendly Octopress running on the mini-Heroku, Dokku. I also changed host from Godaddy to DigitalOcean, and there is definitely a speed increase with their SSDs.

Immediately, I experienced an issue on my first push of Octopress to Dokku. When I tried to view the blog on my browser, I recieved a Nginx gateway error. That was easily fixed however by ensuring that `gem 'thin'` is in the gemfile. After that, I just ran bundle install, git commit, and pushed it up to dokku and all was fine.

Here is what my gemfile looks like now

``` ruby My Octopress Blog's Gemfile
gem 'rake', '~> 0.9'
gem 'jekyll', '~> 0.12'
gem 'rdiscount', '~> 2.0.7'
gem 'pygments.rb', '~> 0.3.4'
gem 'RedCloth', '~> 4.2.9'
gem 'haml', '~> 3.1.7'
gem 'compass', '~> 0.12.2'
gem 'sass', '~> 3.2'
gem 'sass-globbing', '~> 1.0.0'
gem 'rubypants', '~> 0.2.0'
gem 'rb-fsevent', '~> 0.9'
gem 'stringex', '~> 1.4.0'
gem 'liquid', '~> 2.3.0'
gem 'directory_watcher', '1.4.1'

group :production do
  gem 'thin' # Add this to your gemfile
end

gem 'sinatra', '~> 1.4.2'
```