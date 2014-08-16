---
layout: post
title: "Embed Tweets in Posts"
description: "Embed tweets in posts"
modified: 2014-08-16 23:45:10 +0200
category: 
tags: [tweets, embed, post]
image:
  feature: 
  credit: 
  creditlink: 
comments: true
share: true
---

Biostar and Neurostars provide a cool feature related to post editing: users can easily add links to other posts and users and embed snippets of code from Gist and videos from YouTube.

Today I implemented the support for tweets from twitter. This feature will be soon deployed to live.

Some screenshot from our preview website:

<figure>
    <a href="{{ site.baseurl }}/images/2014-08-16-embed-tweets/1.png">
        <img src="{{ site.baseurl }}/images/2014-08-16-embed-tweets/1.png">
    </a>
    <figcaption>The FAQ page explains how to embed items.</figcaption>
</figure>

<br />
<figure>
    <a href="{{ site.baseurl }}/images/2014-08-16-embed-tweets/2.png">
        <img src="{{ site.baseurl }}/images/2014-08-16-embed-tweets/2.png">
    </a>
    <figcaption>A post with several embedded items and links.</figcaption>
</figure>

<br />

<figure class="half">
    <a href="{{ site.baseurl }}/images/2014-08-16-embed-tweets/3.png">
        <img src="{{ site.baseurl }}/images/2014-08-16-embed-tweets/3.png">
    </a>
    <a href="{{ site.baseurl }}/images/2014-08-16-embed-tweets/4.png">
        <img src="{{ site.baseurl }}/images/2014-08-16-embed-tweets/4.png">
    </a>
    <figcaption>The source code of the post in the previous image.</figcaption>
</figure>