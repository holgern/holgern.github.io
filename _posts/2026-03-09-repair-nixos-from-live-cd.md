---
title: Repair Your Broken NixOS from the NixOS Live CD
date: 2026-03-09
---

I broke my NixOS installation while resizing my swap partition from the NixOS Live CD. After the resize, the system no longer booted because the swap UUID had changed. I could still enter the installed system with `nixos-enter`, but rebuilding from there did not solve the whole problem because the bootloader could not be installed.

What finally worked was using `nixos-install --flake` from the Live CD against my existing system. This post documents those recovery steps in case you run into the same issue.


<img src="/assets/images/repair-nixos-livecd.webp"
  alt="Illustration of repairing a broken NixOS system from a NixOS Live CD"
  width="420"
  loading="lazy"
  decoding="async"
  style="display: block; margin: 1.5rem auto; max-width: 100%; height: auto;">

## nixos-enter
What to do? I checked the wiki and found [`nixos-enter`](https://wiki.nixos.org/wiki/Change_root),
which can be used after booting into the NixOS Live CD.

```bash
mount /dev/disk/by-label/<ROOT_LABEL> /mnt/
nixos-enter

```
This worked, and I was able to start nvim and modify my Nix config.

Next, I tried to run `sudo nixos-rebuild switch --flake .#<hostname>`, but it
failed because it could not install the bootloader.

## nixos-install

After searching for other solutions, I finally found  [nixos-install](https://wiki.nixos.org/wiki/NixOS_Installation_Guide/en),
which is also used from the NixOS Live CD (I started it from a USB stick).
Most examples show how to use it without flakes:

```bash
mount /dev/disk/by-label/nixos /mnt
mkdir -p /mnt/boot
mount /dev/disk/by-label/boot /mnt/boot
nixos-generate-config --root /mnt
# Edit config
cd /mnt
nixos-install
```
At first, I did not realize that this was the solution to my problem. But then I
read how to use it with a flake-based config.

```bash
mount /dev/disk/by-label/nixos /mnt
mount /dev/disk/by-label/boot /mnt/boot
nixos-install --flake /mnt/path/to/flake#<hostname>
```
This almost worked. I needed to add options for pipe operators:

```bash
sudo nixos-install --flake /mnt/path/to/flake#<hostname> \
  --option extra-experimental-features 'nix-command flakes pipe-operators'
```

During the installation, I was asked to set a root password. Apart from that, it
worked, and I was finally able to boot into my Nix system.

I hope this helps. At least for me, the solution was not obvious. I almost gave
up and started a full reinstallation.
