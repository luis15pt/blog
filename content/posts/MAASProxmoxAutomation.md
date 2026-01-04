---
title: "I Spent 50 Hours Automating a 5-Minute Proxmox Install (And I'd Do It Again)"
date: 2026-01-04
author: "Luis Sarabando"
tags: ["automation", "proxmox", "maas", "infrastructure", "devops", "virtualization"]
---

Hello! Maurice Moss here again. Today I'm going to tell you about a project that perfectly encapsulates the engineering mindset: spending an absolutely unreasonable amount of time automating something that takes five minutes to do manually.

You know that XKCD comic about whether automation is worth it? The one where you calculate if the time saved exceeds the time invested? Well, I looked at that chart, laughed, and said "challenge accepted" in the wrong direction.

## The "Problem" (Or: When 5 Minutes is Simply Unacceptable)

Picture this: You need to install Proxmox VE on a server. The manual process is straightforward:
1. Download the ISO
2. Boot from USB
3. Click through the installer
4. Configure networking
5. Make a cup of tea

Total time: Maybe 5-10 minutes if you're being particularly thorough about the tea.

But here's the thing - what if you need to do this on *multiple* servers? What if you need to do it *consistently*? What if you just really, really enjoy making computers do your bidding through an elaborate chain of automation tools?

That's where [MAAS-Proxmox](https://github.com/luis15pt/MAAS-Proxmox) comes in - my answer to a question absolutely nobody was asking.

## The Solution (Or: How I Learned to Stop Worrying and Love Over-Engineering)

I've created a fully automated pipeline that builds custom Proxmox VE 9.1 images and deploys them through MAAS (Metal as a Service) with zero manual intervention. It's beautiful. It's elegant. It took approximately 50 hours to build something that saves me 5 minutes per deployment.

The math checks out if you plan to deploy Proxmox... *checks calculator* ...600 times. Which I absolutely will. Eventually. Probably. Don't judge me.

### What Does It Actually Do?

The system uses:
- **Packer** to build the base image (because manually creating images is for people with better time management)
- **Docker** to containerize the entire build process (because why install dependencies on your host when you can install Docker instead?)
- **Ansible** to configure everything just right (because bash scripts are too mainstream)
- **Cloud-init** for initial bootstrapping (because consistency is important)
- **Curtin hooks** for MAAS integration (because regular hooks weren't complicated enough)

The result? A 2.4GB compressed image that deploys Proxmox VE 9.1 on bare metal in about 10-15 minutes, completely hands-free.

Build time? 45-55 minutes. But that's *automated* time, which means I can go make tea, respond to emails, contemplate my life choices, and come back to a perfectly configured image.

## The Features (Or: Complexity For Its Own Sake)

### Network Configuration Automation

The crown jewel of this over-engineering exercise is the automatic network configuration converter. MAAS uses netplan. Proxmox uses ifupdown2. They're incompatible. Naturally.

So I wrote a system that:
1. Reads MAAS netplan configuration
2. Translates it to Proxmox ifupdown2 format
3. Handles bonding (active-backup, LACP, you name it)
4. Manages VLANs with tagged interfaces
5. Sets up bridges automatically
6. Configures static routes

Could I have just manually configured the network on each server? Yes. Would it have taken about 3 minutes? Also yes. But where's the fun in that?

### Security-First Approach

The image uses SSH key-based authentication exclusively - no default passwords, no shared credentials, no "admin/admin" nonsense. MAAS injects your SSH keys during deployment, so you get secure access from the moment the server comes online.

Is this more secure than password-based auth? Absolutely. Does it also mean I never have to remember which variation of "ProxmoxAdmin123!" I used on which server? That's just a happy coincidence.

## The Technical Deep Dive (Or: Let Me Explain Why This Was Totally Necessary)

The build pipeline runs entirely in Docker because I learned the hard way that installing Packer, QEMU, KVM, and all the associated dependencies on your host system is a recipe for an evening you won't enjoy.

The image is based on Debian 13 (Trixie) because Proxmox 9.1 needs a modern foundation. It includes:
- Full Proxmox VE 9.1 installation
- Automatic service startup (pve-cluster, pveproxy, pvedaemon)
- vmbr0 bridge pre-configured (because networking should work out of the box)
- SSH key-based authentication ready to go

The entire thing integrates with MAAS 3.x and requires UEFI boot, because we're doing this properly or not at all.

## When Should You Use This? (Or: Is This Actually Practical?)

You should absolutely use MAAS-Proxmox if:

1. ✓ You manage multiple Proxmox servers and value consistency
2. ✓ You're using MAAS for bare metal provisioning
3. ✓ You need reproducible deployments
4. ✓ You want to version-control your infrastructure
5. ✓ You enjoy the philosophical satisfaction of automation

You probably shouldn't use this if:

1. ✗ You have one server and no plans for more
2. ✗ You don't use MAAS
3. ✗ You value your time in a conventional, boring way
4. ✗ You think "good enough" is actually good enough

## The Bottom Line (Or: In Defense of Automation)

Look, I know what you're thinking. "Maurice, you spent 50 hours automating a 5-minute task. That's absurd."

And you're absolutely right. It is absurd.

But here's what I actually built:
- **Consistency**: Every deployment is identical, no human error
- **Speed at Scale**: Deploy 10 servers as easily as 1
- **Version Control**: Infrastructure as code, properly tracked
- **Reproducibility**: Disaster recovery becomes trivial
- **Knowledge**: I now understand every component intimately
- **Joy**: The pure satisfaction of watching machines do exactly what I told them to

Could I have manually installed Proxmox 600 times in the time I spent building this? Probably not, because I'd have lost my mind somewhere around deployment 47.

But more importantly: automating things isn't just about saving time. It's about reliability, repeatability, and the quiet satisfaction of knowing your infrastructure is defined in code, tested, and deployable with a single command.

Also, it's really cool to watch MAAS grab a bare metal server, deploy your custom image, configure complex networking, and hand you a fully functional Proxmox cluster - all while you're making tea.

## Try It Yourself

If you're similarly afflicted with the automation bug, check out [MAAS-Proxmox on GitHub](https://github.com/luis15pt/MAAS-Proxmox). The README has full build and deployment instructions.

Requirements are modest:
- Ubuntu 22.04+
- Docker and Docker Compose
- KVM support
- About 5GB disk space
- A willingness to spend time to save time (eventually)

If you need me, I'll be in my office, calculating exactly how many deployments I need before this project breaks even on time invested.

Spoiler: It's a lot.

---

*Written by Luis Sarabando, channeling his inner Maurice Moss with assistance from Claude.ai*

P.S. The best part about automation? Once it's built, you get to watch it work perfectly dozens of times, and each time you think "I'm so glad I automated this" instead of "why am I installing Proxmox again?" That satisfaction is worth at least... *checks notes* ...carry the one... okay, maybe not 50 hours, but definitely worth it. Trust me.
