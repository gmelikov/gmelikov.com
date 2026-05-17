---
title: "Debian Stretch diskless machine via PXE boot"
date: 2018-02-25
draft: false
categories:
  - legacy
tags:
  - linux
  - how-to
slug: debian-stretch-diskless-pxe-boot
build:
  list: false
---

Inspired by [this article](https://www.linuxquestions.org/questions/blog/isaackuo-112178/diskless-pxe-netboot-how-to-for-debian-8-jessie-37169/). Sometimes I use barebone machines to test or run something, and I already have a server machine, so it's better to use diskless PXE boot. Enjoy!

### Prerequisites

To run PXE we need:

- DHCP server to serve PXE boot record
- TFTP server with PXE boot kernel
- NFS server as shared file storage for client

You can have other storage options too.

I used router with dnsmasq and client with realtek ethernet adapter.

### Configure server

```bash
apt-get install tftpd-hpa syslinux pxelinux nfs-kernel-server

mkdir -p /srv/tftp /srv/nfs1
###cp -vax /srv/nfs1/boot/*.pxe /srv/tftp/ WE WILL DO THIS LATER
cp -vax /usr/lib/PXELINUX/pxelinux.o /srv/tftp/
cp -vax /usr/lib/syslinux/modules/bios/ldlinux.c32 /srv/tftp/
mkdir /srv/tftp/pxelinux.cfg
```

Create /srv/tftp/pxelinux.cfg/default file (if you have more than one diskless node — you may want to set different pxelinux.cfg configs instead of default one):

```
default Debian
prompt 1
timeout 3
label Debian
kernel vmlinuz.pxe
append rw initrd=initrd.pxe root=/dev/nfs ip=dhcp nfsroot=192.168.111.1:/srv/nfs1
```

Edit /etc/exports:

```
/srv/nfs1 192.168.111.0/24(rw,async,no_subtree_check,no_root_squash)
```

Reload services

```bash
systemctl restart tftpd-hpa
systemctl restart nfs-kernel-server
```

### Create system on NFS share

Here we can install fresh system, or copy already working one.

See [this article](https://www.linuxquestions.org/questions/blog/isaackuo-112178/diskless-pxe-netboot-how-to-for-debian-8-jessie-37169/) to copy system.

To install fresh one — you can use [debootstrap](https://www.linuxquestions.org/questions/debian-26/how-to-install-debian-using-debootstrap-4175465295/).

### Configure client to boot from NFS server

On server, edit /srv/nfs1/etc/fstab:

```
#192.168.111.1:/srv/nfs1 / nfs rw,noatime,nolock 1 1
/dev/nfs                 / nfs tcp,nolock 0 0
proc /proc    proc  defaults 0 0
none /tmp     tmpfs defaults 0 0
none /var/tmp tmpfs defaults 0 0
none /media   tmpfs defaults 0 0
none /var/log tmpfs defaults 0 0
```

Configure network, for example — edit /srv/nfs1/etc/network/interfaces:

```
iface eth0 inet dhcp
```

Add line to /srv/nfs1/etc/initramfs-tools/initramfs.conf:

```
BOOT=nfs
```

Start chroot:

```bash
mount --rbind /dev  /srv/nfs1/dev
mount --rbind /proc /srv/nfs1/proc/
mount --rbind /sys /srv/nfs1/sys/
chroot /srv/nfs1/  /bin/bash --login
```

#### Inside chroot

If your client ethernet adapter needs kernel module to work — add it to /etc/initramfs-tools/modules, example for realtek r8169:

```bash
echo "r8169" >> /etc/initramfs-tools/modules
```

Generate initramfs:

```bash
mkinitramfs -d /etc/initramfs-tools -o /boot/initrd.pxe
update-initramfs -u
cp -vax /boot/initrd.img[TAB] /boot/initrd.pxe
cp -vax boot/vmlinuz[TAB] boot/vmlinuz.pxe
```

#### On server

Copy INITRD.IMG and VMLINUZ to tftp:

```bash
cp -vax /srv/nfs1/boot/*.pxe /srv/tftp/
```

Congratulations, you can boot diskless machine!

There is one sidenote — you have to manually update boot kernel via

```bash
# 1) (on client) update client via it's package manager and get new kernel version
# 2) (on server) prepare and copy it into PXE boot dir
cp -vax /srv/nfs1/boot/initrd.img[TAB] /boot/initrd.pxe
cp -vax /srv/nfs1/boot/vmlinuz[TAB] /srv/nfs1/boot/vmlinuz.pxe
cp -vax /srv/nfs1/boot/*.pxe /srv/tftp/
```
