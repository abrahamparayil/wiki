---
title: "Simple Packaging Tutorial: The Long Version"
date: 2020-08-26T17:19:10+05:30
tags: ["debian", "gnu/linux","packaging"]
draft: true
---

This page is created to aid the attendants of the [ഡെബിയന്‍ പാക്കേജിങ്ങ് ബാലപാഠങ്ങള്‍ പഠിയ്ക്കാം (Simple Packaging Tutorial with debmake)](https://debconf20.debconf.org/talks/88-simple-packaging-tutorial-with-debmake/) workshop held at the Malayalam MiniConf during DebConf 20 on 28th August 12 AM - 1:45 PM IST.

The workshop was taken by [Pirate Praveen](https://poddery.com/people/45fa8bea21b8a0f5) who is a Debian Developer and myself.

In the workshop we will be showing you guys how to make a simple Debian package. You could refer [Simple Packaging Tutorial](https://wiki.debian.org/SimplePackagingTutorial) at the Debian Wiki. This page adheres to the structure of the workshop and provides addition information that is otherwise spread across multiple pages in the Debian Wiki.

{{< note >}}
Having a Debian Unstable System is a pre-requisite to this workshop. If you haven't done the same, you can follow the tutorial here => https://wiki.abrahamraji.in/sid-env/ to set up an unstable system on your machine using schroot.
{{< /note >}}

First Let's get organized, let's create a folder in our home directory dedicated for packaging. you can do so from the terminal itself by running the following command:
```bash
mkdir <directory name>
```
Where <directory name> is pretty much anything you wish to name it. Persoally I've named mine Debian.
Once we've created the said directory let's let's switch to it using the `cd command`
```bash
cd <directory name>
```
Packaging is mostly a staright foreward ordeal. The process of packaging remains more or less the same regardless of whether it's a JS or python or language x package. For the purpose of this tutorial we will be packaging a node module called pretty-ms.

To begin we need to first collect the source code of the peice of software that we intend to package which in our case is `pretty-ms`.