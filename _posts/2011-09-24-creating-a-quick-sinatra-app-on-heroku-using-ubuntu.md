---
layout: post
title: Creating a quick Sinatra app on Heroku using Ubuntu 10.10
date: 2011-09-24 19:53:18
categories: sinatra ruby
---

In this post I will explain how to create a simple, static Sinatra app and push it to Heroku via GitHub. I'm using:

* Ubuntu 10.10
* RVM 1.6.14
* Ruby 1.9.2
* Git 1.7.1

I'm assuming a few things:

* You have RVM, Ruby, RubyGems, and Git installed, and
* You have a GitHub account.

Create a project directory:

```bash
$ mkdir nanadebs
$ cd nanadebs
$ rvm gemset create nanadebs
$ rvm use 1.9.2@nanadebs # use the gemset you just created
$ gem install sinatra
Fetching: rack-1.3.0.gem (100%)
Fetching: tilt-1.3.2.gem (100%)
Fetching: sinatra-1.2.6.gem (100%)
Successfully installed rack-1.3.0
Successfully installed tilt-1.3.2
Successfully installed sinatra-1.2.6
3 gems installed
Installing ri documentation for rack-1.3.0...
Installing ri documentation for tilt-1.3.2...
Installing ri documentation for sinatra-1.2.6...
Installing RDoc documentation for rack-1.3.0...
Installing RDoc documentation for tilt-1.3.2...
Installing RDoc documentation for sinatra-1.2.6...
```

Now create a file called "home.rb" and write the following code:

```ruby 
require 'rubygems'
require 'sinatra'
get '/' do
  "Nana Deb's Aprons"
end
```

Save the file, return to the command prompt run the file you just created:

```bash
$ ruby home.rb
== Sinatra/1.2.6 has taken the stage on 4567 for development with backup from WEBrick
[2011-06-20 20:45:31] INFO  WEBrick 1.3.1
[2011-06-20 20:45:31] INFO  ruby 1.9.2 (2011-02-18) [i686-linux]
[2011-06-20 20:45:31] INFO  WEBrick::HTTPServer#start: pid=3003 port=4567
```

Go to a web browser and navigate to http://localhost:4567. You should see nothing but the 
text "Nana Deb's Apron's". If you view the source code of the web page from the browser 
(in Firefox use ctrl-u), you should see source code with nothing but the text (wait for it) 
"Nana Deb's Aprons". The point is, there's no markup, such as html tags like <body> or <doctype>. 
The browser sees only what we print to it, which in this case is anything inside those 
quotation marks. So let's add some more content to those quotation marks.

The Sinatra app we created (the file 'home.rb') has one view, located at '/'.
It's a pure Ruby file, not an HTML file, so it won't parse HTML.

Since this is going to be a very simple, one-page, static site, we can just put all our HTML 
markup inside the quotes. Later, we'll look at how to use ERb templates to do it "The Ruby Way," 
but for now, just put everything you want the browser to render inside the quotes.

```ruby
require 'rubygems'
require 'sinatra'
get '/' do
"<h1>Nana Deb's Aprons</h1>
anything else you put here will go into the browser"
end
```

Now if you check the page, you'll see Nana Deb's Aprons as a first-level heading (h1), 
along with the text "anything else you put here will go into the browser".

Add some basic styling
------------------

Let's add some styling to that header. At the top of the document, add the following lines:

Extract HTML into an ERb View
-------------------

Our ruby file home.rb should just have Ruby code in it, but we have all kinds of 
HTML and view logic. We've already extracted the style content into its own CSS file, but 
now it's time to extract the view code into its own file.

Sinatra supports lots of view helpers. We're going to use ERb, the same one used by Ruby on Rails.
When you add the method erb and pass it the name of the view, Sinatra knows to look for that 
view in the folder ./views, which in my case is nanadebs/views. 
So the first step is to add that folder and create the ERb view:

```bash
$ mkdir views
$ touch index.html.erb
```
Now we need to add the ERb gem dependency to the file home.rb:

```ruby
require 'erb'
```

Extract everything inside the quotes (all the HTML) into a new file called "index.html.erb".
This new file now looks like this:

```html
<head>
  <link href="nana_debs_styles.css" rel="stylesheet" type="text/css"></link>
</head>
    <header>
      <h1>Nana Deb's Aprons</h1>
</header>
    <section>
      Hand-made artisan aprons made from rescued fabric.
Inquire at <a href="">nanadebsaprons@gmail.com</a>
</section>
    <footer>
      Copyright Nana Deb's Aprons, 2011
</footer>
```

Now, the only thing you need in the '/' route is a call to the file index.html.erb 
via the method "erb". Sinatra automatically knows to look for views in the 
folder /nanadebs/views. Now home.rb looks like this:

```ruby
require 'rubygems' 
require 'sinatra'
require 'erb'

get '/' do
  erb :index
end
```
