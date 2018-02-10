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

# Catch the git fetch when play with versions
RUN git fetch --tags

# Linux 4.4.34
#ARG COMMIT=38f2800e6e94019a00a2880bfed8fba37aea2e5d
#ARG PATCH=linux-arm-4.4.x.patch

# Linux 4.4.x (50+)
ARG COMMIT=rpi-4.4.y
ARG PATCH=linux-arm-4.4.x.patch

# Linux 4.14.x - work in progress
#ARG COMMIT=rpi-4.14.y
#ARG PATCH=linux-arm-4.14.x.patch

ARG TOOLCHAIN=arm-linux-gnueabihf

RUN git checkout ${COMMIT}

# Patch ARM support
COPY ${PATCH} linux-arm.patch
RUN patch -p1 < linux-arm.patch

# Build initial config
RUN make ARCH=arm versatile_defconfig

# Set toolchain
RUN echo CONFIG_CROSS_COMPILE="${TOOLCHAIN}" >> .config

# Add qemu configs
COPY qemu.config qemu.config
RUN cat qemu.config >> .config

# Build kernel
RUN make ARCH=arm CROSS_COMPILE=arm-linux-gnueabihf- oldconfig
RUN make -j 4 -k ARCH=arm CROSS_COMPILE=arm-linux-gnueabihf- bzImage

RUN cp arch/arm/boot/zImage /tmp