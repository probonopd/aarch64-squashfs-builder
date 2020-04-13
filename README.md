# aarch64-squashfs-builder [![Build Status](https://travis-ci.com/probonopd/aarch64-squashfs-builder.svg?branch=master)](https://travis-ci.com/probonopd/aarch64-squashfs-builder)

Build squashfs images for aarch64 that can be booted on Amlogic (and potentially some Allwinner and Rockchip) devices using the mainline kernel.

## Turning normal Ubuntu installations into squashfs based Live systems

https://forum.armbian.com/topic/12162-single-armbian-image-for-rk-aml-aw/ has https://yadi.sk/d/_rQgn_FosYuW0g/20.05.1/20200408 which can be booted on many Amlogic, Allwinner, and Rockchip aarch64 devices. I have verified it on a x96 2 GB box.

Edit `<BOOT partition>/uEnv.txt` like this:

```
FDT=/dtb/amlogic/meson-gxl-s905x-p212.dtb
APPEND= ... selinux=0 apparmor=0
```

It is possible to convert such a system into a squashfs-based Live system by booting into it, and running

```
sudo apt-get update
sudo apt-get -y install casper

sudo nano /boot/uEnv.txt

# Edit like so:
APPEND=boot=casper ... selinux=0 apparmor=0
```

then making a squashfs out of the `<ROOTFS>` partition and putting it into `<ROOTFS>/live/filesystem.squashfs` using another computer. (The rest of `<ROOTFS>/`, except `boot` and `live`, may then be deleted.)

It should be possible to turn a Live system into a regular system by reversing the action. It is even possible to have both a regular system and a live system on the same partition, and switch between them by just switching the `APPEND=` lines.

## Creating the squashfs image from scratch

The Armbian project seems not to be open to integrate native support for TV boxes nor the mainline Amlogic kernel work nor the work done by Oleg Ivanov @150balbes. So it may be better to build images from Ubuntu/Debian packages "from the ground up".

This repository builds a suitable `filesystem.squashfs` file "bottom-up" using `debootstrap`.
Also see http://linux-sunxi.org/Debootstrap#First_stage.

## How to use

* For now, burn https://forum.armbian.com/topic/12162-single-armbian-image-for-rk-aml-aw/, expecially as of April 2020 https://yadi.sk/d/_rQgn_FosYuW0g/20.05.1/20200408 with the 5.6.2 mainline kernel to SD card
* Boot the system once, it will resize the ROOTFS partition.
* Delete the contents of the ROOTFS partition
* Create `/boot` and `/casper` on the ROOTFS partition (if you like you can also leave them there and switch into that system)
* Copy `filesystem.squashfs` to the `<ROOTFS partition>/casper/filesystem.squashfs`
* Copy `uImage` and `initramfs-*` to `<BOOT partition>`, replacing what was there before
* Edit `<BOOT partition>/uEnv.txt` so that it contains

```
FDT=/dtb/amlogic/meson-gxl-s905x-p212.dtb
APPEND=boot=casper union=overlay rw ... selinux=0
```

Use the correct dtb for your board.

## TODO

* Get Xorg to work (as it does in Armbian by Oleg Ivanov @150balbes)
* Check the customizations in https://github.com/drtyhlpr/rpi23-gen-image and https://github.com/150balbes/Build-Armbian/blob/master/lib/distributions.sh
* Check the issues tracker in this repository

## Acknowledgments

* Linux for Amlogic Meson http://linux-meson.com/doku.php (mainline kernel for Amlogic SOCs)
* Linux for ARM development boards https://www.armbian.com/ (non-profit initiative; naming somewhat misleading, it is not a Debian project)
* https://forum.armbian.com/topic/12162-single-armbian-image-for-rk-aml-aw/ by Oleg Ivanov @150balbes
