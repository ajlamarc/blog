---
layout: post
title:  "urbit setup"
date:   2022-11-19
authors: ["AJ LaMarc"]
description: development environment for dummies
categories: ["urbit"]
---

Developing on Urbit is easier than it looks.  That being said, setting up a local development environment (including a frontend, desk, and gall agent) takes quite a few steps, and documentation for it is scattered.  This post aims to be a single source of truth for that process.

## Part one: fakezod

Make a directory for urbit files:
```bash
~/$ mkdir urbit && cd urbit
```
Download the [latest binary](https://urbit.org/getting-started/cli) for your operating system.  On Linux:
```bash
~/urbit$ curl -L https://urbit.org/install/linux64/latest | tar xzk --strip=1 && ./urbit
```
Download the latest developer pill:
```bash
~/urbit$ curl -O https://storage.googleapis.com/media.urbit.org/developers/dev-latest.pill
```
Boot a new fakezod using the developer pill:
```bash
~/urbit$ ./urbit -B dev-latest.pill -F zod
```
As of now (11/19/2022) the `|new-desk` command hasn't been released yet, so we will add it manually to the `%base` desk files.
```bash
~zod:dojo> |mount %base
~zod:dojo> |exit

~/urbit$ cd zod/base/gen/hood/ && curl -O https://raw.githubusercontent.com/urbit/urbit/bad5013c8a008ccf37761fbff63e4b04c4fca95b/pkg/arvo/gen/hood/new-desk.hoon
```
These first steps only need to be done once.  We can backup our ship now to make restarting from here easy:
```bash
~/urbit$ cp -r zod zod.new
```
To restore this backup:
```bash
~/urbit$ rm -r zod && cp -r zod.new zod && ./urbit zod
```

## Part two: agent and frontend

In this demo, we are going to name our application `%hue`.  Replace any references to `hue` below with the intended name of your agent / desk / application.

Start by using the [create-landscape-app](https://github.com/urbit/create-landscape-app) repo to build a starter UI / desk:
```bash
~/urbit$ npx @urbit/create-landscape-app --typescript
? What should we call your application? › hue
? What URL do you use to access Urbit? › http://localhost:8080
```
The URL can be retrieved from Urbit, i.e. after running `./urbit zod`, it prints `http: web interface live on {URL}`.

Now we will configure Dalten's new [agent skeleton](https://github.com/dalten-collective/agent-skeleton) to be the desk in our application.
```bash
~/urbit$ cd hue/ && git clone https://github.com/dalten-collective/agent-skeleton.git
~/urbit/hue$ rm -r desk && cp -r agent-skeleton desk && rm -r agent-skeleton
```
If it requests to remove git files, say yes.  Likewise:
```bash
~/urbit/hue$ cd desk/ && rm -r .git/
```
Now back in our home directory, configure `zod` to recognize a `hue` desk:
```bash
~/urbit$ ./urbit zod
~zod:dojo> |commit %base
~zod:dojo> |new-desk %hue
~zod:dojo> |mount %hue
~zod:dojo> |exit
```