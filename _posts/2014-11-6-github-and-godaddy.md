---
layout: post
title: Configuring a Godaddy domain name with github pages
date: 2014-11-06
categories: jekyll tutorial
---
I own a custom domain through GoDaddy.com that I want to use for my user page
at arsturges.github.io.

Github.io offers a neat, free hosting option. I had no trouble setting up a
site at arsturges.github.io, but I found the custom URL documentation to be
unclear. Below, I'll explain how I set up my github.io user page with my 
GoDaddy.com domain name andrewsturges.com.

In brief, the steps are as follows:

1. Set up my user page arsturges.github.io
1. Commit a file called CNAME with one line `andrewsturges.com`
1. Go to GoDaddy site to manage my URL.
1. Add an "A (Host)" record with "host" = @ and "Points to" = 192.30.252.153
1. Add a "CNAME (Alias)" record with "host = `www` and "Points to" = arsturges.github.io
1. Wait for changes to propogate.

Below i'll go over these steps in detail. 

1. Set up your user page at gitub.io
-------------------------

I'll asume this is done. In my case, I set up a user page at arsturges.github.io.
When I navigated to that page, the URL stayed as asturges.github.io (i.e., it 
didn't redirect to anywhere).

2. Commit a CNAME file
-----------------
I added a file called CNAME (all caps, no extension), with a single line `andrewsturges.com`.
![CNAME file as seen on Github]({{site.url}}{{site.baseurl}}/assets/cname.png)

3. Go to Godaddy.com and set up two records
-------------------------------------------

On the github.io custom URL instructions, this part wasn't clear to me.
For my page, I had to set an A record *and* and Alias record.
First, navigate to the page where you can manage the domain that want to point
to your github.io page. From there, click on the "DNS Zone File" tab to add records.

![GoDaddy domain management page]({{site.url}}{{site.baseurl}}/assets/godaddy_dns_page.png)

### 4. Create an A record ###

Add a single A record that points to 192.30.252.153. This is explained at 
https://help.github.com/articles/tips-for-configuring-an-a-record-with-your-dns-provider/

![GoDaddy A record]({{site.url}}{{site.baseurl}}/assets/a_record.png)

### 5. Create a CNAME record ###

Add a single CNAME record as follows:

![GoDaddy CNAME record]({{site.url}}{{site.baseurl}}/assets/cname_record.png)

6. Wait for changes to propogate
----------------------

When the DNS updates, you should be able to navigate to your custom domain and
see your pages.github.io page.
