# Phase 1: The Beginning – Ubuntu, CasaOS, and a Spark

**Date:** April 2026

## What I Wanted to Achieve

Replace paid services (Netflix, Google Photos) with my own server. Start simple, because I'm a total beginner.

## What We Did

1. Took an old laptop (Intel i5-4310U, 8 GB RAM) and installed Ubuntu Server 22.04.
2. Used CasaOS – a simple graphical interface for managing Docker containers.
3. Deployed AdGuard Home (ad blocking) and a chat server.
4. Started asking questions: how to connect NAS, how to set up a media library, how to use WireGuard.

## AHA! Moments & My Thoughts

- **CasaOS is easy, but limiting.** I could click buttons, but I didn't understand what was happening. If I wanted to change careers, I needed to go deeper.
- **First encounter with permissions.** I tried to access a mounted NAS folder and got "Permission denied". Learned about `chmod`, `chown`, and the difference between `ro` (read-only) and `rw` (read-write).
- **Why not just use a cloud service?** Because I wanted control. And because tinkering is fun. The moment AdGuard blocked the first ad on my phone, I was hooked.

## Result

A basic home server running on an old laptop. It worked, but I knew I would soon outgrow it. The real journey was about to begin.

→ Next: [Phase 2: Leaving CasaOS for Docker and Portainer](02-docker-portainer.md)