---
layout: post
title: "Integration with ORCID"
description: "Integrating ORCID in Neurostars"
modified: 2014-07-23 23:20:34 +0200
category: 
tags: [orcid, api, oauth, django-allauth]
image:
  feature: 
  credit: 
  creditlink: 
comments: true
share: true
---

*"[ORCID](http://orcid.org/) provides a persistent digital identifier that distinguishes you from every other researcher and, through integration in key research workflows such as manuscript and grant submission, supports automated linkages between you and your professional activities ensuring that your work is recognized."*

We would like to integrate ORCID in Neurostars, providing two new features:

- Signup/login using a ORCID account (via OAuth)
- Import data from a ORCID profile to a Neurostars profile

This post is mainly about the first point.

---

**Table of Contents**

- [OAuth](#oauth)
- [Public vs member ORCID API](#public-vs-member-orcid-api)
- [Sandbox vs production ORCID API](#sandbox-vs-production-orcid-api)
- [Client apps registration](#client-apps-registration)
- [OAuth Flow](#oauth-flow)
- [Fetching bio](#fetching-bio)
- [Response](#response)
- [Open Issues](#open-issues)

---

OAuth
=====
ORCID supports [OAuth](http://en.wikipedia.org/wiki/Oauth) which is meant for resource owners to authorize third-party access to their server resources without sharing their credentials.
It can also be used to identify and thus sign up a user.

There are two solid libraries we can use to integrate the OAuth protocol in Django:

1. [requests-oauthlib](https://github.com/requests/requests-oauthlib) a Python low-level library, built on top of [requests](http://docs.python-requests.org/en/latest/), to implement the full OAuth protocol;
2. [django-allauth](https://github.com/pennersr/django-allauth) a Django library, built on top of requests-oauthlib, to use OAuth providers in the signup/signin Django process.

We decided to fork and extend the original **django-allauth** library and add the support for ORCID.


Public vs member ORCID API
==========================
*"ORCID offers two APIs, the **Public API** which can be used by anyone to search the ORCID Registry and retrieve public information, and the Member API. The **Member API** is available only to ORCID members and allows them to authenticate researchers, access read-limited information, and edit ORCID records- as long as they have the researcher's permission."*

Reference:

- All the available API resources are [here](http://support.orcid.org/knowledgebase/articles/116874-orcid-api-guide);
- API [FAQ](http://support.orcid.org/knowledgebase/articles/222773-api-faq);
- [Knowledge base](http://support.orcid.org/knowledgebase).


Sandbox vs production ORCID API
===============================
*"The ORCID **development sandbox** is designed to resemble the production Registry as closely as possible, the only notable differences are with with the Import Works function and that the sandbox does not send email messages to most email addresses. Occasionally, the sandbox will be one version ahead of the Production Registry to allow for API developer testing, when this happens we will notify the API Users Group."*

**Sandbox** API urls:

- Public: `http://pub.api.sandbox.orcid.org`
- Member: `http://api.sandbox.orcid.org`

**Production** API urls:

- Public: `http://pub.orcid.org`
- Member: `https://api.orcid.org`

Reference:

- [Info](http://support.orcid.org/knowledgebase/articles/166623-about-the-orcid-sandbox) about the sandbox;
- [Create](https://sandbox.orcid.org/register) sandbox members.


Client apps registration
========================
Client apps must be registered [here](http://orcid.org/content/register-client-application).
Consult the [guide](http://support.orcid.org/knowledgebase/articles/116739-register-a-client-application) first.


OAuth Flow
==========
The OAuth flow is composed of 2 steps:

- [Authorization](http://support.orcid.org/knowledgebase/articles/120107-get-oauth-authorize);
- [Token generation](http://support.orcid.org/knowledgebase/articles/119985).

A full example is listed [here](http://support.orcid.org/knowledgebase/articles/179969-methods-to-generate-an-access-token-for-testing).

Reference and tools:

- [Scopes](http://support.orcid.org/knowledgebase/articles/120162-orcid-scopes);
- [Playground](http://support.orcid.org/knowledgebase/articles/154641-introduction-to-the-api-with-google-s-oauth-playgr) - [Direct link](https://developers.google.com/oauthplayground/#step3&url=https%3A//api.sandbox.orcid.org/&content_type=application/json&http_method=GET&useDefaultOauthCred=unchecked&oauthEndpointSelect=Custom&oauthAuthEndpointValue=https%3A//sandbox.orcid.org/oauth/authorize&oauthTokenEndpointValue=https%3A//api.sandbox.orcid.org/oauth/token&includeCredentials=unchecked&accessTokenType=bearer&autoRefreshToken=unchecked&accessType=offline&forceAprovalPrompt=checked&response_type=code).


Fetching bio
============
A user's bio can be fetched via Public or Member API at the following urls: 

- Public API: `http://pub.sandbox.orcid.org/v1.1/<ID>/orcid-bio`
- Member API: `https://api.sandbox.orcid.org/v1.1/<ID>orcid-profile`

Where `<ID>` is a user identifier like: `0000-0001-7857-2795`

Reference:

- [Guide](http://support.orcid.org/knowledgebase/articles/132271-retreiving-data-with-the-public-api).


Response
========
**Tokens** have the following form:

~~~ json
{
  "access_token": "de050afe-a068-4374-ab40-430713f9763d", 
  "expires_in": 631138518, 
  "token_type": "bearer", 
  "orcid": "0000-0001-6796-198X", 
  "scope": "/orcid-profile/read-limited", 
  "refresh_token": "1915d42b-4828-42be-b081-db0d27c85a03"
}
~~~

Users bio have the following form:

~~~ json
{
    "message-version": "1.1",
    "orcid-profile": {
        "orcid-bio": {
            "personal-details": {
                "credit-name": {
                    "visibility": null,
                    "value": "J. Doe"
                },
                "given-names": {
                    "value": "John"
                },
                "other-names": {
                    "other-name": [
                        {
                            "value": "Jonathan Doe"
                        }
                    ],
                    "visibility": null
                },
                "family-name": {
                    "value": "Doe"
                }
            },
            "researcher-urls": {
                "researcher-url": [
                    {
                        "url": {
                            "value": "http://neurostars.org/u/46/"
                        },
                        "url-name": {
                            "value": "My profile at Neurostars"
                        }
                    }
                ],
                "visibility": null
            },
            "delegation": null,
            "applications": null,
            "biography": {
                "visibility": null,
                "value": "Lorem ipsum dolor sit amet, consectetur adipiscing elit. Cras id enim auctor, condimentum metus in, vestibulum erat. Integer ultrices diam vitae luctus egestas. Praesent vitae odio euismod, ultricies augue ac, gravida nibh. Ut nec nisi euismod, volutpat sem eu, tincidunt sem. Vivamus pharetra blandit purus ut consequat. Pellentesque convallis risus id leo pellentesque, a rutrum neque feugiat. Sed in metus quis sapien congue luctus. Nullam venenatis dui odio, quis molestie mauris posuere nec."
            },
            "keywords": {
                "visibility": null,
                "keyword": [
                    {
                        "value": "Neuroscience"
                    },
                    {
                        "value": "fmri"
                    }
                ]
            },
            "scope": null,
            "contact-details": {
                "email": [
                    {
                        "verified": true,
                        "primary": true,
                        "visibility": null,
                        "current": true,
                        "source": "0000-0001-5429-1577",
                        "value": "myemail@gmail.com"
                    }
                ],
                "address": {
                    "country": {
                        "visibility": null,
                        "value": "AF"
                    }
                }
            }
        },
        "group-type": null,
        "client-type": null,
        "orcid-identifier": {
            "path": "0000-0001-5429-1577",
            "host": "orcid.org",
            "uri": "http://orcid.org/0000-0001-5429-1577",
            "value": null
        },
        "orcid-activities": {
            "affiliations": null,
            "orcid-works": {
                "scope": null,
                "orcid-work": [
                    {
                        "put-code": "13249405",
                        "work-title": {
                            "subtitle": null,
                            "title": {
                                "value": "Red ant in Somalia: a case study"
                            }
                        },
                        "visibility": null,
                        "work-type": "CONFERENCE_PAPER",
                        "publication-date": {
                            "month": {
                                "value": "02"
                            },
                            "day": {
                                "value": "06"
                            },
                            "media-type": null,
                            "year": {
                                "value": "2012"
                            }
                        },
                        "url": null,
                        "work-external-identifiers": {
                            "scope": null,
                            "work-external-identifier": [
                                {
                                    "work-external-identifier-id": {
                                        "value": "arxiv:056985:4564"
                                    },
                                    "work-external-identifier-type": "ARXIV"
                                }
                            ]
                        },
                        "work-contributors": {
                            "contributor": [
                                {
                                    "contributor-attributes": {
                                        "contributor-role": "AUTHOR",
                                        "contributor-sequence": "FIRST"
                                    },
                                    "credit-name": {
                                        "visibility": "PUBLIC",
                                        "value": "J. Doe"
                                    }
                                }
                            ]
                        },
                        "work-source": {
                            "path": "0000-0001-5429-1577",
                            "host": "orcid.org",
                            "uri": "http://orcid.org/0000-0001-5429-1577",
                            "value": null
                        }
                    }
                ]
            }
        },
        "orcid": null,
        "type": "USER",
        "orcid-preferences": {
            "locale": "EN"
        },
        "orcid-history": {
            "last-modified-date": {
                "value": 1406581351644
            },
            "creation-method": "WEBSITE",
            "submission-date": {
                "value": 1406578397677
            },
            "visibility": null,
            "source": null,
            "claimed": {
                "value": true
            }
        }
    }
}
~~~


Open Issues
===========
Both *Public API* and *Member API* can be used to access users bio.
*Member API* only can be used to identify a user (so to signup/login in a third party website).
In order to get an application for the production Member API, the organization needs to become member, as stated [here](http://support.orcid.org/knowledgebase/articles/180285-introduction-to-the-orcid-api#ProdCreds) and during the creating of a [new client app](http://orcid.org/content/register-client-application).
Becoming a member probably means to pay [$5000](http://orcid.org/about/membership) per year.

So seems that we cannot implement the ORCID login feature in Neurostars without having a ORCID subscription. Of course we could use the Public API to fetch users bio, but we have to rely on what our users declare (by meaning that the Neurostars user Paolo could declare to be the ORCID user Guido van Rossum and we have no way to check that.)

**BUT** this month Publish API [has been extended](http://support.orcid.org/knowledgebase/articles/335483) such that *"organizations can have users add an ORCID iD through an authenticated process that removes the risks of  having users type in an iD themselves"*. This needs further investigation.