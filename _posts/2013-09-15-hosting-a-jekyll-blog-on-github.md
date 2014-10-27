---
layout: post
title: Starting a Jekyll-Powerd Blgo and Hosting it on Github
date:   2013-09-15 23:25:51
categories: jekyll tutorial
---
In this post I'll go through how to set up a Jekyll blog and host it on Github.
I'm using Mac osx.

{% highlight bash %}
$ gem install jekyll
$ jekyll new andrewsturges
$ cd andrewsturges
$ jekyll serve
{% endhighlight %}
Now, in a browser, go to http://localhost:4000

What does `jekyll new andrewsturges` get you?
---------------------------------------------
When you run the command `jekyll new my_blog`, it creates a new folder called
'my_blog' and the folders and files listed below.

```bash
$  andrewsturges  tree .
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
```

A few things to note. First, you can ignore the entire `_site` directory.
The jekyll internals deal with that. Second, it gives you a _config.yml file
that has some pre-filled configuration options. These are directives that you
could otherwise include at the jekyll command line when you start the server.
For example, if in the _config.yml file you have the line

```ruby
favorite_color: green
```

then you could equivalently at the command line type

```bash
$ jekyll server --favorite_color 'green'
```

Third, it comes with some default CSS files. These are useable as-is, but probably
incomplete. For example, all headings h1 through h6 are identical. Fourth, it
has a _layouts folder, meaning you can modularize things like headers and
footers. Finally, notice that there's no _drafts folder. You can either create
one, or just keep your drafts in the master branch, and move them to the gh-pages
branch when they're ready to be published.

Make this folder into a Git repository
--------------------------

I want my blog to be its own git repository, called 'andrewsturges'. So I'm
going to initialize a new repo from inside the project folder:

```bash
$ git init .
Initialized empty Git repository in /Users/andrewsturges/andrewsturges/.git/
git:(master) $ git add .
git:(master) $ git commit -m "first jekyll commit"
[master (root-commit) a821078] first jekyll commit
 9 files changed, 378 insertions(+)
 create mode 100644 .gitignore
 create mode 100644 _config.yml
 create mode 100644 _layouts/default.html
 create mode 100644 _layouts/post.html
 create mode 100644 _posts/2013-09-15-hosting-a-jekyll-blog-on-github.md
 create mode 100644 _posts/2013-09-15-welcome-to-jekyll.markdown
 create mode 100755 css/main.css
 create mode 100644 css/syntax.css
 create mode 100644 index.html
git:(master) $ git remote add origin git@github.com:arsturges/andrewsturges.git
git:(master) $ git push -u origin master
Counting objects: 14, done.
Delta compression using up to 4 threads.
Compressing objects: 100% (13/13), done.
Writing objects: 100% (14/14), 4.47 KiB, done.
Total 14 (delta 0), reused 0 (delta 0)
To git@github.com:arsturges/andrewsturges.git
 * [new branch]      master -> master
Branch master set up to track remote branch master from origin.
```

The _site folder
---------------

The _site folder is managed by the `jekyll` program. We don't need to edit it
or worry about it all. It contains the HTML that actually gets served, but it's
updated each time we change the files in the rest of the directory structure.
In other words, that folder is dynamically re-generated whenever we change our
project. It won't get added to the GitHub repository, because it will be 
dynamically generated on the GitHub server when we activate GitHub pages. In
fact, `jekyll new <site_name>` command creates a .gitignore file with one line
for _site, so it's ignored by default.

Preparing for deployment to GitHub project mate
--------------------------------------------------

If we look at the url for our locally served site, we see something like this:

`http://localhost:4000/jekyll/update/2013/09/15/welcome-to-jekyll.html`

We have the host (localhost:4000), then the rest of the url (jekyll/update...).
This is slightly different from how GitHub pages will try to resolve the urls,
which is like this:  

`http://arsturges.github.io/andrewsturges/jekyll/update/2013/09/15/welcome-to-jekyll.html`

Notice there's the host (arsturges.github.io), then the name of the repository
(andrewsturges), THEN the rest of the url (/jekyll/update...).
To account for this, we need to take the following steps:

1. Edit the file `_config.yml` to include the following line:  
    ```ruby
    baseurl: /andrewsturges
    ```

1.  Change internal links  
    Any internal links, whether to static assets like CSS files or links between
    html pages, need to have this new baseurl prepended. We can do this by adding
    `{%raw%}{{ site.baseurl }}{%endraw%}`. Here's a before-and-after from the file _layouts/default.html:  
    Before:    

    ```html
    <link rel="stylesheet" href="/css/syntax.css">
    ```

    After:  

    ```html
    {%raw%}<link rel="stylesheet" href="{{ site.baseurl }}/css/syntax.css"> {%endraw%}
    ```
    You may have to do this to files in _layouts, as well as index.html.

1.  Run the local server with the `--baseurl ''` flag  
    Now when we run the local server, we don't want it to append '/andrewsturges'
    to everything, so we have to override the _config.yml setting. To start a 
    jekyll local server, we now do this:

    ```bash
    jekyll server --baseurl ''  
    ```

Deploying to GitHub
-------------------

We're going to deploy as a GitHub "project page", rather than a GitHub "user and
organization page". You can read about the difference in Jekyll's [GitHub Pages 
documentation](http://jekyllrb.com/docs/github-pages/). Suffice it to say, this
blog will have its own dedicated repository called 'andrewsturges'.

We've already created the jekyll site, made it into a git repository, and pushed
it to GitHub under the master branch (the default branch). To actually get it
published on GitHub, we need to push our jekyll site to a branch named gh-pages:

```
git:(master) $ git branch gh-pages
git:(master) $ git checkout gh-pages 
M   _posts/2013-09-15-hosting-a-jekyll-blog-on-github.md
Switched to branch 'gh-pages'
git:(gh-pages) $ git push origin gh-pages
Total 0 (delta 0), reused 0 (delta 0)
To git@github.com:arsturges/andrewsturges.git
 * [new branch]      gh-pages -> gh-pages
```

Now navigate to [http://arsturges.github.io/andrewsturges/](http://arsturges.github.io/andrewsturges/) and you should see the site.

Conclusion and Summary
=====================

The full list of steps looks something like this:

1. `$ gem install jekyll`
1. `$ jekyll new andrewsturges`
1. `$ cd andrewsturges`
1.  Create github repository called 'andrewsturges' to hold this blog
1. `$ git init .`
1. Add `baseurl: /andrewsturges` to _config.yml, and prepend `\{\{ baseurl\}\}` to
   internal links
1. `$ jekyll server --baseurl ''` to run local server; view at localhost:4000
1. `$ git add .`
1. `$ git commit -m "first jekyll commit"`
1. `$ git remote add origin git@github.com:arsturges/andrewsturges.git`
1. `$ git push -u origin master`
1. `$ git branch gh-pages`
1. `$ git checkout gh-pages`
1. `$ git push origin gh-pages` This will trigger GitHub to run a `jekyll build`
   on whatever is in your _posts folder, creating a new _site which gets
   published at [http://arsturges.github.io/andrewsturges/](http://arsturges.github.io/andrewsturges/).
