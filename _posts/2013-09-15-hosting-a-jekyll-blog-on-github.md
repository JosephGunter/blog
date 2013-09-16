---
layout: post
title: Starting a Jekyll-Powerd Blgo and Hosting it on Github
date:   2013-09-15 23:25:51
categories: jekyll tutorial
---
In this post I'll go through how to set up a Jekyll blog and host it on Github.
I'm using Mac osx.
{% highlight bash %}
~ $ gem install jekyll
~ $ jekyll new andrewsturges
~ $ cd andrewsturges
~/andrewsturges $ jekyll serve
{% endhighlight %}
Now, in a browser, go to http://localhost:4000

What does `jekyll new andrewsturges` get you?
---------------------------------------------
When you run the command `jekyll new my_blog`, it creates a new folder called
'my_blog' and the folders and files listed below.

{% highlight bash %}
➜  andrewsturges  tree .
.
├── _config.yml
├── _layouts
│   ├── default.html
│   └── post.html
├── _posts
│   ├── 2013-09-15-welcome-to-jekyll.markdown
├── _site
│   ├── css
│   │   ├── main.css
│   │   └── syntax.css
│   ├── index.html
│   └── jekyll
│       └── update
│           └── 2013
│               └── 09
│                   └── 15
│                       └── welcome-to-jekyll.html
├── css
│   ├── main.css
│   └── syntax.css
└── index.html
{% endhighlight %}

##Make this folder into a Git repository
I want my blog to be its own git repository, called 'andrewsturges'. So I'm
going to initialize a new repo from inside the project folder:
{% highlight bash %}
{% endhighlight %}

## The _site folder
The _site folder is managed by the `jekyll` program. We don't need to edit it
or worry about it all. It contains the HTML that actually gets served, but it's
updated each time we change the files in the rest of the directory structure.
In other words, that folder is dynamically re-generated whenever we change our
project. It won't get added to the GitHub repository, because it will be 
dynamically generated on the GitHub server when we activate GitHub pages.

## Deploying to GitHub
We're going to deploy as a GitHub "project page", rather than a GitHub "user and
organization page". You can read about the difference in Jekyll's [GitHub Pages documentation](http://jekyllrb.com/docs/github-pages/).
