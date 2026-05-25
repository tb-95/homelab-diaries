# Phase 9: Jellyfin – The Battle for Hardware Acceleration

**Date:** Late May 2026

## What I Wanted to Achieve

I wanted my own "Netflix" running in a dedicated LXC container. The key goal was to use hardware acceleration for smooth video playback and transcoding, and have it accessible via a beautiful domain name (`jelly.fin`).

## What We Did

1.  Created a dedicated LXC container (`media-server`, ID 102, IP `192.168.1.231`).
2.  Bound the media library (`/mnt/nas-library`) as read-only and prepared the download folder.
3.  Installed Jellyfin via Docker Compose.
4.  Fought a long battle to pass the Intel GPU (`/dev/dri/renderD128`) into the container for VA-API/QSV.
5.  Connected Jellyfin to the reverse proxy (NPM) and DNS (AdGuard Home) to work as `jelly.fin`.
6.  Debugged and resolved persistent subtitle issues that required multiple iterations.

## Configuration Highlights

**GPU Passthrough (Final working method in `102.conf`):**

dev0: /dev/dri/renderD128,mode=0666

**Jellyfin `docker-compose.yml`:**
```yaml
services:
  jellyfin:
    image: jellyfin/jellyfin:latest
    container_name: jellyfin
    restart: unless-stopped
    ports:
      - "8096:8096"
    volumes:
      - /opt/docker/jellyfin/config:/config
      - /mnt/nas-library/Movies:/media/movies:ro
      - /mnt/nas-library/Series:/media/series:ro
    devices:
      - /dev/dri/renderD128:/dev/dri/renderD128
    environment:
      - TZ=Europe/Prague
```

## The GPU Passthrough Saga

This was the hardest part. The Intel GPU had to be passed into an unprivileged LXC container, which is non-trivial. Here's a timeline of what we tried and learned:

- **Attempt 1:** Using the Proxmox GUI "Device Passthrough". Failed with error device does not exist (500).
- **Attempt 2:** Using a full bind mount of /dev/dri. The container failed to start with a lxc_init error.
- **Attempt 3:** Passing just the essential /dev/dri/renderD128 via a bind mount. The container started, but permissions inside were stuck as crw-rw----, preventing Jellyfin from using it.
- **Attempt 4:** Using dev0: /dev/dri/renderD128 (Device Passthrough) and then trying to fix permissions with chmod and a startup script. This failed because chmod does not propagate from the host into an unprivileged container for passed devices.
- **Final Solution:** Using Device Passthrough with the mode attribute: dev0: /dev/dri/renderD128,mode=0666. This single line in the LXC config finally set the correct permissions and Jellyfin recognized the GPU.

The missing /dev/dri/card0 file was a red herring. We eventually learned it's not needed for a headless server and that VA-API works perfectly with just renderD128.

## The Subtitle Saga

With the GPU working, a new problem appeared: enabling subtitles caused the video to go completely black. This was especially frustrating because it happened on the official Jellyfin client for Android TV, which is our primary viewing device.

### First Attempt: Disable Subtitle Extraction

The diagnostic showed that Jellyfin was trying to transcode the video to burn the .ASS subtitles into it, which caused the Intel QSV renderer to crash. The first fix was a simple checkbox in Dashboard → Playback → Transcoding:

- **Uncheck "Allow subtitle extraction on the fly"**

This solved the black screen problem – playback was immediately smooth again. But a new issue emerged: loading became painfully slow, and seeking within a video caused significant delays. The official Android TV client struggled to handle certain subtitle formats without server-side extraction.

### Final Solution: OpenSubtitles Integration

After researching the issue, I discovered that the official Jellyfin client for Android TV has limited native support for complex subtitle formats like .ASS. The solution was to download subtitles in a format the client can handle directly – namely .SRT – which avoids both the black screen problem and the slow loading.

Steps taken:

1.  Created an account at OpenSubtitles.com (free tier).
2.  Installed and configured the official OpenSubtitles plugin in Jellyfin (Dashboard      → Plugins → Catalog → Open Subtitles).
3.  Entered the OpenSubtitles credentials in the plugin settings.
4.  Set the plugin to download subtitles automatically for all new media.
5.  For existing media, manually triggered subtitle downloads via "Edit Subtitles → Search for Subtitles".

Now, when subtitles are downloaded through OpenSubtitles, the plugin prioritizes .SRT format, which the Android TV client handles natively. The result: no more black screens, and seeking/loading is fast and smooth.

## AHA! Moments & My Thoughts

- **"Why is this so hard? The GPU is right there."** – This was my first deep dive into the difference between privileged and unprivileged containers, cgroups, and device nodes. The mode=0666 trick felt like a magic spell after a dozen failed attempts. I learned that sometimes the solution is a single attribute you just didn't know existed.
- **"QSV or VA-API?"** – For my hardware, QSV is the primary choice, but I now understand when and why to switch to VA-API. The key is matching the configuration to the specific workload.
- **"The subtitle problem wasn't really about the GPU at all." – The black screen was a symptom, not the root cause. The real issue was that the Android TV client couldn't handle .ASS subtitles efficiently. Downloading .SRT subtitles via OpenSubtitles was the elegant workaround. I learned that sometimes you need to solve the problem at a different layer than where the symptom appears.
- **"I prefer official clients when possible."** – Rather than switching to a third-party player like Vimu or Nova, I found a way to make the official Jellyfin Android TV client work perfectly. This feels like a more sustainable solution for the long term.

## Why This Phase Matters

This wasn't just about installing a media server. It was a masterclass in Linux permissions, Proxmox container internals, methodical debugging, and understanding that sometimes the solution lies in choosing the right data format rather than fighting the software. The final result is a professional-grade, hardware-accelerated streaming service that the whole family can enjoy.

## Result

A fully functional Jellyfin server, accessible at http://jelly.fin, with working Intel QSV hardware acceleration and perfectly smooth subtitle support via OpenSubtitles integration. The media library is safely mounted as read-only from the NAS, and the reverse proxy handles everything seamlessly.

→ Next: Phase 10: The Great NAS Wake-Up and the Proxmox Storage Lesson