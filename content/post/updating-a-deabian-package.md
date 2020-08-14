---
title: "Updating a Deabian Package"
date: 2020-08-14T01:05:10+05:30
url: updating-deb-pkgs 
tags: ["debian", "gnu/linux","packaging"]
draft: false ``
---

Every GNU/Linux distribution has their own package manager and package format. For Debian it's apt and .deb. Debian packaging is quite interesting, there's a fair bit of information that is included in a Debian source package.
This page deals with updating a Debian package to a new upstream release.
## Preface
### When to update a package?
How frequently a package is updated varies, from package to package. It's usually as per the discretion of the maintainer of the package. Usually there are software packages that are the main products that our users primarily interact with such as Firefox, Gitlab, Emacs etc. and then there are other pieces of software and libraries that is required for above mentioned software packages to function. These packages are called **dependencies**. Dependencies are usually only updated to a newer version when their **dependents** (the main software products we mentioned above) no longer works with the current version.

The rationale for such a system comes from Debian's core policy of preferring stability over shiny new features. It's more important that every software package currently in Debian works well with every other software package in Debian.

### What do you need to update a Debian package?
1. A system running Debian. It's possible to package for Debian using Ubuntu or Mint or any Debian based distro but doing it from a Debian system in my experience has given me the least headaches.
2. A virtual Debian Unstable env. Debian has three to fours distributions active at all times: Old-Stable (Currently Stretch), Stable (Currently Buster), Testing (Currently Bullseye) and Unstable (Always Sid).\
   We do packaging on sid. Every package truly only enters Debian once it is in sid, the unstable distribution. It is here that critical bugs are found, reported and fixed. Once a packages reaches a level of maturity in sid, it's moved to the current testing repo. At any point sid has the latest version of any package in Debian. This helps people like me (and hopefully you) who does packaging for Debian. Sid BTW is the only Debian rolling release distribution. You could compare it to Archlinux.\
   This page is quite long as it is so I'm not going to make it longer by adding instructions to setup a Debian Unstable env. Here's a [Debian Wiki Page](https://wiki.debian.org/Packaging/Pre-Requisites) that tells you how to do that. Might add a page for this too in the future but not now. On that page I personally would go with schroot because we regularly use stuff like LXC for building in clean envs and testing.
3. Ability to work from a terminal. You don't need to be unix wizard. Just know your way around a terminal or this is going to be a lot more harder.
4. Steady internet connection.
5. If you're not a Debian developer you need a Debian Developer to sponsor (upload your package) your package for you.

