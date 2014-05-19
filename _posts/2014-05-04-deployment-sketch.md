---
layout: post
title: Deployment Sketch
description: "Attempt to improve the deployment of Neurostars using Vagrant and Docker"
modified: 2014-05-05 09:55:46 +0200
tags: [deployment, vagrant, docker]
image:
  feature: 
  credit: 
  creditlink: 
comments: true
share: true
---

One of my first activities for *Neurostars* will be improving the deployment strategy. I have been working on it for a week end and produced a sketch. There is still a lot to do, so this is nothing more than a starting point.

---

**Table of Contents**

- [How To Deploy To A Local Development Env](#how-to-deploy-to-a-local-development-env)
- [Overview](#overview)
- [Details](#details)
  - [Requirements For A Development Environment](#requirements-for-a-development-environment)
  - [Docker](#docker)
    - [Container 1: PostgreSQL](#container-1-postgresql)
    - [Container 2: Web Server](#container-2-web-server)
  - [Vagrant](#vagrant)
- [Open Issues](#open-issues)
  - [1. Django Console](#django-console)
  - [2. Vagrant up --provision](#vagrant-up---provision)
  - [3. Full Text Search Engine](#full-text-search-engine)
  - [4. Python virtualenvironment](#python-virtualenvironment)
  - [5. NFS in Vagrant](#nfs-in-vagrant)
  - [6. Review of general deployment strategy](#review-of-general-deployment-strategy)
- [Interesting Articles and Tools](#interesting-articles-and-tools)

---

# How To Deploy To A Local Development Env
- Fork [Biostar repository](https://github.com/ialbert/biostar-central) on Github.  
*Note*: you should temporary use [my repo](https://github.com/nimiq/biostar-central).

- Run the following commands:
{% highlight bash linenos %}
mkdir biostar
cd biostar
git clone https://github.com/<your_github_username>/biostar-central.git .
cd conf
vagrant up
{% endhighlight %}
- Now you can see the website at <http://localhost:8000/> and edit the code in the biostar folder.
When editing the code, Django server automatically reloads.

- You can suspend Vagrant with: `vagrant suspend`   
And resume with: `vagrant resume`  
Or you can stop Vagrant with: `vagrant halt`  
And restart with: `vagrant up --provision`

# Overview
The code is stored in a folder in the developer’s laptop.
**Vagrant** shares this folder with the virtual machine and launches 2 **Docker** containers:

- a PostgreSQL server
- a Django server.

Vagrant also shares the folder where the code is with the Django server, so it can run it.
The developer edits the code, Django detects the change and reload the server.

<figure>
    <img src="{{ site.baseurl }}/images/deployment-sketch.png">
    <figcaption>Main components of the deployment based on Vagrant and Docker.</figcaption>
</figure>

# Details

## Requirements For A Development Environment
After a brief [consultation](https://github.com/nimiq/biostar-central/commit/a10800166d63f2bbb957b98d791430fc1e2126a6#commitcomment-6145672) with Roman and Istvan we decided to:

- always use a proper *web server*, even in development: [waitress](https://pypi.python.org/pypi/waitress/0.8.8) + [whitenoise](https://pypi.python.org/pypi/whitenoise/1.0.1) are good candidates
- use the same *DBMS* as in production, so PostgreSQL and not SQLite: this avoids having problems with migrations and similar.

So we need 2 Docker containers, one for each component.

## Docker

### Container 1: PostgreSQL
The first Docker container is pretty simple, a Ubuntu 14.04 machine.
When this container is **built**, PostgreSQL 9.3 server is installed and configured.
When this container is **run**, PostgreSQL is launched and the port 5432 exposed.

### Container 2: Web Server
*Note: we are temporary using Django integrate server in this sketch.*
The second Docker container is a Ubuntu 14.04 machine with a more complex configuration.
When this container is **built**, Python is installed.
When this container is **run**:

- the *requirements* are installed via pip
- the *environment vars* are set
- the *database* is created (if the database already exists, then skip this)
- `syncdb` and `migrate` commands are run
- if the database has just been created (so it didn't already exist) then `initialize_site` command is run, the *fixtures* are loaded and the *full text search engine index* is rebuilt
- Django internals server is launched on port 8000 and that port exposed.

*Why doing all these tasks when the container is run, instead of when the container is built?*  
Mainly for a reason: this container needs to be linked to the other container in order to use PostgreSQL; and needs to mount a volume (which is the folder where the code is) from the virtual machine in order to run the Biostar project. These 2 operations (link and mount a volume) could be done only when a container is run, not when it is build (because a build should be independent from the local configuration).
This is also convenient because every time the container is run, which happens at the beginning of the working day, new requirements are installed via pip and migrations are run.

## Vagrant
Vagrant uses a special [Docker provisioner](https://docs.vagrantup.com/v2/provisioning/docker.html). This provisioner automatically installs Docker in the VM and builds the containers. It also takes care of running the containers when the VM is launched.

---

# Open Issues

## 1. Django Console
There is no direct access to Django’s internal server console: this is a main issue.
That console is inside a Docker container in the VM, so inaccessible from the laptop.

*Solutions:*

- [Vagrant 1.6](http://www.vagrantup.com/blog/feature-preview-vagrant-1-6-docker-dev-environments.html) solves this
- [Fig](http://orchardup.github.io/fig/).

## 2. Vagrant up --provision
With the current configuration Vagrant must be run with the `--provision` flag.
This is because:

- during provisioning (so with the --provision flag), Docker containers are **run**
- during regular start (so without the --provision flag), Docker containers are **started**.
And using a start command it is not possible to mount volumes and link containers together (it seems strange, but I couldn’t manage to do it).

*Solutions:*

- Seems that committing after a run, helps to keep the run parameters on the next start: <http://docs.docker.io/reference/commandline/cli/#run>
- Fig would probably solve this too
- Create a script in the VM which runs the container when the VM starts.

## 3. Full Text Search Engine
I guess we want to use **Whoosh** in development and **Elasticsearch** in production.
Anyway from time to time we might want to debug Elasticsearch in development: in this case we need a Docker container for Elasticsearch.

## 4. Python virtualenvironment
I think it makes no sense to use Python virtualenvironments inside a Docker container since a container is itself a sort of virtual environment.

## 5. NFS in Vagrant
Vagrant docs [suggest](https://docs.vagrantup.com/v2/synced-folders/nfs.html) to use NFS to share the folders in case of performance issues. No needed so far, but keep it in mind.

## 6. Review of general deployment strategy
The original deployment strategy relies on 2 files:

- `conf/default.env`
- `biostar.sh`

The script `biostar.sh` is the entry point of the deployment. What I have done so far is completely compatible with the original deployment strategy, but if we really mean to switch to a new strategy based on Vagrant and Docker, there is no more need of this script.

The file `default.env` contains environment variables. Django experts recommend to use environment variables for passwords and to set the location of the main settings file. Any other setting should be included in the settings file. According to this recommendation, `default.env` seems to be overused, it contains f.i. `BIOSTAR_ADMIN_EMAIL` and `DATABASE_NAME`. We could review this and follow the guideline: one requirement file and one settings file and one environment vars file for each environment (development, staging, production).

---

# Interesting Articles and Tools

- <http://marceldegraaf.net/2014/04/24/experimenting-with-coreos-confd-etcd-fleet-and-cloudformation.html>
- <http://docs.docker.io/installation/mac/>
- <http://www.vagrantup.com/blog/feature-preview-vagrant-1-6-docker-dev-environments.html>
