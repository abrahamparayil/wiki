# Simple Packaging Tutorial: The Long Version

This page is intended to aid the attendees of the [ഡെബിയന്‍ പാക്കേജിങ്ങ് ബാലപാഠങ്ങള്‍ പഠിയ്ക്കാം (Simple Packaging Tutorial with debmake)](https://debconf20.debconf.org/talks/88-simple-packaging-tutorial-with-debmake/) workshop held at the Malayalam MiniConf during DebConf 20 on 28th August 08:30 PM - 10:15 PM IST.

The workshop was taken by [Pirate Praveen](https://poddery.com/people/45fa8bea21b8a0f5) who is a Debian Developer and myself.
This page adheres to the structure of the workshop and provides additional information that is otherwise spread across multiple pages in the Debian Wiki.

You could refer [Simple Packaging Tutorial](https://wiki.debian.org/SimplePackagingTutorial) (a shorter version of this) at the Debian Wiki.

**Having a Debian Unstable System is a pre-requisite to this workshop. If you haven't set one up yet, you can follow the tutorial => https://wiki.abrahamraji.in/sid-env/ to set up an unstable system on your machine using schroot.**

**Every command we execute in this tutorial will be done in the Unstable System.**

## Step 0 : Getting Organized
First, let's get organized, let's create a folder in our home directory dedicated for packaging. You can do so from the terminal itself by running the following command:
```bash
mkdir <directory name>
```
Where <directory name> is pretty much anything you wish to name the folder where you'll put all the files related to packaging. Personally I've named mine Debian.

Keeping things organized will help you when working with complex packages in the future. It will avoid confusion and help you stay on top of things without getting overwhelming.

Now once we've created the said directory let's switch to it using the `cd command`
```bash
cd <directory name>
```
Packaging is often a straight forward ordeal. The process of packaging remains more or less the same regardless of whether it's a JS or python or language x package. For the purpose of this tutorial we will be packaging a node module called pretty-ms.

## Step 1: Acquire the source code.
To begin we need to first collect the source code of the piece of software that we intend to package, which in our case is `pretty-ms`. So let's go to npmjs for [pretty-ms](https://www.npmjs.com/package/pretty-ms). There on the right side of the page we can find the link to the repository and this is where we can find the source code. The source to `pretty-ms` is hosted [here](https://github.com/sindresorhus/pretty-ms). What this package pretty, much does is convert time produced in milliseconds to more human understandable formats such as hours, minutes and seconds.

If we go through the files we can find the `package.json` file which contains the metadata of the node module and README.md which contains documentation for the node nodule. The main files i.e. the actual module is the index.js file and we also have the test.js file which contains tests for the module to ensure that it works properly. Then we have the LICENSE file which contains the copyright information which will be useful later on.

Now on Github we have 'Releases' which contains various releases of the module. This can vary from one platform to another, most developers tag their releases so that is somewhere you can be sure to find releases if the platform that you encounter does not have a straight forward place to download releases from. Here the latest version of pretty-ms is 7.0.0. Let's download the source code in tar.gz format. Apart from tar.gz, tar.bz2 and tar.xz are also used by developers. The difference between them is the algorithm used to compress the files. When you compare bz2 and gz you will find that bz2 has smaller file sizes but require more time to compress and decompress whereas gz has comparatively larger file sizes but is very fast in compressing and decompressing the files. It's the developer who decides which one to use.

Now, we can download from the browser itself but what's the fun in that? Let's copy the link and use wget to download the tar file. So let's first install wget in out schroot env and download the file using it.
```bash
sudo apt install wget
wget https://github.com/sindresorhus/pretty-ms/archive/v7.0.0.tar.gz
```

#### Package Name
Now we have the source code in our packaging folder, that is if we ran the wget command from that folder. Debian has specifications and formats for everything asociated with a Debian package and there's a format for the source tarball's name too. This format is `<packagename>_<version>.orig.<tarball format>`.

As far as the package name goes if the piece of software we are packaging is something the users will run themselves for example Firefox, Rollup, Rails etc. we usually name the package the actual name of the package itself, if we take the above example their package names will remain `firefox`, `rollup` and `rails`. On the otherhand if it is a library that the users won't necessarily run themselves, we use a different naming convention. The convention used is the system or programming language or framework the library was made for, followed by a hyphen and then the package name sometimes the system or programming language or framework comes after the name of the library (whether it comes before or after the name of the package is a convention set by the team that maintain the particular set of libraries.). If the module we intend to package is made for Node JS we add `node` before the package name and if the package is relevant for JS in general we as `libjs` before the package name. Again this is only applicable to libraries and not to be used for user facing executable programs. Since pretty-ms is a module made for Node JS, the name will be `node-pretty-ms`. Another important thing to note is that we do not use underscores or uppercase characters in our package names, if your package contains an underscore convert those to hyphens and the upper case characters to lowercase characters when naming your package.

Now let's complete the name of our tarball. So first comes the package name which is `node-pretty-ms`, next we add an underscore followed by the version of the package which is `7.0.0` and finally `.orig.tar.gz`. So at the end the whole thing looks like `node-pretty-ms_7.0.0.orig.tar.gz` .

## Step 2: Debianization
Now let's go ahead and extract the contents of our tarball with the following command:
```bash
tar -zxvf node-pretty-ms_7.0.0.orig.tar.gz
```
`tar` is one of those commands that may seem usable at the beginning with -zxvf and everything but trust us in the long run using the command line will save you a lot of time once you get used to the command. If you guys have any doubts regarding any program in your distribution just type in `man <package name>` to see the various options that package provides you.

Now let's change the name of the directory according to the above mentioned naming convention except no underscore before the version number, instead of underscore for the directory we use a hyphen. The `mv` command can be used to do this.
```bash
mv pretty-ms-7.0.0 node-pretty-ms-7.0.0
```
Now let's change into that directory using the cd command. The contents of the folder should look something like this:
```bash
drwxr-xr-x 3 avronr avronr  4096 Apr 27 11:42 .
drwxr-xr-x 3 avronr avronr  4096 Aug 28 10:00 ..
-rw-r--r-- 1 avronr avronr   175 Apr 27 11:42 .editorconfig
-rw-r--r-- 1 avronr avronr    19 Apr 27 11:42 .gitattributes
drwxr-xr-x 2 avronr avronr  4096 Apr 27 11:42 .github
-rw-r--r-- 1 avronr avronr    23 Apr 27 11:42 .gitignore
-rw-r--r-- 1 avronr avronr  2948 Apr 27 11:42 index.d.ts
-rw-r--r-- 1 avronr avronr  3697 Apr 27 11:42 index.js
-rw-r--r-- 1 avronr avronr   833 Apr 27 11:42 index.test-d.ts
-rw-r--r-- 1 avronr avronr  1109 Apr 27 11:42 license
-rw-r--r-- 1 avronr avronr    19 Apr 27 11:42 .npmrc
-rw-r--r-- 1 avronr avronr   855 Apr 27 11:42 package.json
-rw-r--r-- 1 avronr avronr  3333 Apr 27 11:42 readme.md
-rw-r--r-- 1 avronr avronr 12608 Apr 27 11:42 test.js
-rw-r--r-- 1 avronr avronr    45 Apr 27 11:42 .travis.yml
```
If you're file structure looks like this then you're good. If not retrace your steps and find what went wrong. Next we begin the actual Debianization which is done using a tool called Debmake make the `debian/` directory. so let's install `debmake` using `sudo apt install debmake` and run debmake and see how the files structure looks after that.
 ```
 node-pretty-ms-7.0.0
  ├── debian
  │   ├──  changelog
  │   ├──  compat
  │   ├──  control
  │   ├──  copyright
  │   ├──  patches
  │   ├──  README.Debian
  │   ├──  rules
  │   ├──  source
  │   └──  watch
  ├──  .editorconfig
  ├──  .gitattributes
  ├──  .github
  |     └── funding.yml
  ├──  .gitignore
  ├──  index.d.ts
  ├──  index.js
  ├──  index.test-d.ts
  ├──  license
  ├──  .npmrc
  ├──  package.json
  ├──  readme.md
  ├──  test.js
  └── .travis.yml
```
## Step 3: Creating a source package
Next let's create a source package using `dpkg-source -b .`. If you guys don't have dpkg-source in your system you can get it by running `sudo apt install build-essential`. The output of which should look like this:
{{< code numbered="true" >}}
❯ dpkg-source -b .
dpkg-source: info: [[[using source format '3.0 (quilt)']]]
dpkg-source: info: [[[building node-pretty-ms using existing ./node-pretty-ms_7.0.0.orig.tar.gz]]]
dpkg-source: info: [[[building node-pretty-ms in node-pretty-ms_7.0.0-1.debian.tar.xz]]]
dpkg-source: info: [[[building node-pretty-ms in node-pretty-ms_7.0.0-1.dsc]]]
{{< /code >}}
1. If you look through this log you will see dpkg-source mentioning that it is using the quilt 3.0 source format.
2. You will also notice that is using the .orig.tar.gz file that we created earlier
3. It has build a tar of the debian directory too. But there is also something new here, the file is named `node-pretty-ms_7.0.0-1.debian.tar.xz` where you can see that there is a `-1` after the version number. That number is called the debian revision number. Say we packaged a piece of software and uploaded it to the Debian archves and then someone reported a bug and we made and fix and want to release the package again in the archives with the fix. Now you see we have different 'revisions' of the same package with the same version number. So inorder to help keep a track of this we have debian revision number which is just a count of the number of times that particular version package was released into the Debian archives. So if we have made a bug fix in node-pretty-ms and release it again to the archives it will be releases as `node-pretty-ms_7.0.0-2`.
4. Lastly we have the `node-pretty-ms_7.0.0-1.dsc` file. This file is a security measure if you look into that file you file find information regarding the package extracted from the files in our debian directory and the check-sums of the `node-pretty-ms_7.0.0.orig.tar.gz` and `node-pretty-ms_7.0.0-1.debian.tar.xz`. This is so that others can check the integrity of these files once they have downloaded it from the archives after a Debian Developer uploads it there.

These three files together form the *Debian Source Package*.
### The .dsc file
Let's look at the contents of the .dsc file real quick.
```
Format: 3.0 (quilt)
Source: node-pretty-ms
Binary: node-pretty-ms
Architecture: any
Version: 7.0.0-1
Maintainer: Abraham Raji <avronr@tuta.io>
Homepage: <insert the upstream URL, if relevant>
Standards-Version: 4.1.4
Build-Depends: debhelper (>= 11~)
Package-List:
 node-pretty-ms deb unknown optional arch=any
Checksums-Sha1:
 28bb69ab4720c07dcdd73520b1305137ae354f90 6142 node-pretty-ms_7.0.0.orig.tar.gz
 329139a95e273617ecfdb41466325cede4fa983d 2004 node-pretty-ms_7.0.0-1.debian.tar.xz
Checksums-Sha256:
 256871d7b49dc76e33d841e46c5d36008858aceea9081d9e62c7f5760e65ea33 6142 node-pretty-ms_7.0.0.orig.tar.gz
 3e28122e5bbcb1c771c7431b9af90bc74da45c0461be3a6bc8bcadd73e930707 2004 node-pretty-ms_7.0.0-1.debian.tar.xz
Files:
 ab6e9b3155d0cd73b54d4f0ba8dd0774 6142 node-pretty-ms_7.0.0.orig.tar.gz
 1d7cf58aef1718817cece400cb978ad9 2004 node-pretty-ms_7.0.0-1.debian.tar.xz
```
We can see a bunch information such as the format used, which as we saw before is quilt 3.0. The next two fields are interesting, we mentioned at the beginning of this workshop about names of user facing software vs libraries, the name of the source package vs the name of the binary which the users will use to install this package and such. Next we have Architecture which states what all architectures you can install this package on, here since it's a JS package it can be installed on all architectures and that field should actually say all, but debmake hasn't learnt to detect that yet so it put any there, we'll fix that later. Then we have the version followed by the information about the person working on the package (which debmake got from my .zshrc which is where I've put it and if you use bash you can put it in your .bashrc and Debmake will pick that up). Next we have the Homepage feild where we can put the link to the homepage of the package. Next is Standards-Version which tells us which Debian policy the package follows and set to the version number of the package `debian-policy`, this is also a bit outdated and we will fix it a little later. The next field is Build-Depends which specifies the packages needed to build executable files from the source. Next we have the checksums we mentioned above in three formats, Sha1, Sha256 and MD5. So that's the DSC file.

## Step 4: Building, Making Fixes and Satisfying Lintian
### Building
Now that we have the source package we can build it. We need to be in the `node-pretty-ms-7.0.0` folder to build it. Inside the folder we will execute the `dpkg-buildpackage` command which will build the package for us. The log of that command will look something like this:
```
❯ dpkg-buildpackage
dpkg-buildpackage: info: source package node-pretty-ms
dpkg-buildpackage: info: source version 7.0.0-1
dpkg-buildpackage: info: source distribution UNRELEASED
dpkg-buildpackage: info: source changed by Abraham Raji <avronr@tuta.io>
dpkg-buildpackage: info: host architecture amd64
 dpkg-source --before-build .
 fakeroot debian/rules clean
dh clean
   dh_clean
 dpkg-source -b .
dpkg-source: info: using source format '3.0 (quilt)'
dpkg-source: info: building node-pretty-ms using existing ./node-pretty-ms_7.0.0.orig.tar.gz
dpkg-source: info: building node-pretty-ms in node-pretty-ms_7.0.0-1.debian.tar.xz
dpkg-source: info: building node-pretty-ms in node-pretty-ms_7.0.0-1.dsc
 debian/rules build
dh build
   dh_update_autotools_config
   dh_autoreconf
   create-stamp debian/debhelper-build-stamp
 fakeroot debian/rules binary
dh binary
   dh_testroot
   dh_prep
   dh_installdocs
   dh_installchangelogs
   dh_perl
   dh_link
   dh_strip_nondeterminism
   dh_compress
   dh_fixperms
   dh_missing
   dh_strip
   dh_makeshlibs
   dh_shlibdeps
   dh_installdeb
   dh_gencontrol
dpkg-gencontrol: warning: Depends field of package node-pretty-ms: substitution variable ${shlibs:Depends} used, but is not defined
   dh_md5sums
   dh_builddeb
dpkg-deb: building package 'node-pretty-ms' in '../node-pretty-ms_7.0.0-1_amd64.deb'.
 dpkg-genbuildinfo
 dpkg-genchanges  >../node-pretty-ms_7.0.0-1_amd64.changes
dpkg-genchanges: info: including full source code in upload
 dpkg-source --after-build .
dpkg-buildpackage: info: full upload (original source is included)
dpkg-buildpackage: warning: not signing UNRELEASED build; use --force-sign to override
```
Since it is a simple package it will build successfully. Also, if we look at the parent directory we have a few new files:
```
❯ ls ..
node-pretty-ms-7.0.0                    node-pretty-ms_7.0.0-1.debian.tar.xz
node-pretty-ms_7.0.0-1_amd64.buildinfo  node-pretty-ms_7.0.0-1.dsc
node-pretty-ms_7.0.0-1_amd64.changes    node-pretty-ms_7.0.0.orig.tar.gz
node-pretty-ms_7.0.0-1_amd64.deb
```
Most notable of the new files are the .buildinfo, .changes and .deb files.
- .buildinfo gives us the info regarding the packages that were in our distribution at the time of building.
- .changes gives us a lot of information that you could also find in the .dsc file but also contents of the debian/changelog file.
- the .deb file is something we're all familiar with and is what we use to install the package in our system.

Now if we install the generated .deb file and run the command `dpkg -L node-pretty-ms` which essentially gives us all the files associated with that package in our system you will see that the important files such as the index.js or package.json was not installed.
So even though we have a source package that builds and installs properly we haven't been able to install the package to our system yet. Let's start fixing this package.
### Making fixes
- First let's fix the architecture which can be fixed by editing our debian/control file. In the control file has a section for Architecture which currently is set to any and we will set that to all. Let's open a text editor of choice and make the necessary change. This is probably how it will look at first.
```
Source: node-pretty-ms
Section: unknown
Priority: optional
Maintainer: Abraham Raji <avronr@tuta.io>
Build-Depends: debhelper (>=11~)
Standards-Version: 4.1.4
Homepage: <insert the upstream URL, if relevant>

Package: node-pretty-ms
Architecture: any
Multi-Arch: foreign
Depends: ${misc:Depends}, ${shlibs:Depends}
Description: auto-generated package by debmake
 This Debian binary package was auto-generated by the
 debmake(1) command provided by the debmake package.
```
There are a few things we can fix here, namely:
- Section: this is a JS package so set it to javascript.
- Standards-Version: which is also using an old version, so set it the new one which is 4.5.0
- We could also add the homepage link
- The Architecture of course
- Add a description too. You can find this from the package's homepage or README.md.

Once you've made these changes the file should look something like this:
```
 Source: node-pretty-ms
 Section: javascript
 Priority: optional
 Maintainer: Abraham Raji <avronr@tuta.io>
 Build-Depends: debhelper (>=11~)
 Standards-Version: 4.5.0
 Homepage:https://github.com/sindresorhus/pretty-ms#readme
 
 Package: node-pretty-ms
 Architecture: all
 Multi-Arch: foreign
 Depends: ${misc:Depends}, ${shlibs:Depends}
 Description: Convert milliseconds to a human readable string
```
- Next let's tell Debian what all files to install. We can do so in a file inside the debian directory called install. In this case we want to install the `index.js`, `index.d.ts` and the `package.json` file and in debian we install it to `/usr/share/nodejs/<module-name>/ (one thing to note here is that we don't use the debian name but the actual name of the package for the folder). So open the debian/install file with your favorite text editor and add the required files. Once you've done that the debian/install file should look something like this:
```
❯ cat debian/install
index.js usr/share/nodejs/pretty-ms
index.d.ts usr/share/nodejs/pretty-ms
package.json usr/share/nodejs/pretty-ms
````
Let's build again using `dpkg-buildpackage`. If you installed the previous .deb file you should remove it before you install the new one we just generated. 
Now if we install the new .deb file and run the command `dpkg -L node-pretty-ms`, we can see that all the required files were installed properly.

- Next let's fix the copyright file. We can find the missing information in that file from the upstream `license` file. Right now it will look something like this:
```
Format: https://www.debian.org/doc/packaging-manuals/copyright-format/1.0/
Upstream-Name: node-pretty-ms
Source: <url://example.com>
#
# Please double check copyright with the licensecheck(1) command.

Files:     .editorconfig
           .gitattributes
           .github/funding.yml
           .gitignore
           .npmrc
           .travis.yml
           index.d.ts
           index.js
           index.test-d.ts
           package.json
           readme.md
           test.js
Copyright: __NO_COPYRIGHT_NOR_LICENSE__
License:   __NO_COPYRIGHT_NOR_LICENSE__

Files:     license
Copyright: Sindre Sorhus <sindresorhus@gmail.com> (sindresorhus.com)
License:   Expat
 Permission is hereby granted, free of charge, to any person obtaining a copy of this software and associated documentation files (the "Software"), to deal in the Software without restriction, including without limitation the rights to use, copy, modify, merge, publish, distribute, sublicense, and/or sell copies of the Software, and to permit persons to whom the Software is furnished to do so, subject to the following conditions:
 .
 The above copyright notice and this permission notice shall be included in all copies or substantial portions of the Software.
 .
 THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY, FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM, OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE SOFTWARE.

#----------------------------------------------------------------------------
# Files marked as NO_LICENSE_TEXT_FOUND may be covered by the following
# license/copyright files.

#----------------------------------------------------------------------------
# License file: license
 MIT License
 .
 Copyright (c) Sindre Sorhus <sindresorhus@gmail.com> (sindresorhus.com)
 .
 Permission is hereby granted, free of charge, to any person obtaining a copy of this software and associated documentation files (the "Software"), to deal in the Softw
...
```
Make sensible changes and make a single line should not have more than 80 characters, you can achieve this using the macro functionality in your text editor. Once you're done it should look something like this.
```
 Format: https://www.debian.org/doc/packaging-manuals/copyright-format/1.0/
 Upstream-Name: node-pretty-ms
 Source: https://github.com/sindresorhus/pretty-ms/releases
  
 Files:     *
 Copyright: Sindre Sorhus <sindresorhus@gmail.com> (sindresorhus.com)
 License:   Expat
  Permission is hereby granted, free of charge, to any person obtaining a copy
  of this software and associated documentation files (the "Software"), to deal
  in the Software without restriction, including without limitation the rights
  to use, copy, modify, merge, publish, distribute, sublicense, and/ or sell
  copies of the Software, and to permit persons to whom the Software is
  furnished to do so, subject to the following conditions: .
  The above copyright notice and this permission notice shall be included in
  all copies or substantial portions of the Software.
  .
  THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR
  IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY,
  FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE
  AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER
  LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM,
  OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS  IN
  THE SOFTWARE.
```
### Satisfying Lintian
Now run the `dpkg-buildpackage` again so that the changes we made takes effect. As of now we have resolved all the basic issues. Lintian will help us find the ones that we missed, lintian by default will give short messages but detailed descriptions will help us solve these issues. in order to do that let's set an alias in our shell's rc file (~/.bashr or ~/.zshrc). So pul the following lines in your .bashrc or .zshrc
```bash
alias lintian='lintian -iIEcv --pedantic --color auto'
```
Now run `lintian` and it will throw a bunch of complaints on your face. We need to pay attention to the lines that start with E: or W:. Let's fix an E which complains that ` changelog-is-dh_make-template` and if you read through the description you'll see that the issue is in the debian/changelog. Let's take a look at the file:
```
node-pretty-ms (7.0.0-1) UNRELEASED; urgency=low

  * Initial release. Closes: #nnnn
    <nnnn is the bug number of your ITP>

 -- Abraham Raji <avronr@tuta.io>  Fri, 28 Aug 2020 16:05:03 +0530
```
The issue is that when we wish to package a new package we need to send an Intend to Package mail which is registered as an ITP Bug. This helps co-ordinate packaging work and prevent duplicate work. The bug has a unique identification number. Which we put in the changelog after the hashtag in `* Initial release. Closes: #nnnn`. So we need to check for existing bugs before we make a new ITP bug, we could search for existing bugs at wnpp.debian.net. Now we can solve this error by removing the line `<nnnn is the bug number of your ITP>` and add the ITP bug number. For the sake of this workshop we're going to add a random number there. Once we do that the file should look something like this:
```
node-pretty-ms (7.0.0-1) UNRELEASED; urgency=low
 
   * Initial release. Closes: #982937
 
  -- Abraham Raji <avronr@tuta.io>  Fri, 28 Aug 2020 16:05:03 +0530
```
Now we still have a lot more complaints to solve but to keep the size of this page somewhat reasonable, I'm not going to include fixes to all the complaints here. Here’s an [example](https://salsa.debian.org/avron/node-cjson/-/commits/master) from a package I updated a while ago. It is sort of a jackpot in the sense it has a bunch of fixes to common lintian complaints I’ve come across. Commit messages could be a little better and more creative though.

Here we did a lot of things manually like visit different sites and collect tarballs and stuff but actually we didn't have to do that. We actually have a tool that does most of what we already did called npm2deb. We went throug all taht in this tutorial because it is important you know what's happening under the hood.

Here's a [wiki page](https://wiki.debian.org/Javascript/Nodejs/Npm2Deb) giving a tutorial on how to use npm2deb.

Here's a [wiki page](https://wiki.abrahamraji.in/updating-deb-pkgs/) I wrote regarding updating packages.
