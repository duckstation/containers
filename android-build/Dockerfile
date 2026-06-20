FROM ubuntu:22.04

# Set environment variables to avoid interactive prompts during installation
ENV DEBIAN_FRONTEND=noninteractive
ENV CMDLINETOOLS_VERSION=14742923
ENV NDK_VERSION=29.0.14206865
ENV ANDROID_HOME=/opt/android-sdk
ENV ANDROID_NDK_HOME=/opt/android-sdk/ndk/${NDK_VERSION}
ENV PATH=${PATH}:${ANDROID_HOME}/cmdline-tools/latest/bin:${ANDROID_HOME}/platform-tools:${ANDROID_NDK_HOME}

# Install system dependencies
RUN echo 'APT::Install-Suggests "0";' >> /etc/apt/apt.conf.d/00-docker && \
    echo 'APT::Install-Recommends "0";' >> /etc/apt/apt.conf.d/00-docker && \
    apt-get update && apt-get install -y \
    curl \
    wget \
    zip \
    unzip \
    openjdk-17-jdk-headless \
    git \
    build-essential \
    ninja-build \
    && rm -rf /var/lib/apt/lists/*

# Update cmake, 22.04 is too old.
COPY hashes.txt /tmp
RUN cd /tmp && \
    curl -LO https://github.com/Kitware/CMake/releases/download/v4.3.4/cmake-4.3.4-linux-x86_64.sh && \
    sha256sum -c hashes.txt && \
    chmod +x cmake-4.3.4-linux-x86_64.sh && \
    ./cmake-4.3.4-linux-x86_64.sh --prefix=/usr/local --skip-license && \
    rm cmake-4.3.4-linux-x86_64.sh

# Create directory for Android SDK
RUN mkdir -p ${ANDROID_HOME}

# Download and install Android Command Line Tools
RUN wget -q https://dl.google.com/android/repository/commandlinetools-linux-${CMDLINETOOLS_VERSION}_latest.zip -O /tmp/cmdline-tools.zip && \
    unzip -q /tmp/cmdline-tools.zip -d /tmp && \
    mkdir -p ${ANDROID_HOME}/cmdline-tools && \
    mv /tmp/cmdline-tools ${ANDROID_HOME}/cmdline-tools/latest && \
    rm /tmp/cmdline-tools.zip

# Accept Android SDK licenses
# Install Android SDK components and NDK
RUN yes | sdkmanager --licenses && \
    sdkmanager \
    "platform-tools" \
    "ndk;${NDK_VERSION}"

# Set up user
RUN useradd -ms /bin/bash build
USER build

# Set working directory
WORKDIR /workspace

# Default command
#CMD ["/bin/bash"]
