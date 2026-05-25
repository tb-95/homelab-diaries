# Phase 3: Breaking Free from CasaOS

**Date:** Late April 2026

## What I Wanted to Achieve

CasaOS was starting to feel like a cage. I wanted to understand Docker properly, use `docker-compose` files, and manage everything through Portainer. This was also the moment I started thinking about a career change.

## What We Did

1. Uninstalled CasaOS using the official uninstall script.
2. Cleaned up leftover directories (`/var/lib/casaos`) and network interfaces.
3. Installed Docker Engine and Docker Compose from the official Docker repository.
4. Deployed **Portainer** – a web UI for managing containers.
5. Re-deployed AdGuard Home and Jellyfin, but now via `docker-compose.yml` files.
6. Started creating a proper directory structure: `/opt/docker/<service>/`.

## AHA! Moments & My Thoughts

- **"Portainer doesn't have a GUI?"** – My first confused moment. I was looking for a desktop app, but Portainer *is* a web app. Lesson learned: not all tools are native applications.
- **"Package 'docker-ce' has no installation candidate"** – Classic Docker installation hiccup. Forgot to run `sudo apt update` after adding the Docker repository. *Now I never forget.*
- **"Can I just 'reset' Ubuntu instead of reinstalling?"** – I wanted a clean slate but didn't want to reinstall the OS. Learned that with CasaOS's uninstall script + manual cleanup, you can get a clean system without reinstalling.
- **The NAS mount saga** – Should I use NFS or CIFS? Ended up with NFS because it's faster and respects Linux permissions. This was my first real "technology choice" discussion.
- **Permissions hell** – Jellyfin couldn't see files on the NAS. Learned about UID/GID mapping (`PUID`, `PGID`), `chmod`, and the difference between the user *inside* a container and the user *on the host*.

## Why This Phase Mattered

This was the turning point. I stopped using "beginner tools" and started using the tools that professionals use. Every `docker-compose.yml` file I wrote was a step toward a new career.

## Result

A clean Ubuntu Server running Docker and Portainer, with AdGuard Home and Jellyfin managed via `docker-compose`. Ready for the next evolution: Proxmox.

→ Next: [Phase 4: Hardware Upgrade and Proxmox](04-hardware-upgrade-proxmox.md)
