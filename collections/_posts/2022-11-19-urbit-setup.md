---
layout: post
title: "urbit setup"
date: 2022-11-19
authors: ["AJ LaMarc"]
description: development environment for dummies
categories: ["urbit"]
---

Developing on Urbit is easier than it looks. That being said, setting up a local development environment (including a frontend, desk, and gall agent) takes quite a few steps, and documentation for it is scattered. This post aims to be a single source of truth for that process.

## Part one: fakezod

Make a directory for urbit files:

```bash
~/$ mkdir urbit && cd urbit
```

Download the [latest binary](https://urbit.org/getting-started/cli) for your operating system. On Linux:

```bash
~/urbit$ curl -L https://urbit.org/install/linux64/latest | tar xzk --strip=1 && ./urbit
```

Download the latest developer pill:

```bash
~/urbit$ curl -O https://storage.googleapis.com/media.urbit.org/developers/dev-latest.pill
```

Boot a new fakezod using the developer pill (this takes a few minutes):

```bash
~/urbit$ ./urbit -B dev-latest.pill -F zod
```

```hoon
~zod:dojo> |exit
```

These first steps only need to be done once. We can backup our ship now to make restarting from here easy:

```bash
~/urbit$ cp -r zod zod.new
```

To restore this backup:

```bash
~/urbit$ rm -r zod && cp -r zod.new zod && ./urbit zod
```

## Part two: agent and frontend

In this demo, we are going to name our application `%hue`. Replace any references to `hue` below with the intended name of your agent / desk / application.

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

If it requests to remove git files, say yes. Likewise:

```bash
~/urbit/hue$ cd desk/ && rm -r .git/
```

Now configure `zod` to recognize a `hue` desk:

```hoon
~zod:dojo> |new-desk %hue
~zod:dojo> |mount %hue
~zod:dojo> |exit
```

We want our future changes inside `/hue/desk` to automatically be copied into the desk inside of `zod`, for ease of testing. Leave this command running in the background:

```bash
~/urbit$ watch cp -LR hue/desk/* zod/hue/
```

One problem is that the skeleton agent is named `mine`, but we need it to reference `hue`. Inside of the `/hue/desk` folder:

- replace all references to `mine` with `hue`: these should be in desk.bill, desk.docket-0, and mine.hoon
- inside desk.docket-0, remove `site+/apps/mine` and replace it with `glob-http+['https://bootstrap.urbit.org/glob-0v5.hurm4.ejod5.ngg9h.iub9i.n1j7o.glob' 0v5.hurm4.ejod5.ngg9h.iub9i.n1j7o]`. Also, add `base+'hue'`.
- rename mine.hoon to hue.hoon

These changes will automatically propagate to our zod, thanks to the watch command.
Now, let's get our new Gall agent running:

```hoon
~zod:dojo> |commit %hue
~zod:dojo> |install our %hue
~zod:dojo> :hue +dbug
```

You should see a default state `[%0 ~]` printed as output from the agent. Keeping zod running, start up the frontend:

```bash
~/urbit/hue/ui$ npm i && npm run dev
```

Open `http://localhost:3000/apps/grid/`. When prompted for a password, type `+code` into the dojo to retrieve it. After signing in, you should now be able to see and open the `hue` app!

From here, testing changes is relatively easy: frontend changes will be hot-reloaded. For changes on the hoon side, make sure to `|commit %hue`. To hard restart the Gall agent (useful for state changes):

```hoon
~zod:dojo> |nuke %hue
~zod:dojo> |rein %hue [& %hue]
```

Another post will cover final deployment steps onto the Urbit network.
