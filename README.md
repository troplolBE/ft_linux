# ft_linux

## Description
First project of the Kernel branch ! The purpose is to create a simple LFS that will be used to create your own Linux distribution on which you will work for the next projects.

## Goal
The main goal of this project is to create your own very basic linux distribution. You will have chosen all the software you want and can now add whatever you want more for your own distribution. The will also be used in the KFS projects that come next as your base system.

This distribution will be yours and you will be able to select what is installed in what config and with which version.

## Questions

```json
[
   {
      "answers":[
         {
            "answer":null,
            "value":0
         }
      ],
      "guidelines":"\nTime to make your demo ! Present your distribution, and the\nsoftware on it. Bonus point for X Applications (Windows Manager,\nX Server)\n",
      "id":33498,
      "kind":"bonus",
      "name":"Bonus Binaries",
      "rating":"multi"
   },
   {
      "answers":[
         {
            "answer":null,
            "value":1
         }
      ],
      "guidelines":"\nTest the script ft_linux_basic.sh, available at the top of the\ncorrection page. If the score is not perfect, the scale stop\nhere.\n",
      "id":33493,
      "kind":"standard",
      "name":"Basic Linux tools",
      "rating":"bool"
   },
   {
      "answers":[
         {
            "answer":null,
            "value":1
         }
      ],
      "guidelines":"\nInternet working ? (Net module + ping / curl / ...)\n",
      "id":33496,
      "kind":"standard",
      "name":"Internet connection",
      "rating":"bool"
   },
   {
      "answers":[
         {
            "answer":null,
            "value":1
         }
      ],
      "guidelines":"\nNow the real test.\n\nAsk the student to install the screen package. The package can\nbe found\n[Here.](https://ftp.gnu.org/gnu/screen/screen-4.5.1.tar.gz)\n\nIf the student can't install the package the evaluation stop\nhere, grade 0.\n",
      "id":33497,
      "kind":"standard",
      "name":"screen",
      "rating":"bool"
   },
   {
      "answers":[
         {
            "answer":null,
            "value":1
         }
      ],
      "guidelines":"\nIs the kernel a Linux one ? (If the kernel is a BSD, the scale\nstops here, if it's a DOS one, burn the student alive, kill his\nentire family, and grade -42.)\n",
      "id":33487,
      "kind":"standard",
      "name":"Linux Kernel",
      "rating":"bool"
   },
   {
      "answers":[
         {
            "answer":null,
            "value":1
         }
      ],
      "guidelines":"\nIs the linux version a 4.x ? Are the sources in\n/usr/src/linux-4.x ?\n\nCheck the kernel version in the logs. Must be something like\nlinux version-<student_login>\n",
      "id":33488,
      "kind":"standard",
      "name":"Linux Version",
      "rating":"bool"
   },
   {
      "answers":[
         {
            "answer":null,
            "value":4
         }
      ],
      "guidelines":"\nTest the following script ft_linux_others.sh, available at the\ntop of the correction page. Report the score below\n",
      "id":33494,
      "kind":"standard",
      "name":"Other binaries",
      "rating":"multi"
   },
   {
      "answers":[
         {
            "answer":null,
            "value":1
         }
      ],
      "guidelines":"\nIs there at least 3 partitions on the system ? (root, /boot,\nswap)\n",
      "id":33489,
      "kind":"standard",
      "name":"Partitions",
      "rating":"bool"
   },
   {
      "answers":[
         {
            "answer":null,
            "value":1
         }
      ],
      "guidelines":"\nTest if an editor is present on the system. (More points if it's\nvim :D)\n",
      "id":33495,
      "kind":"standard",
      "name":"Editor",
      "rating":"bool"
   },
   {
      "answers":[
         {
            "answer":null,
            "value":1
         }
      ],
      "guidelines":"\nIs there a module loader on the system ? (udev like)\n",
      "id":33490,
      "kind":"standard",
      "name":"Kernel Module loader",
      "rating":"bool"
   },
   {
      "answers":[
         {
            "answer":null,
            "value":1
         }
      ],
      "guidelines":"\nIs there a bootloader ? (LILO / GRUB / ...)\n\nCheck the kernel binary name in /boot. Must be something like\nvmlinuz-<linux_version>-<student_login>\n",
      "id":33491,
      "kind":"standard",
      "name":"Bootloader",
      "rating":"bool"
   },
   {
      "answers":[
         {
            "answer":null,
            "value":1
         }
      ],
      "guidelines":"\nIs there a daemon manager ? (SysV, SystemD, ...)\n",
      "id":33492,
      "kind":"standard",
      "name":"Daemon manager",
      "rating":"bool"
   }
]
```

## References
[The Holy Bible](http://www.linuxfromscratch.org/lfs/view/stable/index.html "Linux From Scratch")

[ft_linux_basic.sh](https://intra.42.fr/uploads/document/document/425/ft_linux_basic.sh)

[ft_linux_others.sh](https://intra.42.fr/uploads/document/document/426/ft_linux_others.sh)

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

if [ -h $LFS/dev/shm ]; then
  mkdir -pv $LFS/$(readlink $LFS/dev/shm)
fi
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
