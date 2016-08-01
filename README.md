# Introduction

**NOTE:** Much of this documentation is mirrored directly from the [original source](http://www.udpcast.linux.lu/).

UDPcast is a file transfer tool that can send data simultaneously to many destinations on a LAN. This can for instance be used to install entire classrooms of PC's at once. The advantage of UDPcast over using other methods (nfs, ftp, whatever) is that UDPcast uses UDP's multicast abilities: it won't take longer to install 15 machines than it would to install just 2.

UDPcast is released under the GPL 2.0 license or any later version. Parts of the code (fec.c) falls under a BSD-like license. The new boot loader contains cardmgr, which falls under the mozilla public license. All other included third-party software (busybox, dialog, lzop, udhcp, ...) falls under GPL (to the best of my knowledge. If you notice software falling under a different license, especially one with a BSD-like publicity clause, please notify me).

UDPcast can be started from the included busybox boot image for OS installations, or from the command line when using it for other purposes. Udpcast may be booted from CD, floppy media, or via the network, by uding PXE or Etherboot. A configuration file (udpcfg.txt) may be included on these boot media in order to bypass the setup menu.

# Building
## Compiling udpcast itself (server version)

1. Untar the file:
`tar xzvf udpcast-20120424.tar.gz`
2. Cd into the source directory:
`cd udpcast`
3. Compile:
`make`
4. As root, install:
`make install`

This gives you a standalone `udp-sender` and `udp-receiver`, suitable for use on a filesserver. For the bootable version, see below.

## Compiling the menu system

The bootloader of udpcast version uses busybox. In order to save space, it should be compiled against uclibc rather than glibc. So, before compiling the menu system itself, you first need to set up uclibc'c buildroot environment.
### Building buildroot environment
Buildroot is an environment to compile programs with uclibc instead of glibc.

1. Download buildroot from [the official site](http://buildroot.uclibc.org/)
2. Unpack it
3. First run `make menuconfig`
4. Select the submenu `ToolChain`
5. Scroll down
6. Enable `large file (files > 2GB) support`
7. `make`
8. Put the resulting `output/host/usr/bin/i386-unknown-linux-uclibc-*` files in a directory which is in your $PATH (for example: `/usr/bin`)

### Compiling the boot dialog itself
In order to compile the menu system, first get the following software, and put them into one directory:

- udpcast-20120424.tar.gz udpcast itself
- udpbusybox-20120424.tar.gz the menu system (includes modified dialog library)
- busybox a utility which combines tiny versions of many common UNIX utilities into a single small executable.
- addBbApp.pl script to add a new application to busybox.
- busybox-config-maxi-1.20.0.txt configuration file for compiling "fullbox" format of busybox
- busybox-config-mini-1.20.0.txt configuration file for minimal format of busybox

To compile busybox with the udpcast menu system, download a virgin 1.20.0 busybox tar file (or later) from http://www.busybox.net/, and proceed as follows:

```bash
tar xfjv busybox-1.20.0.tar.bz2
cd busybox-1.20.0
tar xfzv ../udpcast-20120424.tar.gz
(cd udpcast-20120424  && ./configure)
tar xfzv ../udpbusybox-20120424.tar.gz
../addBbApp.pl udpcdialog udpcast-20120424
cp ../busybox-config-mini-1.20.0.txt .config
make
```

Copy `.config.maxi` to `.config` and compile again to get the "fullbox" version (for CD, USB and netboot images).

If you need to patch udpcast or udpcdialog with your own modifications, you may do so before make.
