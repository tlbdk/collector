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
docker run kernelbuild:latest cat /tmp/zImage > zImage
```

or download pre-compiled version:

``` bash
wget https://github.com/dhruvvyas90/qemu-rpi-kernel/raw/master/kernel-qemu-4.4.34-jessie
```

### Trimming the config

``` bash
diff -u -I '#.*' orig.config new.config
````

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
* https://blog.christophersmart.com/2016/10/27/building-and-booting-upstream-linux-and-u-boot-for-raspberry-pi-23-arm-boards/
* Network support: https://ownyourbits.com/2017/02/06/raspbian-on-qemu-with-network-access/

### Building custom root fs

* https://buildroot.org/
* http://www.kaizou.org/2016/09/boot-minimal-linux-qemu/
* https://github.com/gamaral/rpi-buildroot/blob/rpi/README.md
* https://ltekieli-com.cdn.ampproject.org/c/ltekieli.com/buildroot-with-raspberry-pi-what-where-and-how/amp/

### Testing on mac

* Run on MacOS: https://gist.github.com/hfreire/5846b7aa4ac9209699ba
* https://3mdeb.com/os-dev/emulate-rapberry-pi-2-in-qemu/#.Wjhdc1Q-cl4

### Building in docker

* Build kernel in docker: https://github.com/moul/docker-kernel-builder

### Boot speed up

* https://www.toradex.com/blog/embedded-linux-boot-time-optimization
* Custom go init: https://www.mustafaak.in/posts/2016-02-12-packaging-myinit-and-configs/
* Custom go init: http://u-root.tk/#setup
* http://www.zdnet.com/article/hands-on-with-picore-7-0-tiny-core-linux-for-the-raspberry-pi/

### Boot fallback

* https://github.com/raspberrypi/firmware/issues/593
* https://dius.com.au/2015/08/19/raspberry-pi-uboot/
* http://ltekieli.com/buildroot-with-raspberry-pi-u-boot/
* https://www.raspberrypi.org/documentation/hardware/raspberrypi/bootmodes/bootflow.md
* https://raspberrypi.stackexchange.com/questions/3796/custom-recovery-partition
* https://github.com/mkj/rpi-flipflop
* echo 5 > /sys/module/bcm2708/parameters/reboot_part // Set reboot partition
  * https://github.com/raspberrypi/linux/issues/2095

### Watchdog timer

* http://www.switchdoc.com/2014/11/reliable-projects-using-internal-watchdog-timer-raspberry-pi/
