---
layout: post
title: "Our git-flow"
description: ""
modified: 2014-05-19 22:59:34 +0200
category: 
tags: [git, github, git-flow, github-flow]
image:
  feature: 
  credit: 
  creditlink: 
comments: true
share: true
---

We decided to organize our work in GitHub following the [git-flow](http://nvie.com/posts/a-successful-git-branching-model/). The following is an overview of the strategy. 

*Note*: I'll keep editing this page until all details have been defined.

---

**Table of Contents**

- [Repositories](#repositories)
- [Development Process](#development-process)
- [Branches](#branches)
  - [Temporary branches](#temporary-branches)

---

# Repositories

- Original Biostar codebase: [ialbert/biostar-central](https://github.com/ialbert/biostar-central)
- Neurostars codebase: [INCF/biostar-central](https://github.com/INCF/biostar-central)
- My codebase: [nimiq/biostar-central](https://github.com/nimiq/biostar-central)


# Development Process

For each new feature I will create a new branch off the `develop` branch in my repository.

Naming convention: anything except `master`, `develop`, `release-*`, or `hotfix-*`. E.g. `api`, `new-deployment`.

When the feature is complete, I will merge back to `develop` and open a pull request to INCF's repository.

# Branches

- `master`: the code deployed to [live](http://neurostars.org).  
Temporary this branch matches ialbert/master. We need to migrate our current live code (branch `neurostars.org`) to Biostar2 and move the result to this `master` branch.
- `develop`: the latest delivered development changes for the next release.  
Each new feature branch will be based on this.
- `api`: a feature branch I'm currently working on to develop API.
- `new-deployment`: a feature branch meant for the new deployment strategy based on Docker.  
It currently contains some work we have done with Vagrant and Docker.
- `biostar1`: the old Biostar1 codebase.

## Temporary branches

- `dev`: old branch, to be removed.
- `old-develop`: old branch, to be removed.
- `neurostars.org`: the code currently running on [live](http://neurostars.org).  
We need to migrate this code to Biostar2, merge it to `master` and `develop`, then delete this branch.




