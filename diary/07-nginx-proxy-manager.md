# Phase 7: Nginx Proxy Manager – No More Ports in the Browser

**Date:** Late May 2026

## What I Wanted to Achieve

I was tired of typing IP addresses and port numbers. `http://192.168.1.231:8096` to watch a movie? My family would never use that. I wanted beautiful addresses like `http://jelly.fin`. To do that, I needed a **reverse proxy** – a "smart doorman" that looks at the name in the address and forwards the request to the right service.

## What We Did

1.  Created a new LXC container (ID 101, `proxy`, IP `192.168.1.222`) – separate from everything else.
2.  Installed Docker and prepared a `docker-compose.yml` for Nginx Proxy Manager (NPM). Research showed that the `latest` image had a bug in LXC, so we pinned version `2.11.2`.
3.  Used `network_mode: host` so NPM could see other containers on different IPs. (Docker inside LXC normally creates its own private network, which can't reach other LXC containers.)
4.  Logged in at `http://192.168.1.222:81` and changed the default password.
5.  Created the first Proxy Host: `jelly.fin` → `192.168.1.231:8096` (Jellyfin, ready for when it's built).
6.  Added a DNS rewrite in AdGuard Home: `jelly.fin` → `192.168.1.222` (NPM's IP), so the whole network can use the friendly name.

## Configuration (Highlights)

Full config in [`/configs/npm/docker-compose.yml`](../configs/npm/docker-compose.yml).

### docker-compose.yml
```yaml
services:
  npm:
    image: jc21/nginx-proxy-manager:2.11.2
    container_name: nginx-proxy-manager
    restart: unless-stopped
    network_mode: host
    volumes:
      - /opt/docker/npm/data:/data
      - /opt/docker/npm/letsencrypt:/etc/letsencrypt
    environment:
      - TZ=Europe/Prague
```

## Proxy Host for Jellyfin

- **Domain: jelly.fin
- **Forward Hostname / IP: 192.168.1.231
- **Forward Port: 8096
- **Websockets Support: ✓
- **Block Common Exploits: ✓

## AHA! Moments & My Thoughts

- **"Why can't I just use ports?"** – I could, but that's not how the internet works. Nobody types google.com:443. A reverse proxy lets me use standard ports (80, 443) and route by name. It feels professional.
- **"The latest image didn't work."** – NPM froze on "Setting ownership" when I first tried to start it. We had to search forums and find that version 2.11.2 was stable in LXC containers. Lesson: latest doesn't always mean "best for my setup". Sometimes a specific older version is safer.
- **"What is network_mode: host?"** – Docker normally isolates containers in their own virtual network. But my NPM needed to reach Jellyfin on a different IP (192.168.1.231). With host mode, NPM shares the LXC's network and can see the whole home network. It's not perfect, but it's simple and works.
- **"I chose jelly.fin instead of jellyfin.local."** – It's shorter, easier to remember, and I learned that in my own network I can invent any domain I want. The router's DNS (AdGuard) will resolve it. I'm the master of my own domain… literally.
- **"How does the magic work?"** – When I type jelly.fin in the browser, the query goes: My browser → AdGuard (resolves to 192.168.1.222) → NPM (sees the Host: jelly.fin header) → forwards to 192.168.1.231:8096. Three steps, but it feels like one.
- **"Why put NPM in its own LXC?"** – Because it's the entry point for all web traffic. If it goes down, I can't reach anything by name. Isolating it means a problem in another container (like a misconfigured Matrix server) won't take down access to Jellyfin or AdGuard.

## Why This Service Matters

Nginx Proxy Manager is the gateway. It makes my services accessible and user-friendly. Without it, I'd be stuck explaining port numbers to my family. With it, they just type jelly.fin and it works. That's the difference between a developer's toy and a family cloud.

## Result

Reverse proxy running. So far it only points to Jellyfin (which isn't even built yet!), but the infrastructure is ready. Adding new services will be a matter of creating another Proxy Host and DNS rewrite.

→ Next: [Phase 8: Jellyfin – Media Server in a Box](https://08-jellyfin.md)