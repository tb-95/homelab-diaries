# Phase 2: CasaOS – First Services and First Questions

**Date:** April 2026

## What I Wanted to Achieve

Get the server actually *doing* something useful. AdGuard Home for ad blocking, a media library for movies, and a private chat server for the family.

## What We Did

1. Deployed **AdGuard Home** – set it as the DNS server for the entire home network via the router.
2. Set up **Jellyfin** – connected to a Synology NAS (DS115j) via NFS, started building a media library.
3. Started experimenting with **Matrix** (chat server) – wanted a private alternative to WhatsApp.
4. Asked *a lot* of questions about permissions, mount options, and security.

## Configuration (Highlights)

### AdGuard Home
- Upstream DNS: Quad9 (`https://dns.quad9.net/dns-query`) and Cloudflare (`https://dns.cloudflare.com/dns-query`)
- DNS cache: increased from 4 MB to 16 MB for better performance
- Blocklists: HaGeZi's Pro++, OISD Big, AdGuard DNS filter, and several others

### Jellyfin
- Mounted NAS via `/etc/fstab` with `ro` (read-only) for safety
- Path: `192.168.1.80:/volume1/Media /mnt/nas-media nfs ro,...`

Full configs available in [`/configs/`](../configs/).

## AHA! Moments & My Thoughts

- **"UFW not found"** – My first encounter with "not everything is installed by default". Learned that minimal Ubuntu Server installations are truly minimal, and that's okay.
- **"Do I really need UFW?"** – Discovered that Docker bypasses UFW anyway. Decided to skip the firewall for now, but learned why it exists and that I can add it later. *Lesson: security is a journey, not a single checkbox.*
- **Read-only vs read-write for media** – Asked: "Why would I give Jellyfin write access to my movies?" Answer: I shouldn't. Set the NAS mount to `ro` (read-only). This was my first lesson in the **principle of least privilege**.
- **"Can't I use a domain name instead of IP:port?"** – This was the seed that later grew into Nginx Proxy Manager. Already thinking about simplicity.
- **First Matrix struggles** – The chat server was complicated. Federation, TLS, clients... I wasn't ready yet, but I planted the flag: "I want my own chat."

## What I Learned About Myself

I wasn't satisfied with "it works". I wanted to understand *why* it works. That's what separates a tinkerer from an engineer.

## Result

A functional home server running essential services. But I was already hitting the limits of CasaOS – it was time to take the training wheels off.

→ Next: [Phase 3: Breaking Free from CasaOS](03-casaos-breaking-free.md)