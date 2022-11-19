---
layout: post
title:  "urbit setup"
date:   2022-11-19
authors: ["AJ LaMarc"]
description: development environment for dummies
categories: ["urbit"]
---

Developing on Urbit is easier than it looks.  That being said, setting up a local development environment (including a frontend, desk, and gall agent) takes quite a few steps, and documentation for it is scattered.  This post aims to be a single source of truth for that process.

**From the top:**

Make a directory for urbit files
```console
~/$ mkdir urbit && cd urbit
```
Download the [latest binary](https://urbit.org/getting-started/cli) for your operating system.  On Linux:
```bash
~/urbit/$ curl -L https://urbit.org/install/linux64/latest | tar xzk --strip=1 && ./urbit
```
Download the latest developer pill:
```shell
~/urbit/$ curl -O https://storage.googleapis.com/media.urbit.org/developers/dev-latest.pill
```
