---
layout: post
title:  "Creating a blog with Jekyll on Windows and host it on Github"
date:   2016-12-15 13:51:59 +0200
categories: jekyll
---

# Why jekyll & github

I first tried to use wix as a blogging platform.
It is beautiful and easy to use.
The first problem was that it does not support syntax highlighting. 
Besides this it binds you to a specific platform and you start to pay very early for every single app ypu install.
What is nice with github that you develop everything locally and if you want to switch the provider you have everything on your machine.
There is syntax highlighting out the box and if you want to improve or change something you can write your own gem.
 


# Technical setup

## Installation process
As with most cool tools it is not fully supported on windows

I followed the link of https://labs.sverrirs.com/jekyll/ until  page 2  

{% highlight bash %}
 gem install jekyll bundler
{% endhighlight %}

The blog there is not up to date as with the new jekyll things change

Go into the directory where you want to create the blog

Run
{% highlight bash %}
 jekyll new .`
{% endhighlight %}

A new site is created.

The Gemfile file look like here:
{% highlight ruby %}
 source "https://rubygems.org"
 ruby RUBY_VERSION
 
 # Hello! This is where you manage which Jekyll version is used to run.
 # When you want to use a different version, change it below, save the
 # file and run `bundle install`. Run Jekyll with `bundle exec`, like so:
 #
 #     bundle exec jekyll serve
 #
 # This will help ensure the proper Jekyll version is running.
 # Happy Jekylling!
 gem "jekyll", "3.3.1"
 
 
 
 # This is the default theme for new Jekyll sites. You may change this to anything you like.
 gem "minima", "~> 2.0"
 
 # If you want to use GitHub Pages, remove the "gem "jekyll"" above and
 # uncomment the line below. To upgrade, run `bundle update github-pages`.
 #gem "github-pages", group: :jekyll_plugins
 
 # If you have any plugins, put them here!
 group :jekyll_plugins do
    gem "jekyll-feed", "~> 0.6"
 end

{% endhighlight %}


Do as he says and uncomment the line of gem github-pages and comment the gem jekyll line.
In additional for windows, add the wdm plugin

{% highlight ruby %}
source "https://rubygems.org"
ruby RUBY_VERSION

# Hello! This is where you manage which Jekyll version is used to run.
# When you want to use a different version, change it below, save the
# file and run `bundle install`. Run Jekyll with `bundle exec`, like so:
#
#     bundle exec jekyll serve
#
# This will help ensure the proper Jekyll version is running.
# Happy Jekylling!
#gem "jekyll", "3.3.1"

gem 'wdm', '~> 0.1.0' if Gem.win_platform?

# This is the default theme for new Jekyll sites. You may change this to anything you like.
gem "minima", "~> 2.0"

# If you want to use GitHub Pages, remove the "gem "jekyll"" above and
# uncomment the line below. To upgrade, run `bundle update github-pages`.
 gem "github-pages", group: :jekyll_plugins

# If you have any plugins, put them here!
group :jekyll_plugins do
   gem "jekyll-feed", "~> 0.6"
end

{% endhighlight %}

Afterwards don't forget to run

{% highlight bash %}
 bundle update
{% endhighlight %}


## _config.yaml

This is the file i got after running jekyll new, with few additions of mine
{% highlight yaml%}
# Welcome to Jekyll!
#
# This config file is meant for settings that affect your whole blog, values
# which you are expected to set up once and rarely edit after that. If you find
# yourself editing this file very often, consider using Jekyll's data files
# feature for the data you need to update frequently.
#
# For technical reasons, this file is *NOT* reloaded automatically when you use
# 'bundle exec jekyll serve'. If you change this file, please restart the server process.

# Site settings
# These are used to personalize your new site. If you look in the HTML files,
# you will see them accessed via {{ site.title }}, {{ site.email }}, and so on.
# You can create any custom variable you would like, and they will be accessible
# in the templates via {{ site.myvariable }}.
title: #Add here the title of the blog 
email:  #Add here your email
description: > # this means to ignore newlines until "baseurl:"
  Add here the description of your blog
baseurl: "" # the subpath of your site, e.g. /blog
url: # the base hostname & protocol for your site, e.g. http://example.com. This is very important for disqus integration
twitter_username: # Your twitter username
github_username:  # Your github username
repository: #your github name followed by slash and then followed by the github name

# Build settings
markdown: kramdown
theme: minima
gems:
  - jekyll-feed
exclude:
  - Gemfile
  - Gemfile.lock

disqus:
    shortname: # Your disqus short name for comment integration
{% endhighlight %}


As always if you want that the github fetching of metadata will work, you have to work according to [this link](https://talk.jekyllrb.com/t/github-metadata-no-github-api-authentication-could-be-found-some-fields-may-be-missing-or-have-incorrect-data/2602/4)

## Develop locally

For some reaon jekyll serve does not work
It fails with 

{% highlight bash %}
jekyll serve
WARN: Unresolved specs during Gem::Specification.reset:
      listen (< 3.1, ~> 3.0)
WARN: Clearing out unresolved specs.
Please report a bug if this causes problems.
C:/Ruby22-x64/lib/ruby/gems/2.2.0/gems/bundler-1.13.6/lib/bundler/runtime.rb:40:in `block in setup': You have already activated json 2.0.2, but your Gemfile requires json 1.8.3. Prepending `bundle exec` to your command may solve t
his. (Gem::LoadError)
        from C:/Ruby22-x64/lib/ruby/gems/2.2.0/gems/bundler-1.13.6/lib/bundler/runtime.rb:25:in `map'
        from C:/Ruby22-x64/lib/ruby/gems/2.2.0/gems/bundler-1.13.6/lib/bundler/runtime.rb:25:in `setup'
        from C:/Ruby22-x64/lib/ruby/gems/2.2.0/gems/bundler-1.13.6/lib/bundler.rb:99:in `setup'
        from C:/Ruby22-x64/lib/ruby/gems/2.2.0/gems/jekyll-3.3.1/lib/jekyll/plugin_manager.rb:36:in `require_from_bundler'
        from C:/Ruby22-x64/lib/ruby/gems/2.2.0/gems/jekyll-3.3.1/exe/jekyll:9:in `<top (required)>'
        from C:/Ruby22-x64/bin/jekyll:23:in `load'
        from C:/Ruby22-x64/bin/jekyll:23:in `<main>'
{% endhighlight %}

When changing to 

{% highlight bash %}
bundle exec jekyll serve
{% endhighlight %}

it works

# Deploy it to github

* Create a github repository with the name <your user name>.github.io

* Connect it with your local repository and push all changes.

* visit <your user name>.github.io and tada you are done

