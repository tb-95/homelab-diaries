# 🏠 Homelab Diaries: From Netflix to Self-Hosted Cloud

**Author:** Tomáš Bednář ([@tomasbednar](https://github.com/tomasbednar))  
**Status:** 🟢 Active project (started April 2026)

## Why This Project Exists

I was paying for Netflix, Google Photos, and WhatsApp. Prices kept rising, and I had zero control over my data. I also owned an old laptop collecting dust.  

One evening I asked myself: *"What if I could build my own cloud?"*  

I had no IT background. I started with Ubuntu and CasaOS – a simple, graphical tool. It worked, but I quickly felt limited. I wanted to understand what was happening under the hood. I wanted to switch careers. That's when the real journey began.

This repository is the living diary of that journey. It's not just code – it's the story of a non-IT person learning Linux, Docker, Proxmox, networking, and security from scratch.

## What You'll Find Here

- **📖 Diary entries:** Every major phase, with my thoughts, mistakes, and "AHA!" moments.
- **🧰 Configurations:** Real-world `docker-compose.yml` files I use daily.
- **🗺️ Architecture diagrams:** How my network and services are connected.

## Current Stack (May 2026)

- **Hardware:** Lenovo L390 (i5-8265U, 16 GB RAM), Synology DS115j + DS213j
- **Virtualization:** Proxmox 9.1, LXC containers (1 service = 1 container)
- **Services:** AdGuard Home, Nginx Proxy Manager, Jellyfin (media server), Matrix (chat), more coming
- **Security:** WireGuard VPN on router, SSH keys, fail2ban

## Why I'm Sharing This

I want to show that you don't need a degree to build a homelab. If you're curious and willing to learn, you can do it. And maybe a future employer will see that too.

→ Start reading the diary: [`diary/01-casaos-start.md`](diary/01-casaos-start.md)