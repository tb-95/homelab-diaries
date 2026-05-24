# Phase 5: WireGuard – VPN at the Router Level

**Date:** Early May 2026

## What I Wanted to Achieve

My server was running on the home network, but I wanted access from outside. Not through port forwarding (that's dangerous), but through a VPN. And I didn't want to set up a VPN on every device – I wanted one central point, ideally directly on the router.

## What We Did

1.  Router **Mercusys MR85X** supports a WireGuard server. I enabled it directly in the router's admin panel.
2.  Set VPN clients to have access to the entire home network (option **"Internet and Home Network"**).
3.  Set my AdGuard Home (`192.168.1.221`) as the DNS server for VPN clients – so ad blocking works even remotely.
4.  Added **Control D** (`76.76.2.2`) as backup DNS – it also blocks ads and malware, so protection continues even if AdGuard goes down.
5.  Added a **switch** to the network and connected all devices (server, NAS, router) to it. Now data flows directly between devices without burdening the router.

## AHA! Moments & My Thoughts

- **"Why should I write the server's IP in the WireGuard config, not the container's IP?"** – WireGuard clients see my entire home network, so they use the normal address `192.168.1.221`, not some internal Docker IP. I understood the difference between Docker's network and my home network.
- **"What if the power goes out and the server doesn't boot?"** – That's why I have the backup DNS `76.76.2.2` set in the router. If AdGuard goes down, the internet still works. And when the server comes back, I just toggle the VPN off and on.
- **"76.76.2.2 blocks ads too?"** – Yes! I learned that Control D isn't just a plain DNS resolver – it has built-in filters against ads, malware, and tracking. It's a much better backup DNS than Google's `8.8.8.8`.
- **The switch is a game-changer** – Plugging everything into the switch eliminated network bottlenecks and instability. Data flows directly between devices. The router only handles internet and VPN, not local traffic.
- **"What if a family member can't use a VPN?"** – For regular users, WireGuard is simple – just scan a QR code into the mobile app. No passwords, no configuration.

## Why This Matters

This isn't just a technical decision. It's a philosophy: **I want access from anywhere, but not at the cost of security**. WireGuard on the router gives me both – an encrypted tunnel into my home network without opening ports to the internet.

## Result

The whole family has secure access to the home server from anywhere. Ads are blocked even on the go. And I have peace of mind knowing the server isn't exposed to the internet.

→ Next: [Phase 6: AdGuard Home – DNS for the Whole Network](06-adguard-home.md)