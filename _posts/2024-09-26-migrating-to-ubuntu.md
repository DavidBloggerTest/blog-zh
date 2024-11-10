---
layout: post
title: "Migrating to Ubuntu"
date: 2024-09-26 12:23 +0800
tags: [Diary,IT,OS]
comments: true
author: DavidX
---
## Ubuntu: Advantages and Drawbacks

### Advantages

Ubuntu Linux is an open-source and free operating system. It uses the Linux kernal which is stable and secure. It also gives a customizable interface. Many programming tools are based on Linux, so it is perfect for developers.

### Drawbacks

Linux\'s look may be quite different from Windows and unfamiliar for users. Also, some apps do not have Linux versions and many functions are limited.

## My Process

### Previous Attempts

On my previous DELL Latitude 7390 computer, I successfully installed Ubuntu (that was back in 2021).

Out of some unknown reason, I failed to install Ubuntu on my current computer, a Lenovo T430U. After searching for some tutorials online, I tried setting GRUB with $ nomodeset $ but it didn\'t work at all.

### Successful Attempt

Later, I tried resetting the BIOS and found that I can load the Ubuntu installer normally. Then, I installed Ubuntu successfully.

## Customization

### These customizations suit my tastes quite well but may not be suitable for everyone.

### Show icons in the upper left

Out of some reason, GNOME shows the desktop icons in the lower right corner. Open settings to change it to the upper left.

### Install VSCode and Edge

I personally prefer Edge over Firefox and installed Microsoft Edge. You can download Edge for Linux in the official website. VSCode is the best code editor for Linux and I installed it as well.

### Make icons on the dock smaller

I prefer smaller dock icons and set the size to 32.

### Install development tools

```bash
sudo apt install build-essential
```

### Uninstall Snap

Snap is an app manager for Ubuntu. However, I and many other people are not satisfied with it. Here\'s how to remove it:

Note: Remember to install Edge or Chrome before you uninstall snap because Firefox will be removed too.

#### 1. Disable automatic starting snap

```bash
sudo systemctl disable snapd.service
sudo systemctl disable snapd.socket
sudo systemctl disable snapd.seeded.service
```

#### 2. List snap packages

```bash
sudo snap list
```

#### 3. Remove Firefox and Snap Store

```bash
sudo snap remove --purge snap-store
sudo snap remove --purge firefox
```

#### 4. Remove other packages (this may change depending on yourself)

```bash
sudo snap remove --purge snapd-desktop-intergration gnome-42-2204 canonical-livepatch  gtk-common-themes 
```

#### 5. Remove Core

```bash
sudo snap remove core22 bare
```

#### 6. Remove Snap

```bash
sudo apt autoremove --purge snapd
sudo rm -rf /var/cache/snapd
sudo rm -rf ~/snap
sudo gedit /etc/apt/preferences.d/nosnap.pref
```

Enter the following:

```
Package: snapd
Pin: release a=*
Pin-Priority: -10
```

Save and close the editor. Then, restart the computer and Snap is gone for good.

### To Be Continued...

I\'m still exploring my new system, and will post more later.
