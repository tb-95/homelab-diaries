# Phase 8: The Great NAS Wake-Up and the Proxmox Storage Lesson

**Date:** Late May 2026

## What I Wanted to Achieve

I needed a reliable backup strategy for all my LXC containers. The logical place was my DS213j NAS, where I already had a `backup` folder. I thought adding it as a dedicated Proxmox storage would be the cleanest way.

## What We Did

1.  Added the NAS folder as a Proxmox directory storage with a simple command: `pvesm add dir NAS-Backup --path /mnt/proxmox/nas-data/backup --content backup`.
2.  Everything seemed fine until I noticed the NAS hard drives never spun down anymore. They were constantly active.
3.  Investigated the cause: Proxmox's `pvestatd` service checks all configured storages every few seconds. This permanent polling kept the NFS connection alive and prevented the drives from sleeping.
4.  First attempt to fix: Removed the storage via GUI. That didn't stop the disks because the NFS mount remained active.
5.  Manual unmounting: `umount /mnt/proxmox/nas-data/backup` and restarting services (`systemctl restart pvestatd pveproxy`). This only provided temporary relief.
6.  Final solution: Restored the Proxmox configuration from a backup taken *before* adding the storage. This fully reverted Proxmox to its clean state and permanently eliminated the unwanted polling.

## Configuration (Key Commands)

### The offending command
```bash
pvesm add dir NAS-Backup --path /mnt/proxmox/nas-data/backup --content backup
```

## Restoring the clean configuration

```bash
systemctl stop pvestatd pveproxy pvedaemon
tar -xzpf /mnt/proxmox/nas-data/backup/proxmox-config-20260524_203015.tar.gz -C /
reboot
```

## Verification

```bash
cat /etc/pve/storage.cfg   # No mention of NAS-Backup
```

## AHA! Moments & My Thoughts

- **"Why are the drives still spinning? I removed the storage in the GUI!"** – This was my first deep dive into how Proxmox manages storage. Removing an entry from the GUI deletes the configuration, but it doesn't automatically unmount the filesystem. The underlying NFS mount still existed, and pvestatd was still checking it. I learned that a clean removal sometimes requires manual intervention.
- **"Wait, pvestatd keeps the NAS awake?"** – The Proxmox status daemon is designed to monitor storage for capacity and health. For a production environment where you need immediate alerts about a failing disk, this makes perfect sense. For my homelab, where silence and low power are more important, it was a burden. I understood that a tool's default behavior isn't always aligned with my specific use case.
- **"Is my Proxmox config gone forever?"** – Before the pvesm command, we had created a manual backup of the entire /etc/pve directory with a simple tar script. This moment proved why that backup was worth its weight in gold. Being able to restore the entire Proxmox configuration to a known-good state without losing any containers was a lifesaver. It turned a potential disaster into a routine 5-minute fix.
- **"How will I do backups now without Proxmox storage?"** – The answer is simpler and better: I can still use the NAS for backups, but I won't define it as a permanent Proxmox storage. I'll just vzdump to the local disk and then cp the backup files to the NAS afterwards. This one-time copy doesn't keep the connection alive permanently. 
The NAS drives can finally sleep.

## Why This Matters

This incident was a perfect lesson in how each layer of a complex system (Proxmox, NFS, the NAS's power management) interacts. A seemingly innocent command can have consequences (constant drive activity) that only become visible hours later. Learning to systematically trace such side effects—and having a robust backup of your configuration—is what separates a tinkerer from a reliable administrator.

## Result

Proxmox is back to its clean state. The NAS drives now spin down after a period of inactivity. I have a clear path for performing backups without disturbing the quiet of my homelab. A valuable lesson was learned, and a solid backup habit was validated.

→ Next: [Phase 9: Jellyfin – The Battle for Hardware Acceleration](09-jellyfin.md)