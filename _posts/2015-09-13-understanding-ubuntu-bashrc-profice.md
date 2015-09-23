---
layout:     post
title:      Understanding Ubuntu bashrc and profile
date:       2015-09-23 21:19:19
summary:    See what the different elements looks like. Your markdown has never looked better. I promise.
categories: dev linux
tags:
- linux
- ubuntu
---


script|wide|run after login|run when terminal opens
-------|-------|-------|-------|-------
`/ect/bash.bashrc`| system | Y | Y
`~/.bashrc`| user | Y | Y
`/etc/profile` `/etc/profile.d/*.bash`| system | Y | N
`~/.profile`| user | Y | N

> `/etc/profile` calls `/etc/bash.bashrc` directly


* set environmental items, e.g. PATH, JAVA_HOME
  * system wide: `/etc/profile`, `/etc/profile.d/*.bash`
  * user wide: `~/.profile`, `~/.bash_profile`
* set command aliases and functions used by other bash shell
  * system wide: `/etc/bash.bashrc` (Ubuntu) or `/etc/bashrc` (Redhat)
  * user wide: `~/.bashrc`

for more, refer to `man bash`


