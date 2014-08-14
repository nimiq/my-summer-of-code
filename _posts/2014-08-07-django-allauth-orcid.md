---
layout: post
title: "Adding ORCID provider to django-allauth"
description: "Adding ORCID OAuth provider to django-allauth for social login"
modified: 2014-08-10 23:50:34 +0200
category: 
tags: [orcid, api, oauth, django-allauth]
image:
  feature: 
  credit: 
  creditlink: 
comments: true
share: true
---

In a previous [article]({% post_url 2014-07-22-integration-with-orcid %}) I have been talking about the intergration of [ORCID](http://orcid.org/) in Neurostars. In particular we would like to provide two new features:

- Signup/login using a ORCID account (via OAuth);
- Import data from a ORCID profile to a Neurostars profile.

[django-allauth](https://github.com/pennersr/django-allauth) is a Django library, built on top of [requests-oauthlib](https://github.com/requests/requests-oauthlib), which provides Django projects with similar features and works with [many providers](http://django-allauth.readthedocs.org/en/latest/providers.html) like Amazon, Facebook, GitHub, Twitter, Google, LinkedIn, etc.

We decided to contribute to the django-allauth project and add support for ORCID provider.

The OAuth flow implemented by ORCID is standard and the architecture of code in django-allauth is quite easy to understand, even though not documented: so I just had to write [a few lines of code](https://github.com/pennersr/django-allauth/tree/master/allauth/socialaccount/providers/orcid) to extend some base classes and the result was just great. My contribution was merged into the master branch quite quickly.

We have then used the updated django-allauth library to integrate ORCID as social login provider in Neurostars with the following scenario:

- a user wants to signup in Neurostars using her ORCID account; she clicks on a "Signup with ORCID" link;
- the OAuth flow begins and the user is redirected to Orcid.org to a page where she is asked to authorize Neurostars to access her ORCID profile info;
- the user confirms and she is redirected back to Neurostars to a page which asks if she wants to use her ORCID bio as her Neurostars profile info;
- the user might choose to ignore the question or confirm; if she confirms she is redirected to her profile page.

Screenshots:

<figure>
    <a href="{{ site.url }}/images/orcid-1.png">
        <img src="{{ site.url }}/images/orcid-1.png">
    </a>
    <figcaption>Adding the ORCID social login provider.</figcaption>
</figure>

<br />
<figure>
    <a href="{{ site.url }}/images/orcid-2.png">
        <img src="{{ site.url }}/images/orcid-2.png">
    </a>
    <figcaption>Authorizing Biostars (or Neurostars) to access the ORCID profile info.</figcaption>
</figure>

<br />
<figure>
    <a href="{{ site.url }}/images/orcid-3.png">
        <img src="{{ site.url }}/images/orcid-3.png">
    </a>
    <figcaption>Asking to import ORCID profile info to Neurostars.</figcaption>
</figure>

<br />
<figure>
    <a href="{{ site.url }}/images/orcid-4.png">
        <img src="{{ site.url }}/images/orcid-4.png">
    </a>
    <figcaption>The user profile after having imported the profile info.</figcaption>
</figure>