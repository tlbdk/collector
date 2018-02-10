# Collector

Repo to gather information and tools on how to build a minimal Linux firmware

## Current Plan

* Custom kernel no modules
* Initramfs with custom init in go
  * Cross-compile tiny libc
  * Cross-compile pppd
  * Cross-compile go init
  * Cross-compile bsdiff
  * cpio and gzip

## Setup Raspberry Pi emulator on MacOS

### Installing from brew

``` bash
brew install qemu
```

### Compiling from git

Install libraries for compiling qemu: 

``` bash
brew install glib gmp mpfr pkgconfig ppl libmpc
```

Configure for x86 and arm 32bit and 64bit support:

``` bash
./configure \
  --enable-cocoa \
  --target-list=i386-softmmu,x86_64-softmmu,arm-softmmu,aarch64-softmmu \
  --disable-vnc \
  --disable-libssh2 \
  --enable-modules \
  --enable-tcg-interpreter \
  --enable-debug-tcg \
  --disable-strip \
  --python=/usr/local/bin/python2
```

Build and install:

``` bash
make -j4
make install
```

## Build kernel

``` bash
docker build -t kernelbuild:latest kernel/
```

or download pre-compiled version:

``` bash
wget https://github.com/dhruvvyas90/qemu-rpi-kernel/raw/master/kernel-qemu-4.4.34-jessie
```

## Run Raspbian

Download image and kernel for qemu:

``` bash
wget https://downloads.raspberrypi.org/raspbian_lite_latest
unzip raspbian_lite_latest 
```

Run image:

``` bash
qemu-system-arm -kernel zImage  \
-cpu arm1176 -m 256 \
-M versatilepb -no-reboot -serial stdio \
-append "root=/dev/sda2 panic=1 rootfstype=ext4 rw" \
-drive format=raw,file=2017-11-29-raspbian-stretch-lite.img \
-net user,hostfwd=tcp::5022-:22
```

## Links

### Build custom kernel

* Build qemu rpi kernel:  https://github.com/dhruvvyas90/qemu-rpi-kernel/tree/master/tools
* https://www.raspberrypi.org/documentation/linux/kernel/configuring.md
* https://github.com/kolargol/raspberry-minimal-kernel
* http://wiki.osdev.org/Raspberry_Pi_Bare_Bones

### Building custom root fs

* https://buildroot.org/
* https://github.com/gamaral/rpi-buildroot/blob/rpi/README.md
* https://ltekieli-com.cdn.ampproject.org/c/ltekieli.com/buildroot-with-raspberry-pi-what-where-and-how/amp/

### Testing on mac

* Run on MacOS: https://gist.github.com/hfreire/5846b7aa4ac9209699ba
* https://3mdeb.com/os-dev/emulate-rapberry-pi-2-in-qemu/#.Wjhdc1Q-cl4

### Building in docker

* Build kernel in docker: https://github.com/moul/docker-kernel-builder

### Boot speed up

* Custom go init: https://www.mustafaak.in/posts/2016-02-12-packaging-myinit-and-configs/
* Custom go init: http://u-root.tk/#setup
* http://www.zdnet.com/article/hands-on-with-picore-7-0-tiny-core-linux-for-the-raspberry-pi/