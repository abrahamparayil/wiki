---
title: "Setting up Sid Development Environment for Debian Packaging"
date: 2020-08-27T12:44:21+05:30
tags: ["debian", "gnu/linux","packaging"]
url: sid-env
draft: false 
---

## Why a sid env?
Debian packages are developed for Debian through the Unstable distribution or Unstable Branch. Debian almost always has three distributions going for it at any point of time. There is a stable branch which is currently Buster which is what users put on their production machines and is recommended by Debian for it's users as it's officially supported by the community and recieves regular updates that resolves bugs and provides secuirity patches. Concurrently Debian also maintains two other Distributions: testing which currently is Bullseye and unstable which always will be sid. The way this works is when a new release is made the Debian Deveopers discuss and decide what features they wish to implement in the next release. They work on these features in Sid which is used to package new packages that are uploaded to the sid archives and once they've proven their usability they're moved to the testing archives where they're further tested and worked upon. When all the features that was decieded has been implemented and the distribution contains no release critical bugs, a new version of Debian is released that is the Testing becomes the new stable and the current stable becomes old-stable. The next stable version will be current testing which is Bullseye and Buster at that point will become old-stable.

So in short in order to do packaging we need a sid env set up somehow. you can Dual Boot, use VMs, use container tech such as docker or LXD or create a schroot.

## Why schroot?
I personally prefer schroot. Schroot is light-weight and easy to set-up and use but the real feature that sells schroot for me is that it uses the same home folder that my base distro uses. So I can access the files that I worked from schroot without entering schroot and there's a lot less duplicatiion of config files.

## Setting a sid env with Schroot.

If you already have a debian stable or debian-based distribution (arch also has schroot package, though if you have a different distro, you need to check if it has schroot package), this option is best for you. These instructions assume you are already running a Linux Distribution (preferably Debian Stable).

```bash
sudo apt install schroot debootstrap
```
Create root file system:
The `/srv` contains data served by your system, it's a relatively new folder in Unix and not a lot of distributiions use it. We will create a folder in the /srv directory to home the root files of our debian sid env.
```bash
mkdir -p /srv/chroot/debian-sid
```
Now we will use `debootstrap` to pull the Debian sid files.
```bash
debootstrap sid /srv/chroot/debian-sid
```
Now create a text file `/etc/schroot/chroot.d/debian-sid` with your favorite text editor and add the following lines in it:

```bash
[debian-sid]
description=Debian Sid for building packages suitable for uploading to debian
type=directory
directory=/srv/chroot/debian-sid
users=<your username>
root-groups=root
personality=linux
preserve-environment=true
```
Where <your username> is an underprivileged user on your host system.

And there you go! We have successfully set up a Debian sid env that you can now use to package Debian packages.

## Using Schroot.

To enter schroot as a superuser, enter the following from a terminal.
```bash
sudo schroot -c debian-sid
```

`W: Failed to change to directory '/ ...` is comman warning and can be ignored for now.

Now that you're in schroot play around a bit. Install your favorite applications:
```bash
apt-get update && apt-get install <some-package>
```
Once you're done just type in `exit` and hit enter to exit the schroot.

To run schroot as normal user, type in:
```bash
schroot -c debian-sid
```
You will notice that your home folder in your base system is still your home folder and all the files are accessible to you and your configs such as your .bashrc will work here too.

## Setting up sudo
Maybe you don't want to exit out of your normal user and re-enter schroot as the root user everytime you want to install a dependency for a package and want sudo. You can set it up the exact whay you would in regular distro.

First, enter the schroot as root with the command given above and install sudo with
```bash
apt install sudo
```
Once that is done add your user to the sudo group with this command:
```bash
usermod -aG sudo <your username>
```
Now you can exit from the root shell and re-enter schroot as a normal user and run sudo.

## How to make sre you've set up everything correctly?
 Run the command `cat /etc/debian_version` and it should give you an output as shown below.
 ```bash
 ❯ cat /etc/debian_version
bullseye/sid
```