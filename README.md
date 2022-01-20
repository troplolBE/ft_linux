# ft_linux (LFS 11-Systemd)

## Description
First project of the Kernel branch ! The purpose is to create a simple LFS that will be used to create your own Linux distribution on which you will work for the next projects.

## Goal
The main goal of this project is to create your own very basic linux distribution. You will have chosen all the software you want and can now add whatever you want more for your own distribution. The will also be used in the KFS projects that come next as your base system.

This distribution will be yours and you will be able to select what is installed in what config and with which version.

## References
[The Holy Bible](http://www.linuxfromscratch.org/lfs/view/stable/index.html "Linux From Scratch")

[ft_linux_basic.sh](https://intra.42.fr/uploads/document/document/426/ft_linux_basic.sh)

[ft_linux_others.sh](https://intra.42.fr/uploads/document/document/425/ft_linux_others.sh)

## Tips & tricks for LFS
Checklist before starting LFS on a VM:
- VM has more than one CPU (_if possible_)
- VM has at least 4Gb of RAM
- 2 vdi image disks (8Gb for Host, 16Gb for LFS)
- VM has internet connection (**important**)
- You have _**SSH**_ connection to your VM (**important, will make life easier aftewards**)
- No need for a graphical interface on the host system
- You have access to root user (**important**)

## Important commands to succeed

### Partition disk
See file [commands.txt](commands.txt) for more explainations

first: `fdisk /dev/sdb`

then: (each line is divided by command and then the input for the questions)
```shell
g
n default default +1M
t 1 4
n default default +200M
t 2 1
n default default +4G
t 3 swap
n default default default
w
```

### Create filesystem

```shell
mkswap /dev/sdb3
mkfs -v -t ext4 /dev/sdb4
mkfs -v -t ext2 /dev/sdb2
```

### LFS variable

```shell
export LFS=/mnt/lfs
```

Add this to root and user's .bashrc to make sure variable is always there.

### Mount partitions

First time mounting partitions (and creating the directories)
```shell
mkdir -pv $LFS
mount -v -t ext4 /dev/sdb4 $LFS
mkdir -pv $LFS/boot
mount -v -t ext2 /dev/sdb2 $LFS/boot
/sbin/swapon -v /dev/sdb3
```

If not the first time you only need to mount the partitions and the swap
```shell
mount -v -t ext4 /dev/sdb4 $LFS
mount -v -t ext2 /dev/sdb2 $LFS/boot
/sbin/swapon -v /dev/sdb3
```

Starting from chapter 7.3.3 you also need to mount the following files :
```shell
mount -v --bind /dev $LFS/dev
mount -v --bind /dev/pts $LFS/dev/pts
mount -vt proc proc $LFS/proc
mount -vt sysfs sysfs $LFS/sys
mount -vt tmpfs tmpfs $LFS/run
if [ -h $LFS/dev/shm ]; then
  mkdir -pv $LFS/$(readlink $LFS/dev/shm)
fi
```

### Entering chroot

For chapter 7 and 8 enter the chroot environment using this command :
```shell
chroot "$LFS" /usr/bin/env -i   \
    HOME=/root                  \
    TERM="$TERM"                \
    PS1='(lfs chroot) \u:\w\$ ' \
    PATH=/usr/bin:/usr/sbin     \
    MAKEFLAGS=-j6               \
    /bin/bash --login +h
```

From chapter 8.79 use :
```shell
chroot "$LFS" /usr/bin/env -i          \
    HOME=/root TERM="$TERM"            \
    PS1='(lfs chroot) \u:\w\$ '        \
    PATH=/usr/bin:/usr/sbin            \
    MAKEFLAGS=-j6                      \
    /bin/bash --login
```

### Configuring the system

/etc/fstab file
```
/dev/sda4      /            ext4     defaults            1     1
/dev/sda2      /boot        ext2     defaults            1     1
/dev/sda3      swap         swap     pri=1               0     0
```

### Configuration du kernel

To configure the kernel right you have to enable and disable all the asked options. The option to add your login to the kernel name is `local version` and you nee to put `-login` and replace login with your login. Do not remove the dash or it will not render properly.

You should also run the `make defconfig` before you run the `make menuconfig` command. This will do most of the work for you.

When copying the files to the boot directory, you need to modif the command for the vmlinuz to use your login. It should look like this :
```shell
cp -iv arch/x86/boot/bzImage /boot/vmlinuz-5.13.12-login
```

again replace above login with your login.

### Configuring grub

Install grub on the right disk :
```shell
grub-install /dev/sdb
```

To configure grub to boot the right parition and the right file. Put this in the /boot/grub/grub.cfg file :
```grub
# Begin /boot/grub/grub.cfg
set default=0
set timeout=5

insmod ext2
set root=(hd0,2)

menuentry "GNU/Linux, Linux 5.13.12-yourlogin" {
        linux   /vmlinuz-5.13.12-yourlogin root=/dev/sda4 ro
}
```

Again, replace yourlogin with you login. It is crucial here that for the line starting with 'linux' you use the right name of the vmlinuz file. If this is not right your lfs will not boot.


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
