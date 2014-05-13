---
layout: post
title: "API Sketch"
description: "API sketch starting from what has been done in Biostar 1"
modified: 2014-05-13 15:16:47 +0200
category: 
tags: [api, rest]
image:
  feature: 
  credit: 
  creditlink: 
comments: true
share: true
---

Building a [RESTful API](http://en.wikipedia.org/wiki/RESTful_API) will probably be my first activity for Neurostars: it is a good opportunity for me to get an overview of the codebase. Some work have already been done in *Biostar 1* and that could be the starting point.

**Table of Contents**

- [API In Biostar 1](#api-in-biostar-1)
  - [1. Traffic](#traffic)
    - [Url](#url)
    - [Response](#response)
    - [Description](#description)
    - [Possible Improvements](#possible-improvements)
  - [2. User Details](#user-details)
    - [Url](#url-1)
    - [Response](#response-1)
    - [Description](#description-1)
  - [3. Post Details](#post-details)
    - [Url](#url-2)
    - [Response](#response-2)
    - [Description](#description-2)
  - [4. Statistics](#statistics)
    - [Url](#url-3)
    - [Response](#response-3)
    - [Description](#description-3)
    - [Possible Improvements](#possible-improvements-1)
- [API In Biostar 2](#api-in-biostar-2)
  - [Ideas](#ideas)
    - [a. Questions](#a-questions)
    - [b. Answers](#b-answers)
    - [c. Comments](#c-comments)
    - [d. Search](#d-search)
    - [e. Users](#e-users)
    - [f. Authentication](#f-authentication)
    - [g. Versioning](#g-versioning)
    - [h. Semantic backend](#h-semantic-backend)
  - [Notes](#notes)
  - [Tools](#tools)
  - [Bibliography](#bibliography)

---


# API In Biostar 1

A simple API was built for [Biostar 1](https://github.com/ialbert/biostar-central/blob/biostar1/main/server/api.py).
The available methods are:

## 1. Traffic

### Url
`GET api/traffic`

### Response
{% highlight json %}
{
    "date": "Tue May 13 08:55:32 2014", 
    "timestamp": 1399992932.0, 
    "traffic": 512
}
{% endhighlight %}

### Description
Number of post views over the last 60 mins filtered by unique IPs.

### Possible Improvements
- Clearly state the units of measurement.  
We could f.i. change the field `"traffic"` to `"post_views_last_60_mins"`.
- Add minutes parameter: `GET api/traffic?min=5`.
This returns the number of post views over the last 5 min filtered by unique IPs.
- Improve the count algorithm.  
Users part of the same network infrastructure (like a office or a university) might share the same public IP, so the number provided might be underestimated.  
We could improve this algorithm by counting post visits of unique logged users plus post visits of anonymous users filtered by unique IPs.


## 2. User Details

### Url
`GET api/user/<id>`

### Response
{% highlight json %}
{
    "date_joined": "2013-12-13", 
    "id": 2, 
    "joined_days_ago": 151, 
    "last_visited": "2013-12-17", 
    "name": "John Doe", 
    "vote_count": 156
}
{% endhighlight %}

### Description
General info about a user.


## 3. Post Details

### Url
`GET api/post/<id>`

### Response
{% highlight json %}
{
    "answer_count": 0, 
    "author": "John Doe", 
    "author_id": 1, 
    "creation_date": "2013-12-16", 
    "id": 2, 
    "lastedit_date": "2013-12-16", 
    "parent_id": 1, 
    "rank": 5626.6076326, 
    "root_id": 1, 
    "score": 0, 
    "title": "C: How to cook Pizza?", 
    "type": "Comment", 
    "type_id": 3, 
    "xhtml": "<p>Wood fired oven is best!</p>\n"
}
{% endhighlight %}

### Description
General info about a post.


## 4. Statistics

### Url
`GET api/stats/<days>`

### Response
{% highlight json %}
{
    "answers": 70, 
    "comments": 53, 
    "date": "2014-05-03", 
    "days_ago": 10, 
    "questions": 30, 
    "timestamp": 1399179600.0, 
    "toplevel": 43, 
    "users": 103, 
    "votes": 215, 
    "x_new_posts": [2], 
    "x_new_users": [3], 
    "x_new_votes": [4]
}
{% endhighlight %}

### Description
Website statistics from day-0 until \<days\> days ago. 

### Possible Improvements
- Clearly state the date interval.  
In the response we could use the fields `date_from` (day-0) and `date_until`.
- The code is currently broken.  
The cause is a name clash between the variable `json` and the module `json`.
- \<days\> might be not user friendly.  
*Istvan* pointed out that counting backwards from the current day seems to be simple to use, as it answers questions like: "What was posted ten days ago?". But this makes life harder for people who want to mine our system periodically. He suggests to count forward from day-0.  
I guess we could accept 3 exclusive parameters:
    - `until` to provide stats from day-0 until `until`.
    - `days_ago` to provide stats from day-0 until `days_ago` days ago.
    - `days_from_zero` to provide stats from day-0 until day-0 + `days_from_zero`.  
As *Istvan* suggests we could also generate these files every day (it's a matter of 1 file per day) and serve them as static files.

- Cached files could grow indefinitely.
I like the idea of caching statistics in json files, but we have to consider that those files could grow indefinitely. We must monitor this, f.i. we could set a threshold of N files and when reached, delete the file with the oldest modification timestamp.  
We might simply ignore this point as it is a matter of one small text file per day.

---

# API In Biostar 2

Biostar 2 has no API so far. 
Porting Biostar 1 API to Biostar 2 should be not too hard but my impression is that the code requires some love before being ported. A solid design is also required if we plan to create a complete set of functionalities.

## Ideas

Apart from the possible improvements I've already mentioned above, we could consider some of the followings.

### a. Questions
- Get the most recent questions (filtering by id, date, tags, author, popularity, unanswered status, votes, bookmarks).
- Create/edit a question.
- Delete a question I have authored.
- Vote/bookmark a question.

### b. Answers
- Get the most recent answers (filtering by id, question, date, author, votes, bookmarks).
- Create/edit a answer.
- Delete a answer I have authored.
- Vote/bookmark a answer.
- Accept/refuse answers if I'm the author of the question.

### c. Comments
- Get the most recent comments (filtering by id, question, answers, date, author, votes).
- Create/edit a comment.
- Vote a comment.

### d. Search
- Search for posts (questions, answers, comments) meeting certain criteria.
- Search for similar questions.

### e. Users
- Get users list.
- Get user profile.
- Get posts (questions, answers, comments) authored by a user ordered by date or popularity (votes, views, answers).

### f. Authentication
Some of those methods require authentication, by meaning f.i. that only authenticated users could post a question.

### g. Versioning
I suggest we first introduce a small set of methods and then gradually extend it. This requires us to use version numbers like:
`api.biostars.org/1.0/answers`

### h. Semantic backend
*Satrajit* has a plan to build a semantic backend for Biostar. This would let Biostar interact with external websites: f.i. we could provide any website with a button "ask this in Biostar" to automatically post a new question. This semantic backend would work together with the API, we should keep this in mind!


## Notes

Building such API is a major benefit for our project since it gives developers the ability to create new user interfaces like mobile applications.

We must take inspiration from the very well designed [StackOverflow API](http://api.stackexchange.com/docs/).

## Tools
[Django REST Framework](http://www.django-rest-framework.org/)

## Bibliography
[REST in Practice](http://shop.oreilly.com/product/9780596805838.do)