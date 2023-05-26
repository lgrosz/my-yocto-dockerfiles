FROM ubuntu:22.04 as base

ARG DEBIAN_FRONTEND=noninteractive

# [Required packages for build host](https://docs.yoctoproject.org/ref-manual/system-requirements.html#ubuntu-and-debian)
RUN apt update && apt install -y \
  build-essential \
  chrpath \
  cpio \
  debianutils \
  diffstat \
  file \
  gawk \
  gcc \
  git \
  iputils-ping \
  libegl1-mesa \
  liblz4-tool \
  libsdl1.2-dev \
  mesa-common-dev \
  pylint \
  python3 \
  python3-git \
  python3-jinja2 \
  python3-pexpect \
  python3-pip \
  python3-subunit \
  socat \
  texinfo \
  unzip \
  wget \
  xterm \
  xz-utils \
  zstd \
  && apt clean && rm -rf /var/lib/apt/lists/*

# Locale setup
RUN apt update && apt install -y \
  locales \
  && apt clean && rm -rf /var/lib/apt/lists/*
RUN sed -i '/en_US.UTF-8/s/^# //g' /etc/locale.gen \
  && locale-gen
ENV LANG en_US.UTF-8
ENV LANGUAGE en_US:en
ENV LC_ALL en_US.UTF-8

# Can't run bitbake as root
RUN useradd --create-home chef


from base as builder

USER chef
WORKDIR /home/chef

RUN \
  git clone git://git.yoctoproject.org/poky \
  && cd poky \
  && git checkout master \
  && . ./oe-init-build-env \
  && bitbake buildtools-tarball buildtools-extended-tarball buildtools-make-tarball


FROM ubuntu:22.04 as buildtools-amd64

COPY --from=builder /home/chef/poky/build/tmp/deploy/sdk/x86_64-buildtools-nativesdk-standalone-4.2.sh /
COPY --from=builder /home/chef/poky/build/tmp/deploy/sdk/x86_64-buildtools-extended-nativesdk-standalone-4.2.sh /
COPY --from=builder /home/chef/poky/build/tmp/deploy/sdk/x86_64-buildtools-make-nativesdk-standalone-4.2.sh /


FROM ubuntu:22.04 as buildtools-arm64

COPY --from=builder /home/chef/poky/build/tmp/deploy/sdk/aarch64-buildtools-nativesdk-standalone-4.2.sh /
COPY --from=builder /home/chef/poky/build/tmp/deploy/sdk/aarch64-buildtools-extended-nativesdk-standalone-4.2.sh /
COPY --from=builder /home/chef/poky/build/tmp/deploy/sdk/aarch64-buildtools-make-nativesdk-standalone-4.2.sh /


FROM buildtools-$TARGETARCH as buildtools

