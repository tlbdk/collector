FROM ubuntu:artful

# Install build tools
RUN apt-get update               \
 && apt-get -y -q upgrade        \
 && apt-get -y -q install        \
    bc                           \
    binutils-arm-linux-gnueabihf \
    build-essential              \
    ccache                       \
    gcc-arm-linux-gnueabihf      \
    gccgo-arm-linux-gnueabihf    \
    gcc-aarch64-linux-gnu        \
    git                          \
    libncurses-dev               \
    libssl-dev                   \
    u-boot-tools                 \
    wget                         \
    xz-utils                     \
    device-tree-compiler         \
 && apt-get clean

RUN cd /usr/src && \
    git clone https://github.com/raspberrypi/linux.git

WORKDIR /usr/src/linux

RUN mkdir /out

# Cache the git fetch when we play with versions
RUN git fetch --tags
RUN git pull

# Linux 4.4.34
#ARG COMMIT=38f2800e6e94019a00a2880bfed8fba37aea2e5d
#ARG PATCH=linux-arm-4.4.x.patch

# Linux 4.4.x (50+)
#ARG COMMIT=rpi-4.4.y
#ARG PATCH=linux-arm-4.4.x.patch

# Linux 4.14.x - work in progress
ARG COMMIT=rpi-4.14.y
#ARG PATCH=linux-arm-4.14.x.patch

RUN git checkout ${COMMIT}

# Patch ARM support
#COPY ${PATCH} linux-arm.patch
#RUN patch -p1 < linux-arm.patch

# Build initial config
RUN ARCH=arm64 CROSS_COMPILE=aarch64-linux-gnu- make defconfig

# Merge qemu config to default
COPY qemu.config new.config
RUN ./scripts/kconfig/merge_config.sh -m .config new.config

# Build kernel
RUN make ARCH=arm64 CROSS_COMPILE=aarch64-linux-gnu- olddefconfig
RUN make -j 4 -k ARCH=arm64 CROSS_COMPILE=aarch64-linux-gnu-

WORKDIR /usr/src

RUN apt-get update && apt-get -y install cpio python unzip rsync

# Install Buildroot.
RUN wget -nv https://buildroot.org/downloads/buildroot-2017.11.2.tar.bz2 &&\
    tar xf buildroot-*.tar* &&\
    rm buildroot-*.tar* &&\
    ln -s buildroot-* buildroot &&\
    mkdir -v buildroot/patches

# Install BusyBox.
RUN wget -nv https://busybox.net/downloads/busybox-1.26.2.tar.bz2 &&\
    tar xf *.tar* &&\
    rm *.tar*

CMD ["/bin/bash"]