---
title: Jekyll, the easy way installation, Ubuntu 14.04
description: How to install Jekyll without pain (the instruction for old Ubuntu distributives).
---
Installing ```jekyll``` on Ubuntu is not much pain, but I feel I should publish a recipe for Ubuntu 14.04 users (at least because I have one more Ubuntu machine),
which is based on [Github instructions](https://help.github.com/articles/using-jekyll-with-pages/).


Firstly, one needs to install Ruby **2** -- [ спасибо тебе, святой человек!](http://stackoverflow.com/a/33064386)

    sudo apt-add-repository ppa:brightbox/ruby-ng
    sudo apt-get update
    sudo apt-get install ruby2.2
    

Then ```bundler```

    
    sudo gem install bundler
    

Then devtools -- [ спасибо тебе, святой человек!](https://github.com/jekyll/jekyll/issues/3737) @chr-br
    
    sudo apt-get install ruby-dev ruby2.2-dev
    
Then create a Gemfile in the project directory
    
    source 'https://rubygems.org'
    gem 'github-pages'
    
Then ```cd``` into the project directory if you are not already there and execute
    
    bundler install
    

Then install some ```js``` runner
    
    apt-get install nodejs
    

Then you should be able to run
    
    
    bundle exec jekyll serve
    

This means everything is fine.
