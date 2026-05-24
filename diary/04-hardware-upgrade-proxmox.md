# Phase 4: New Hardware and the Proxmox Revelation

**Date:** Mid-May 2026

## What I Wanted to Achieve

The old laptop (Intel i5-4310U, 8 GB RAM) was reaching its limits. I wanted to add more services – Immich, Nextcloud, a password manager – but didn't have the resources. Then I got a Lenovo L390 with an i5-8265U and 16 GB RAM. Now I had the power, but also a choice: just install Ubuntu again, or do something bigger?

I also kept thinking about my career. I didn't just want a server; I wanted to *become* the kind of person who builds one professionally. That meant thinking like a system administrator, not a hobbyist.

## What We Did

1.  Compared the two CPUs and their capabilities – the new one was a massive leap: 4 cores / 8 threads vs 2/4, better GPU, lower power idle.
2.  Instead of immediately installing Ubuntu, we researched **Proxmox** – a virtualization platform that professionals use to run multiple isolated servers on one machine.
3.  Made the decision: Proxmox, not bare-metal Ubuntu. This would let me run each service in its own **LXC container**, with its own IP, resource limits, and easy backup.
4.  Installed Proxmox 9.1 on the Lenovo, set up networking (static IP `192.168.1.17`), and got familiar with the web GUI.
5.  Mounted both NAS devices directly on the Proxmox host via `/etc/fstab` – this way, every future container could access them through bind mounts. This was a game-changer: one-time configuration, used by all.
6.  Created a new LXC container with Ubuntu 26.04 as a "docker host" inside Proxmox – later we'd replace this with separate containers per service, but it was the first step.

## AHA! Moments & My Thoughts

- **"Should I stay on 22.04 or go for the newest?"** – We debated Ubuntu 22.04 vs 24.04 vs even 26.04. The research showed that newer kernels and drivers would benefit my new GPU for Jellyfin transcoding, and that LTS (Long Term Support) is essential for stability. The final answer: 26.04 LTS.
- **"Why not just put everything in one Ubuntu VM?"** – That's how we started. But as we talked, I realized that isolating services is safer and more professional. If Jellyfin crashes, it won't take down my DNS (AdGuard) or my reverse proxy. This is the "blast radius" concept.
- **"What even is Proxmox?"** – At first, it sounded scary. But it's just a Debian-based OS with a web interface that manages containers and VMs. It's like having a mini data center on my laptop. The ability to snapshot a container before making changes, and roll back if I break something, gave me confidence to experiment.
- **"How will the containers see the NAS?"** – Learned about **bind mounts** at the Proxmox level. The NAS is mounted once on the host, and then I just "pass through" the folders to any container that needs them. No more fstab inside every server!
- **RAM and future-proofing** – I considered upgrading from 2x8GB to 1x16GB + 8GB, but we discovered that mixing sizes would slow down the dual-channel memory. Stuck with 2x8GB, but the analysis showed 16 GB is plenty for the services I planned. This was my first taste of hardware resource planning.

## Why This Phase Matters

This was the moment I stopped being a "kid with a server" and started thinking like an architect. I chose the tooling that companies use. I designed a system that's modular, safe, and ready to grow.

## Result

A Proxmox host with NAS mounts ready, and the first LXC containers waiting to be born. The stage was set for a truly professional homelab.

→ Next: [Phase 5: WireGuard – VPN at the Router Level](05-wireguard-vpn.md)