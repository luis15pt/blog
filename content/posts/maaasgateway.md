---
title: "MAAS Default Gateway Configuration: When Two Networks Play Tug-of-War"
date: 2024-05-15
author: "Luis Sarabando"
tags: ["maas", "networking", "infrastructure", "automation"]
---


Hello there, fellow IT enthusiasts! Today I'm going to walk you through a peculiar little quirk in MAAS (Metal as a Service) that's about as intuitive as explaining to Jen what RAM actually does.

## The Problem with Multiple Networks (Or: "Why Is My Traffic Going That Way?")

You see, when you have two networks in MAAS, it's like having two doors to leave a building - perfectly reasonable until you realize MAAS can't decide which one should be the main exit. The GUI, much like Roy's dating strategy, completely fails to provide an option for selecting your default gateway.

It's as if MAAS is saying, "I'll just pick one randomly, shall I?" which is about as helpful as Moss's fire extinguisher email.

## The Solution: CLI to the Rescue!
Now, don't panic! We can fix this with a bit of command-line magic. It's quite simple really, like making a cup of tea... actually, that's quite complicated when you think about it.

First things first, you'll need to log in to the MAAS API. This step is absolutely crucial - like remembering to wear pants to work:

```bash
maas login admin http://192.168.200.99:5240/MAAS/api/2.0/
```

This will ask for your API key (leave empty for anonymous access, though that's about as useful as Jen's computer skills). You can get your API key from the WebUI - it's under your user profile, not hidden in a black box like the Internet.

Once you're logged in, here's the command to set your default gateway:

```bash
maas admin interface set-default-gateway -d r47ry6 bond0.20
```


Where:

 - `r47ry6` is your machine ID (like a serial number, but less serial and more... numbery)
 - `bond0.20` is the interface you want as your default gateway

This command is like telling the Internet exactly which box it should use to send your packets. Very important stuff!

## Why This Matters (Or: "The Consequences of Incorrect Routing")
Having the wrong default gateway is like sending Roy to fix a computer problem on the 7th floor - technically possible, but likely to end in disaster. Your traffic might go through unexpected routes, performance could suffer, and worst of all, your network diagram would be incorrect (and we all know how devastating that can be).

The Technical Bits (For Those Who Actually Understand Networking)
When you run the command, you'll get a lovely JSON response showing all the details of your interface configuration. It's quite satisfying, like when you finally get all the cables neatly organized (which lasts approximately 3.7 minutes before someone needs to plug something in).

The output confirms your VLAN settings, MTU configuration (9000 in this case - jumbo frames, very fancy!), and the space assignment ("Public").

## Conclusion
And there you have it! A perfectly configured default gateway that's almost as satisfying as solving the Grand Unified Theory of Mathematics... which I did do, by the way, but I lost the paper I wrote it on.

Remember, with great networking comes great responsibility, and with MAAS comes... well, occasionally confusing interface choices.

If you need me, I'll be in my office, making sure all my default gateways are properly configured. Not because I'm obsessed, just because it's the right thing to do.

---

P.S. If anyone needs help with their MAAS configuration, remember: Have you tried setting the default gateway and then turning it off and on again?