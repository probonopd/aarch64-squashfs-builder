# aarch64-squashfs-builder [![Build Status](https://travis-ci.com/probonopd/aarch64-squashfs-builder.svg?branch=master)](https://travis-ci.com/probonopd/aarch64-squashfs-builder)

Trying to build squashfs images for aarch64 that can be booted on Amlogic devices using the mainline kernel.


https://forum.armbian.com/topic/12162-single-armbian-image-for-rk-aml-aw/ has https://yadi.sk/d/_rQgn_FosYuW0g/20.05.1/20200408 which can be booted on many Amlogic, Allwinner, and Rockchip aarch64 devices. I have verified it on a x96 2 GB box.

Edit `<BOOT partition>/uEnv.txt` like this:

```
FDT=/dtb/amlogic/meson-gxl-s905x-p212.dtb
APPEND= ... selinux=0
```

It is possible to convert such a system into a squashfs-based Live system by booting into it, and running

```
sudo apt-get update
sudo apt-get -y install casper

sudo nano /boot/uEnv.txt

# Edit like so:
APPEND=boot=casper union=overlay  ...
```

then making a squashfs out of the `<ROOTFS>` partition and putting it into `<ROOTFS>/live/filesystem.squashfs` using another computer. (The rest of `<ROOTFS>/`, except `boot` and `live`, may then be deleted.)

This repository is an attempt to build a suitable `filesystem.squashfs` file "bottom-up" using `debootstrap`.
