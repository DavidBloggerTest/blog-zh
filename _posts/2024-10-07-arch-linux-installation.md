---
layout: post
title: "Arch Linux Installation"
date: 2024-10-07 21:07 +0800
tags: [Guide,IT]
comments: true
author: DavidX
---
## Why Arch

Arch Linux is flexible, lightweight and powerful Linux distro. It allows the user to determine almost everything -- the desktop, the kernel, the shell....

However, Arch Linux is not suitable for beginners. It is not so easy to install (but its better now). It is one of the best Linux distros for professional users.

Note that an automated installation script is now available in the install media. However, I\'ll still show the process step-by-step.

## About This Guide

I myself successfully installed Arch Linux a few days ago. It wasn\'t easy but you\'ll certainly succeed if you closely follow the instructions.

## Step 0. Burn an USB

Download an Arch Linux image [here](https://geo.mirror.pkgbuild.com/iso/latest/archlinux-x86_64.iso). (It is always the latest.)

The image is quite small, about 1.1 GB in size.

Use Balena Etcher to burn the image to an USB. Note that only 2GB is required because Arch Linux installer has so few built-in packages.

Next, boot into the live installation media.

Wait a few seconds and you shall see the root user logged in.

## Step 1. Pre-check

```bash
systemctl stop reflector.service
ls /sys/firmware/efi/efivars
# if it outputs several lines of text than it's OK
# otherwise, change to UEFI boot mode
```

## Step 2. Connect to the Internet

A good Internet connection is required for installation. All the packages in your new system will be fetched online.

If you\re using WiFi, follow these steps to connect:

```bash
iwctl # enter iwd
device list # record the name of your adaptor
station wlan0 scan # nothing will happen. don't worry
station wlan0 get-networks # show networks
station wlan0 connect [SSID] # connect to a WiFi
exit #exit iwd
ping archlinux.org # test connectivity (press Ctrl+C to end)
```

If you see no errors than congratulations!

## Step 3. Configure time and mirrors

```bash
timedatectl set-ntp true # sync time
timedatectl status # check sync status
vim /etc/pacman.d/mirrorlist # edit mirrors
```

Now, put this at the top of the file (scroll down to see a mirror that you want to use, and copy it to the top):

```bash
Server = https://[Your Mirror Site]/archlinux/$repo/os/$arch
```

## Step 4. Disk partition

```bash
lsblk
```

Note the name of your disk device as DISK.

You should use `cfdisk /dev/DISK` to partition your disk.

It is quite intuitive.

Normally, you should make 3 partitions:

| size         | type             | description    |
| ------------ | ---------------- | -------------- |
| 1G           | EFI system       | EFI bootloader |
| ~10G         | Linux Swap       | virtual memory |
| all the rest | Linux filesystem | your system    |

Remember to write before you leave. Note down the device labels of the three partitions.

```bash
fdisk -l # double check for errors
mkfs.fat -F32 /dev/EFI_PARTITION
mkswap /dev/SWAP_PARTITION
mkfs.btrfs -L YOUR_LABEL /dev/MAIN_PARTITION\
mount -t btrfs -o compress=zstd /dev/SWAP_PARTITION /mnt
# subvolumes
btrfs subvolume create /mnt/@
btrfs subvolume create /mnt/@home
btrfs subvolume list -p /mnt # double check
umount /mnt
```

## Step 5. Install new system

```bash
# mount partitions
mount -t btrfs -o subvol=/@,compress=zstd /dev/sdxn /mnt
mkdir /mnt/home
mount -t btrfs -o subvol=/@home,compress=zstd /dev/sdxn /mnt/home
mkdir -p /mnt/boot
mount /dev/sdxn /mnt/boot
swapon /dev/sdxn
# install packages, wait a minute...
pacstrap /mnt networkmanager vim sudo bash bash-completion
genfstab -U /mnt > /mnt/etc/fstab
cat /mnt/etc/fstab
# change root to new system
arch-chroot /mnt
vim /etc/hostname
# enter your computer name here, like ArchLinux
vim /etc/hosts
# 127.0.0.1   localhost
# ::1         localhost
# 127.0.1.1   YOUR_COMPUTER_NAME.localdomain YOUR_COMPUTER_NAME
ln -sf /usr/share/zoneinfo/CONTINENT/CITY /etc/localtime
hwclock --systohc
vim /etc/locale.gen # uncomment your own locale **_**.UTF_8 UTF_8
# uncomment en_US.UTF_8 UTF_8 as well
locale-gen
echo 'LANG=en_US.UTF-8'  > /etc/locale.conf # suggested
passwd root # set a password
pacman -S intel-ucode # Intel
pacman -S amd-ucode # AMD
pacman -S grub efibootmgr os-prober
grub-install --target=x86_64-efi --efi-directory=/boot --bootloader-id=ARCH # install GRUB bootloader
vim /etc/default/grub
# add GRUB_DISABLE_OS_PROBER=false
exit
umount -R /mnt
reboot
```
Pull off the USB and wait.
If all goes well you\'ll log into the new system with root.

## Step 6. Install GNOME

```bash
systemctl enable --now NetworkManager
nmcli dev wifi list # list wifis
nmcli dev wifi connect SSID password PASSWORD # connect to wifi
ping archlinux.org
sudo pacman -S --needed xorg gnome gnome-tweaks nautilus-sendto gnome-nettool gnome-usage gnome gnome-multi-writer adwaita-icon-theme xdg-user-dirs-gtk fwupd arc-gtk-theme seahosrse gdm archlinux-wallpaper fastfetch
systemctl enable gdm
fastfetch # check your new system out!
reboot
```

## Step 7. Add user

Using root is dangerous. It\'s suggested that you create your own user.
```bash
useradd -m -s /bin/bash USERNAME
passwd USERNAME
sudo usermod -aG wheel USERNAME
```
All set and done! Reboot and you\'ll have a fully-fledged Arch Linux system.