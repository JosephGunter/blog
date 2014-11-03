---
layout: post
title:  "Installing Twitter Bootstrap in Flask 0.9"
date:   2012-08-09 19:53:18
categories: bootstrap flask
---
I have a small web application called 'dispatchtool' written in the Python microframework 
[Flask](http://flask.pocoo.org/).
I want to use Twitter's [Bootstrap CSS](http://twitter.github.com/bootstrap/).
In this post I will explain how to do this.

I am running Ubuntu 12.04, using Python 2.7, and Flask 0.9.
This tutorial assumes you've got a basic Flask application up and running,
and that you can view the development version on your local machine.
It also assumes that you're using [templates](http://flask.pocoo.org/docs/tutorial/templates/).
(This is important!) 

Step 1: Get a basic CSS file working
-----------------------------------
Before dealing with Bootstrap, we want to ensure sure that the application can
correctly find and use a simple test CSS file.
To do this, first, create a new directory in your Flask application's root directory called 'static'.
My app is called 'dispatchtool', so I create 'static' inside the 'dispatchtool' directory:

```bash
$ cd dispatchtool
$ mkdir static
```
Next, in that new directory, create the test css file. I'll call mine 'test.css'.
Add some simple style that will be loud and obvious.
It's only purpose will be to ensure that we can get the application to see this file and use it.
Here's my 'test.css':

```css
h1 {color: red}
p {color: green}
```
Of course, make sure that your app includes an `<h1>` tag and a `<p>` tag somewhere,
or else these styles won't be applied, even if the stylesheet is properly set up.

Now that we've got our test css file in the right place, we need to tell Flask how to find it.
CSS files are always linked to HTML pages in the `<head>` section of
each page using the `<link … />` tag. In my application, I'll add the following tag:

```html
<link type="text/css" rel="stylesheet" href='/static/test.css'>
```

Here's the top of my page, for context:

```html
<!DOCTYPE html>
<html lang="en">
<head>
<title>The Demand Response Dispatch Tool</title>
<link type="text/css" rel="stylesheet" href='static/test.css' />
</head>
<body
```

Now open up your page in a browser, and see if the styles were applied.
In my case, it suddenly looks like Christmas. If nothing changed, look at the source (ctrl-U)
and click on the link you created to the stylesheet. That will show where Flask thinks the CSS file is. 

Even though that's working, we're not quite done yet.
Web application frameworks like Flask have a better way of creating links to
static things like CSS files.

In Flask, it's a method called [url_for()](http://flask.pocoo.org/docs/api/?highlight=url_for#flask.url_for).
Methods like this make your application more flexible, allowing you to move 
around pages without breaking links to your CSS files.
So now, replace the value of the `href` attribute in the `<link>` tag with the following line:

```python
{% raw %}
{{ url_for('static',filename='test.css') }}
{% endraw %}
```
The double braces tell Flask a) that the contents are to parsed as Python code and b) to 
render the result directly into the HTML output.

Now the whole link tag should look like this:

```html
{% raw %}
<link type="text/css" rel="stylesheet" href="{{ url_for('static', filename='test.css') }}" />
{% endraw %}
```
Now save the file and refresh your browser page.
If that still renders your page using your test CSS file, then we're most of the way done. 
If it doesn't, check the page source, and ensure that the `<link>` tag is pointing to the right place.

Step 2: Download and "install" Twitter Bootstrap
----------------------------------------------
The next step is simple and short.
We need to get the Bootstrap files (download) and put them in the right place (install). 
Normal people will navigate to [the Bootstrap page](http://twitter.github.com/bootstrap/getting-started.html),
click [the link](http://twitter.github.com/bootstrap/assets/bootstrap.zip),
and unzip the files, then put those files (as-is, with no changes to the directory structure)
in the 'static' folder that we created earlier:

```bash
$ cd ../static/
$ wget http://twitter.github.com/bootstrap/assets/bootstrap.zip
$ unzip bootstrap.zip
$ rm -r bootstrap.zip
```

Whichever way you do it, you should now have a folder called 'bootstrap' inside your 'static' folder.
That's it. You have downloaded and 'installed' Bootstrap.

Step 3: Link to the boostrap.css stylesheet
-------------------------------------------

The main Bootstrap stylesheet is in bootstrap/css/, and it's called 'bootstrap.css'. 
We want to change our link from 'test.css' to this file, so update your `<link>` tag as follows:

```html
{%raw%}
<link type="text/css" rel="stylesheet" href="{{ url_for('static', filename='bootstrap/css/bootstrap.css') }}" />
{% endraw %}
```
That's it. If you reload your page, you should see at least some changes reflecting Twitter's style. 
(When you go to production, you should switch from the general 'bootstrap.css' to the 
minified 'bootstrap.min.css' to save bandwidth.)

Step 4: Update your HTML to hook into the Bootstrap styles
-------------------------------------------------------
Now that we've got our Flask app properly linking to the Boostrap css file,
all that remains is to use it.
That means we have to look at the boostrap.css file, and see what classes and ids we 
need to assign to our HTML elements. 
I'll go through two quick examples.

###Container
The most important is probably to use Bootstrap's "container" class. 
Simply add the class 'container' to the <code><body></code> tag:

```html
<body class="container">
```
When you refresh the page, you should see your content contained in a 940 pixel-wide column. 

###The Hero Unit
The most famous Bootstrap style is probably the "hero unit". 
To see it, just add the class 'hero-unit' to any block-level element: 

```html
<div class="hero-unit"><p>Don't abuse this style!</p></div>
```

Conclusion
------------
We walked through how to set up general CSS file in a Flask app, 
then how to acquire and link Bootstrap to the same Flask app. 
The next step is to check out the 
<a href="http://twitter.github.com/bootstrap/scaffolding.html">Bootstrap Documentation</a>,
and start choosing the styles you want to use. Thanks for reading!
