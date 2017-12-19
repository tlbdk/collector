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

RUN git fetch --tags && git checkout rpi-4.9.y

COPY arm.config .config

RUN make ARCH=arm CROSS_COMPILE=arm-linux-gnueabihf- oldconfig
RUN make -j 4 -k ARCH=arm CROSS_COMPILE=arm-linux-gnueabihf- bzImage

RUN cp arch/arm/boot/zImage /tmp