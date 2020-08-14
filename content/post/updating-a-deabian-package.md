---
title: "Updating a Deabian Package"
date: 2020-08-14T01:05:10+05:30
draft: true
---

Every GNU/Linux distribution has their own package managers and package formats. for Debian package manager is apt and the .deb package format. Debian packaging is quite interesting, there's a fair bit of instruction that is included in a Debian source package.
This page deals with updating a debian package to a new upstream release.
## Preface
### When to update a package?
How frequently a package is updated varies, from package to package. It's usually as per the discretion of the maintainer of the package. Usually there are software packages that are the main products that our users use such as Firefox, Gitlab, Emacs etc. and then there are other pieces of software and libraries that is required for above mentioned software packages to function. These packages are called **dependencies**. Dependencies are usually only updated to a newer version when their 'dependents' (the main software products we mentioned above) no longer works with the current version.

The rationale for such a system comes from debian core policy of prefering stability over shiny new features. It's more important that every software package currently in Debian works well with every other software package in Debian.

### What do you need to update a Debian package?
1. A system running Debian. It's possible to package for Debian using Ubuntu or Mint or any Debian based distro but doing it from a Debian system in my experience has given me the least headaches.
2. A virtual Debian Unstable env. Debian has three to fours distributions active at all times: Old-Stable (Currently Stretch), Stable (Currently Buster), Tester (Currently Bullseye) and Unstable (Always Sid).\
   We do packaging on sid. Every package enters truly only enters Debian once it is in sid, the unstable distribution. It is here that critical bugs are found, reported and fixed. Once a packages reaches a level of maturity in sid, it's moved to the current testing repo. At any point sid has the latest version of any package in Debian. this helps people like me (and hopefully you) who does packaging for Debian. Sid BTW is the only Debian rolling release distribution. You could compare it to Archlinux.\
   This page long as it is so I'm not going to make it long by adding instructions to setup a Debian Unstable env. Here's a [Debian Wiki Page](https://wiki.debian.org/Packaging/Pre-Requisites) that tells you how to do that. On that page I personally would go with the schroot method because we regularly use stuff like LXC for building in clean envs and testing.
3. Ability to work from a terminal. You don't need to be unix wizard. Just know your way around a terminal or this is going to be a lot more harder than it is.
4. Steady internet connection.
5. If you're not a Debian developer you need a Debian Developer to sponsor (upload your package) your package for you.

### Getting Started
1. Get the Unstable env up.
2. Install required packages:
```bash
sudo apt install uscan git git-buildpackage lintian
```
3. Create an account at [Salsa](https://salsa.debian.org)- Debian's Gitlab instance.

## Basic steps
I'm going to use `ruby-health-check`, a package I recently worked on as an example.
- Fork and clone the salsa repo using git-buildpackage i.e gbp:
  ```
  gbp clone --pristine-tar git@salsa.debian.org:avron/ruby-health-check.git
  ``` 
