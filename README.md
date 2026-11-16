# Linux on a MacBook Air M2 (2022) with Asahi Linux

Installing and running **Fedora Asahi Remix** natively on an Apple Silicon **MacBook Air M2 (13-inch, 2022)**, dual-booting alongside macOS.

Apple Silicon Macs do not use a standard PC boot process, so normal Linux installers do not work on them. The Asahi Linux project reverse-engineered the hardware and provides an installer that runs from inside macOS. This project documents how I installed it, what works, what doesn't, and what I learned.

---

## Contents

1. [Hardware](#hardware)
2. [Before installing](#before-installing)
3. [Installation](#installation)
4. [First boot and setup](#first-boot-and-setup)
5. [Switching between macOS and Linux](#switching-between-macos-and-linux)
6. [Hardware support on the M2 Air](#hardware-support-on-the-m2-air)
7. [Troubleshooting](#troubleshooting)
8. [My results](#my-results)
9. [What I learned](#what-i-learned)

---

## Hardware

| Component | Detail |
|---|---|
| Model | MacBook Air (13-inch, 2022) |
| Chip | Apple M2 |
| Architecture | ARM64 (aarch64), not x86 |
| OS installed | Fedora Asahi Remix (GNOME desktop) |
| Setup | Dual boot with macOS |

---

## Before installing

| Check | Why |
|---|---|
| **Back up the Mac** (Time Machine or external drive) | The installer resizes the macOS partition. It is designed not to erase macOS, but any disk change carries risk. |
| **Update macOS** to a supported version | Fedora Asahi Remix is installed *from* macOS and needs macOS 13.5 or 14.2 or later. |
| **Free up disk space** | The installer keeps around 38 GB free inside macOS so macOS can still update, so you need that **plus** the space you want for Linux. |
| **Use an admin account** | The installer needs your macOS administrator password. |
| **Plug in the charger** | Don't let the battery die mid-install. |
| **Check Time Machine local snapshots** | Snapshots can hold "free" space and cause resize errors. Delete old ones if the resize step fails. |

---

## Installation

The whole install is driven by one script, run from the macOS Terminal:

```bash
curl https://alx.sh | sh
```

The installer then walks through these steps:

1. **Enter the admin password.**
2. **Resize macOS.** Choose how much space to keep for macOS. The rest becomes free space for Linux. You can enter a size or a percentage.
3. **Choose what to install.** Select **Fedora Asahi Remix** and a desktop environment. I chose **GNOME** (KDE Plasma is the other option).
4. **Name the OS** as it will appear in the boot menu.
5. **Follow the final instructions exactly.** The installer finishes by asking you to fully shut down, wait, then **press and hold the power button** until "Loading startup options" appears. You then select the new Linux volume and complete a short one-time setup in recovery mode that allows the Mac to boot it.

> **Important:** read every message the installer prints, especially at the end. The final boot-setup step is the one most people get wrong.

---

## First boot and setup

On first boot, Fedora's setup wizard runs:

1. Choose language, keyboard layout and time zone.
2. Connect to Wi-Fi.
3. Create a user account.

Then update the system and install the basics:

```bash
# Update everything
sudo dnf upgrade --refresh

# Useful tools
sudo dnf install -y htop neofetch git

# Check the system: kernel, architecture, hardware
uname -a                 # kernel version (an Asahi kernel, aarch64)
lscpu | head -20         # CPU: Apple M2 performance and efficiency cores
free -h                  # memory
df -h                    # disk space on the Linux partition
```

**ARM64 note:** the M2 is an ARM chip, so software must be built for **aarch64**. Most apps in Fedora's repositories and Flathub work fine, but some x86-only apps (certain games or proprietary tools) will not run natively.

---

## Switching between macOS and Linux

Both systems stay installed:

- **Choose an OS at startup:** shut down, then press and hold the power button until the startup options appear, then pick macOS or Linux.
- **Change the default OS:** in macOS, go to *System Settings → General → Startup Disk*.

---

## Hardware support on the M2 Air

Based on the Asahi Linux project's published feature support table for the MacBook Air (13-inch, 2022):

| Feature | Status |
|---|---|
| Display, brightness | ✅ Supported |
| Keyboard, keyboard backlight, trackpad | ✅ Supported |
| Wi-Fi, Bluetooth | ✅ Supported |
| Speakers, headphone jack, microphones | ✅ Supported |
| Webcam | ✅ Supported |
| GPU acceleration | ✅ Supported |
| Battery info, suspend/sleep | ✅ Supported |
| USB-C (USB 2/3 data) | ✅ Supported |
| External display over USB-C | 🚧 Work in progress |
| Thunderbolt | 🚧 Work in progress |
| Touch ID | ❌ Not supported |

Source: [Asahi Linux M2 feature support](https://asahilinux.org/docs/platform/feature-support/m2/)

---

## Troubleshooting

| Problem | Cause | Fix |
|---|---|---|
| Installer says there isn't enough free space | It reserves around 38 GB for macOS updates | Free up more space in macOS (check Time Machine snapshots), then re-run |
| Error during the macOS resize step | Time Machine local snapshots or disk errors | Delete local snapshots, run First Aid in Disk Utility, try again |
| Linux doesn't appear at startup | The final boot-setup step wasn't completed | Hold the power button at startup, choose the Linux volume and complete the recovery prompts |
| An app won't install | It only exists for x86 | Look for an aarch64 build or a Flatpak, or use an alternative |
| External monitor doesn't work | Display over USB-C is still in development on M2 | Not available yet on this hardware |

---

## My results

- Fedora Asahi Remix with the **GNOME** desktop, installed natively on the M2 Air.
- Dual-booting with macOS, so both systems stay available from the startup menu.

---

## What I learned

- **Boot processes are platform-specific.** Apple Silicon uses its own secure boot chain, which is why Linux needs a special installer and a one-time recovery step instead of a USB stick.
- **Disk partitioning in practice:** shrinking an APFS container safely and keeping enough free space for the original OS.
- **CPU architecture matters.** ARM64 and x86 software are not interchangeable, so checking compatibility comes before installing anything.
- **Read the documentation first.** Checking the official hardware support table told me exactly what would and wouldn't work before I started.

---

*Author: [ArcSkt1](https://github.com/ArcSkt1) · CompTIA A+ · CompTIA Network+*
