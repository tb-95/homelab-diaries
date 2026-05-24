# Phase 6: AdGuard Home – DNS for the Whole Network

**Date:** Late May 2026

## What I Wanted to Achieve

I wanted my own DNS server that blocks ads, trackers, and malware for the entire home network. Not just a browser extension, but protection at the router level – for every device, including phones, TV, and guests.

## What We Did

1.  Created the first standalone LXC container in Proxmox (ID 100, `adguard`, IP `192.168.1.221`). This was the first real test of the new "1 service = 1 container" architecture.
2.  Installed Docker and ran AdGuard Home via `docker-compose`.
3.  Solved the port 53 conflict with `systemd-resolved` – on Ubuntu 26.04 you need to create a drop-in config in `/etc/systemd/resolved.conf.d/`.
4.  Set upstream DNS: `https://dns.quad9.net/dns-query` and `https://dns.cloudflare.com/dns-query`.
5.  Added 7 DNS blocklists for maximum protection.
6.  Increased DNS cache to 64 MB and enabled DNSSEC.
7.  Set AdGuard as the primary DNS server on the router for the whole network.

## Configuration (Highlights)

Full config in [`/configs/adguard/docker-compose.yml`](../configs/adguard/docker-compose.yml).

### docker-compose.yml
```yaml
services:
  adguardhome:
    image: adguard/adguardhome:latest
    container_name: adguardhome
    restart: unless-stopped
    ports:
      - "53:53/tcp"
      - "53:53/udp"
      - "3000:3000/tcp"
      - "3001:80/tcp"
    volumes:
      - /opt/docker/adguardhome/work:/opt/adguardhome/work
      - /opt/docker/adguardhome/conf:/opt/adguardhome/conf
```

## DNS Blocklists

- **HaGeZi's Pro++
- **OISD Big
- **AdGuard DNS filter
- **AdAway
- **Steven Black's
- **Peter Lowe's
- **NoCoin (cryptominer protection)

## Port 53 fix (Ubuntu 26.04)

```bash
sudo mkdir -p /etc/systemd/resolved.conf.d
echo -e "[Resolve]\nDNS=76.76.2.2\nDNSStubListener=no" | sudo tee /etc/systemd/resolved.conf.d/adguardhome.conf
sudo rm -f /etc/resolv.conf
echo "nameserver 76.76.2.2" | sudo tee /etc/resolv.conf
sudo systemctl reload-or-restart systemd-resolved
```

## AHA! Moments & My Thoughts

- **"What actually is DNS and why should I care?"** – DNS is the phonebook of the internet. When you type google.com, DNS translates it to an IP address. If you control that phonebook, you can cross out ad-serving numbers. That's why DNS filtering is so powerful – it blocks the ad before it even loads.
- **"Why not use Google DNS (8.8.8.8)?"** – Google collects data. Quad9 (9.9.9.9) and Cloudflare (1.1.1.1) promise not to sell your browsing history. Quad9 even actively blocks malware domains. If you're building your own cloud for privacy, you can't hand your DNS to Google.
- **"What's this port 53 thing, and why do I have to free it up?"** – Port 53 is the standard port for DNS. On Ubuntu, it's hogged by systemd-resolved – a local resolver. I had to "turn it off" so AdGuard could use that port. This was the first time I had to resolve a port conflict.
- **"Why map 3001:80 and not 80:80?"** – Port 80 is reserved for the future reverse proxy (NPM). AdGuard got port 3001 so it wouldn't clash. This was my first lesson in port planning – every service needs its own.
- **"76.76.2.2 as backup DNS?"** – If AdGuard goes down, I still want a DNS that blocks ads. Control D (76.76.2.2) does that – it's not just a "dumb" resolver, it has built-in filters. So protection continues even when the server is offline.
- **"How do I know it's working?"** – The best test: open an ad-heavy website and see empty boxes. And in the AdGuard dashboard, watch the blocked query counter climb. It's more satisfying than Netflix.

## Why This Service Matters

AdGuard Home is the heart of the entire network. Without it, there'd be no ad blocking, but more importantly, the beautiful domain names like jelly.fin wouldn't work – it's AdGuard that translates them into IP addresses. It's the invisible hero everyone relies on but nobody knows about.

## Result

The whole home network (including VPN clients) uses AdGuard as DNS. Ads and trackers are blocked on all devices. A 64 MB DNS cache speeds up repeated queries. DNSSEC protects against response tampering.

→ Next: [Phase 7: Nginx Proxy Manager – No More Ports in the Browser](07-nginx-proxy-manager.md)