---
title: "Using GnuPG"
date: 2020-11-26T11:24:23+05:30
tags: ["privacy", "gnupg"]
url: gpg
---

> GNU Privacy Guard (GnuPG or GPG) is a free (as in freedom) software 
> replacement for Symantec's PGP cryptographic software suite, and is compliant
> with RFC 4880, the IETF standards-track specification of OpenPGP.

Basically a piece of software that lets you generate public-private key pairs 
which can then be used for things like encrypting your mail, signing commits, 
storing passwords and more.

> Modern versions of PGP are interoperable with GnuPG and other 
> OpenPGP-compliant systems. 

So technically it doesn't matter what system you use as long as you make sure
that they're compliant with the OpenPGP standard but GnuPG is free software and
so that is what I use.

## Generating a GPG key pair
You can genrate a key pair with the following command:
```bash
gpg --full-gen-key
```
Upon which you'll recieve the following menu:
```
gpg (GnuPG) 2.2.20; Copyright (C) 2020 Free Software Foundation, Inc.
This is free software: you are free to change and redistribute it.
There is NO WARRANTY, to the extent permitted by law.

Please select what kind of key you want:
   (1) RSA and RSA (default)
   (2) DSA and Elgamal
   (3) DSA (sign only)
   (4) RSA (sign only)
  (14) Existing key from card
Your selection?
```
Here GPG is asking you to choose an encryption algorithm that your key will use.
I would suggest you either go with DSA or RSA, simply because both have well 
defined specifications, are widely used and at the time of me writing this blog
equally secure. However it's always a good idea to do some research on your own.

Once you make your choice you'll have to make another decision, Keysize.
```
RSA keys may be between 1024 and 4096 bits long.
What keysize do you want? (3072)
```
I usually go with 4096 because why not? It takes a few extra seconds to make but
that's an accaptable trade of for me.

Then we choose how long we want the key to be valid.
```
Please specify how long the key should be valid.
         0 = key does not expire
      <n>  = key expires in n days
      <n>w = key expires in n weeks
      <n>m = key expires in n months
      <n>y = key expires in n years
Key is valid for? (0)
```
For most cases you should be fine with the `key does not expire option` but if
you're not like me and can handle keeping track of the key's expiration date
and is okay with generating new one when the current one is expired go ahead and
set a time period for automatic expiration of the key.

```
Is this correct? (y/N)
```
Put a `y`.

Now to add your information to the key.
```
GnuPG needs to construct a user ID to identify your key.

Real name: Name
Email address: email@mail.com
Comment: Comment
You selected this USER-ID:
    "Name (Comment) <email@mail.com>"
Change (N)ame, (C)omment, (E)mail or (O)kay/(Q)uit?
```
Unless your version of GPG has some weird bug this step should be go smoothly.
If everything looks good type in `O` and hit Enter.

At this point you'll receive a message about how your key is being generated and
GPG needs entropy to create random numbers and so on. Just keep using the system
as you would and depending on your algorithm and keysize the process should take
anywhere between a second to a couple of seconds.
## What to call my key?
Now that you have a key what do you call it? GPG is the software here. PGP is 
the actual system. You could say 'GPG Key' and that is widely used too but that 
wouldn't exactly be accurate. The most accurate term would be an 'OpenPGP key'.

## Backing Up keys
To export your GPG private key, run the following command on your terminal:
```
gpg --export-secret-keys --armor name > /path/to/secret-key-backup.asc
```
As an addition, you can also backup the GPG trust database.
```
gpg --export-ownertrust > /path/to/trustdb-backup.txt
```
Now to restore the GPG Key:
```
gpg —-import /path/to/secret-key-backup.asc
```
And to restore your GPG trust database, run the following command:
```
gpg --import-ownertrust < /path/to/trustdb-backup.txt
```
## Signing commits
First we need to list all the keys first:
```
gpg --list-secret-keys --keyid-format LONG <your_email>
```

This will list all your GPG keys. Copy the GPG key ID that starts with 'sec' and
add this as the key to sign commits in git using the following commit:
```
git config --global user.signingkey <GPG Key ID>
```
Optionally you can ask git to sign commits automatically using the following 
command:
```
git config --global commit.gpgsign true
```
