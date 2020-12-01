# ft_linux

## Description
First project of the Kernel branch ! The purpose is to create a simple LFS that will be used to create your own Linux distribution on which you will work for the next projects.

## Goal
The main goal of this project is to create your own very basic linux distribution. You will have chosen all the software you want and can now add whatever you want more for your own distribution. The will also be used in the KFS projects that come next as your base system.

This distribution will be yours and you will be able to select what is installed in what config and with which version.

## References
[The Holy Bible](http://www.linuxfromscratch.org/lfs/view/stable/index.html "Linux From Scratch")

## Tips & tricks for LFS
Checklist before starting LFS on a VM:
- VM has more than one CPU (_if possible_)
- VM has at least 4Gb of RAM
- 2 vdi image disks (8Gb for Host, 16Gb for LFS)
- VM has internet connection (**important**)
- You have _**SSH**_ connection to your VM (**important, will make life easier aftewards**)
- No need for a graphical interface on the host system
- You have access to root user (**important**)

Partition disk:
- Install parted
- `parted -a optimal /dev/<xxx>`
- `mklabel gpt`
- `unit mib`
- `mkpart primary 1 129`
- `name 1 boot`
- `mkpart primary 129 4225`
- `name 2 swap`
- `mkpart primary 4225 -1`
- `name 3 root`
- `set 1 boot on`

Give partitions a type:
- `mkfs.fat -F32 /dev/sdb1`
- `mkswap /dev/sdb2`
- `mkfs.ext4 /dev/sdb3`

Useful commands when rebooting during LFS:
- When rebooting after Chapter 2:
   - `mount -v -t ext4 /dev/sdb3 $LFS` (mount lfs partition)
   - `/sbin/swapon /dev/sdb2` (mount swap partition)
   - `mount -v -t vfat /dev/sdb1 $LFS/boot` (mount boot partition)
- When rebooting after Chapter 7:
   - `mount -v --bind /dev $LFS/dev`
   - These commands too:
```
mount -v --bind /dev/pts $LFS/dev/pts
mount -vt proc proc $LFS/proc
mount -vt sysfs sysfs $LFS/sys
mount -vt tmpfs tmpfs $LFS/run
```
   - To enter Chroot environment:
```
chroot "$LFS" /usr/bin/env -i   \
    HOME=/root                  \
    TERM="$TERM"                \
    PS1='(lfs chroot) \u:\w\$ ' \
    PATH=/bin:/usr/bin:/sbin:/usr/sbin \
    MAKEFLAGS='-j4' \
    /bin/bash --login +h
```
- When rebooting after chapter 8:
```
chroot "$LFS" /usr/bin/env -i          \
    HOME=/root TERM="$TERM"            \
    PS1='(lfs chroot) \u:\w\$ '        \
    PATH=/bin:/usr/bin:/sbin:/usr/sbin \
    MAKEFLAGS='-j4' \
    /bin/bash --login
```

!!! FAUT AJOUTER UNE PARTITION POUR GRUB ABRUTI !!!
