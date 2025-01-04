FROM ubuntu:22.04

ARG CROSS_ARCH="arm64"
ARG CROSS_TRIPLET="aarch64-linux-gnu"

WORKDIR /tmp
COPY hashes.txt /tmp

# curl is needed for adding the LLVM repo.
# Add LLVM repository for clang.
# Install host packages.
# Install cross-compiler and qemu.
RUN apt-get update && \
    apt-get install -y curl gnupg && \
    curl -L https://apt.llvm.org/llvm-snapshot.gpg.key | apt-key add - && \
    echo 'deb http://apt.llvm.org/jammy/ llvm-toolchain-jammy-18 main' > /etc/apt/sources.list.d/llvm.list && \
    apt-get update && \
    apt-get install -y --no-install-recommends autoconf automake build-essential clang-18 curl extra-cmake-modules git libasound2-dev libcurl4-openssl-dev libdbus-1-dev libdecor-0-dev libegl-dev libevdev-dev libfontconfig-dev libfuse2 libgtk-3-dev libgudev-1.0-dev libharfbuzz-dev libinput-dev libopengl-dev libpipewire-0.3-dev libpulse-dev libssl-dev libudev-dev libwayland-dev libx11-dev libx11-xcb-dev libxcb1-dev libxcb-composite0-dev libxcb-cursor-dev libxcb-damage0-dev libxcb-glx0-dev libxcb-icccm4-dev libxcb-image0-dev libxcb-keysyms1-dev libxcb-present-dev libxcb-randr0-dev libxcb-render0-dev libxcb-render-util0-dev libxcb-shape0-dev libxcb-shm0-dev libxcb-sync-dev libxcb-util-dev libxcb-xfixes0-dev libxcb-xinput-dev libxcb-xkb-dev libxext-dev libxkbcommon-x11-dev libxrandr-dev libtool lld-18 llvm-18 ninja-build patchelf pkg-config unzip zlib1g-dev libegl1-mesa-dev libdrm-dev libgbm-dev appstream "g++-${CROSS_TRIPLET}" debootstrap binfmt-support qemu-user-static && \
    rm -rf /var/cache/apt/archives /var/lib/apt/lists/*

# Update cmake, 22.04 is too old.
RUN curl -LO https://github.com/Kitware/CMake/releases/download/v3.31.3/cmake-3.31.3-linux-x86_64.sh && \
    sha256sum -c hashes.txt && \
    chmod +x cmake-3.31.3-linux-x86_64.sh && \
    ./cmake-3.31.3-linux-x86_64.sh --prefix=/usr/local --skip-license && \
    rm cmake-3.31.3-linux-x86_64.sh

# Create chroot.
RUN debootstrap --arch="${CROSS_ARCH}" jammy "/$CROSS_ARCH-chroot" && \
    sed -i -e 's/main/main universe/' "/${CROSS_ARCH}-chroot/etc/apt/sources.list" && \
    chroot "/${CROSS_ARCH}-chroot" apt-get update -y && \
    chroot "/${CROSS_ARCH}-chroot" apt-get install -y --no-install-recommends autoconf automake build-essential cmake curl extra-cmake-modules git libasound2-dev libcurl4-openssl-dev libdbus-1-dev libdecor-0-dev libegl-dev libevdev-dev libfontconfig-dev libgtk-3-dev libgudev-1.0-dev libharfbuzz-dev libinput-dev libopengl-dev libpipewire-0.3-dev libpulse-dev libssl-dev libudev-dev libwayland-dev libx11-dev libx11-xcb-dev libxcb1-dev libxcb-composite0-dev libxcb-cursor-dev libxcb-damage0-dev libxcb-glx0-dev libxcb-icccm4-dev libxcb-image0-dev libxcb-keysyms1-dev libxcb-present-dev libxcb-randr0-dev libxcb-render0-dev libxcb-render-util0-dev libxcb-shape0-dev libxcb-shm0-dev libxcb-sync-dev libxcb-util-dev libxcb-xfixes0-dev libxcb-xinput-dev libxcb-xkb-dev libxext-dev libxkbcommon-x11-dev libxrandr-dev libtool ninja-build pkg-config unzip zlib1g-dev libegl1-mesa-dev libdrm-dev libgbm-dev && \
    rm -rf /${CROSS_ARCH}-chroot/var/cache/apt/archives /${CROSS_ARCH}-chroot/var/lib/apt/lists/*