### Getting Started
1. Get the Unstable env up.
2. Install the required packages:
```bash
sudo apt install uscan git git-buildpackage lintian
```
3. Create an account on [Salsa](https://salsa.debian.org)- Debian's Gitlab instance.

## Basic steps
I'm going to use `ruby-health-check`, a package I recently worked on as an example. The general steps remain same for almost all packages.

1. First fork and clone the salsa repo using git-buildpackage i.e gbp:

  `gbp clone --pristine-tar git@salsa.debian.org:avron/ruby-health-check.git`
  
  We use gbp for convenience purposes because it does a lot of things automatically. For example, every Debian source package has three branches: master, upstream and pristine tar. Master branch is usually where we work and build the package, upstream just has untouched upstream files and pristine-tar has upstream tarballs of the packages as downloaded from upstream. If we clone using git it will only clone the master branch but when we use gbp all three branches will be pull from salsa. 
- Next `cd` into the directory and download new upstream release tarball using the command:

  `uscan --verbose`

  Sometimes when using uscan the download link would have deprecated and uscan won't work, at that point your options are either to fix the download link which can be found at `debian/watch` or to manually download the tarball from the upstream releases page. I prefer to fix the link at debian/watch whenever possible and it is fixable in most cases. Here's an [example](https://salsa.debian.org/avron/ruby-doorkeeper/-/commit/14d2d337bcbf3b5fd4d2409be548a541aacda84f) of me fixing the `debian/watch` file for another package. Here are the [instructions](https://wiki.debian.org/Javascript/Nodejs/Npm2Deb#Option_2:_Download_via_github.com_commit_snapshot) to manually download upstream sources when necessary.
- If you used uscan to download the tarballs you should see a tarball named package-name\_upstream-version.orig.tar.gz in the directory you cloned the repo in. Import the orig.tar.gz using\
  `gbp import-orig --pristine-tar ../ruby-health-check_3.0.0.orig.tar.gz`.  
- We need to tell the operating system that we have imported a new version into our we repo. We do that by adding an entry in the debian/changelog file. We do so by running the following command: `gbp dch -a`. What this will do is add an entry to debian/changelog file that looks something like this:
```
ruby-health-check (3.0.0-1) UNRELEASED; urgency=medium

 * New upstream version 3.0.0

-- Abraham Raji <avronr@tuta.io>  Thu, 13 Aug 2020 16:17:14 +0000
  ```
   As I mentioned above Debian takes bugs seriously and the team works constantly on improving the quality of the software, so the package maintainers often update the packages with patches, missinng tests etc., so a single version of the package can have multiple revisions that are uploaded to unstable. In order to keep track of this we add a Debian revision number to the end of the upstream version number. So how the version number looks at the end is upstream-version-debian-revision. Up above, you can see the version is 3.0.0-1, which means the upstream version of the package right now is 3.0.0 and 1 signifies that this is the first revision of the said package in Debian.
  
  Sometimes gbp can mess up the version number. In the sense it will add your entry as a new revision of the previous version. In that case you can open the file in a text editor of your choice and manually change the version number.
- Now we build. Run:
  
  `dpkg-buildpackge`
  
  For minor updates, `i.e. a.b.c-d => a.b.e-1` there shouldn't be any problem. The build will complete successfully. If it's not a minor update, things may not \`just work\`. Sometimes you may have issues where you need a newer version of a dependency for this version of our package to work, so we may have to update that package first and come back to this one or there are patches in debian/patches made by package maintainers in case a change needs to be made and for some reason the upstream maintainer can't make that change in the upstream, these patched may no longer work when switching from one major version to another. So we need to fix these issues and successfully build the package. When do you know you've successfully build it? Ironically it is when you get a warning saying something along the lines of failed to sign your .dsc file. You don't need to worry about that since the person uploading the package will be the one to sign it.
- Next we look for minor bugs and policy violations using lintian. When you successfully build a package a file named `ruby-doorkeeper_5.4.0-1_amd64.changes` is generated in the parent directory which if you pass on as an argument to lintian will give you all the warnings and errors. Run 
  
  `lintian ../ruby-health-check_3.0.0-1_amd64.changes`
  
  All complaints from lintian come with reasons and suggestions as to how to fix them. If it doesn't then you haven't added the stuff I asked you to add at the begining of this page. Fix them. Commit your changes using git and use clear commit messages. Commit everything except debian/changelog. Here's an [example](https://salsa.debian.org/avron/node-cjson/-/commits/master) from a package I updated a while ago. It is sort of a jackpot in the sense it had alomst every lintian error I've come across. So you should find fixes to most lintian errors there. Commit messages could be a little better and more creative.
- Now remember `gbp dch -a`? once you've satisfied Lintian run it again. Now if you look at the changelog you'll find that all of your commit messages are listed as entries in the changelog. Check if there are any duplicate and remove them. Now change the `UNRELEASED`in the header to `unstable`. Now your changelog should look something like this:
```
  ruby-health-check (3.0.0-1) unstable; urgency=medium

  * Team Upload
  * New upstream version 3.0.0
  * Bumps debhelper-compat version to 13
  * Bumps standards version to 4.5.0
  * Added Rules-Requires-Root: no

 -- Abraham Raji <avronr@tuta.io>  Thu, 13 Aug 2020 16:17:14 +0000
``` 
   At this point commit your debian/changelog with the commit message `Upload to Unstable`.
- Now try and build the package in a clean env. Here are the [instructions](https://wiki.debian.org/Packaging/sbuild) to setup sbuild and building your package using sbuild.
- If the package was a major update you also need to run autopkgtests.
  An easy way to do this is through the [meta](https://salsa.debian.org/ruby-team/meta/) scripts made by Debian ruby team. Instructions to setup and use the scripts are mentioned in the README.md file.
- If all goes well send an RFS to the mailing list of the Debian Team that maintains the packages. Node packages are maintained by Debian JS team and ruby packages by Debian Ruby Team. Join the mailing list from a seperate mail account as there's a lot of mail those comes to lists everyday. Then send a mail requesting for sponsorship.
