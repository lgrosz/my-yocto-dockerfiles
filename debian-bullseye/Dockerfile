FROM debian:bullseye as base

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
  pylint3 \
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


FROM base as test
USER chef
WORKDIR /home/chef

# [Quick build](https://docs.yoctoproject.org/brief-yoctoprojectqs/index.html#use-git-to-clone-poky)
RUN \
  git clone git://git.yoctoproject.org/poky \
  && cd poky \
  && git checkout master \
  && . ./oe-init-build-env \
  && echo INHERIT += \"rm_work\" >> conf/local.conf \
  && bitbake quilt-native


FROM base as development

RUN apt update && apt install -y \
  curl \
  && apt clean && rm -rf /var/lib/apt/lists/*

# Setup [fixuid](https://github.com/boxboat/fixuid)
RUN USER=chef && \
    GROUP=chef && \
    curl -SsL https://github.com/boxboat/fixuid/releases/download/v0.5.1/fixuid-0.5.1-linux-amd64.tar.gz | tar -C /usr/local/bin -xzf - && \
    chown root:root /usr/local/bin/fixuid && \
    chmod 4755 /usr/local/bin/fixuid && \
    mkdir -p /etc/fixuid && \
    printf "user: $USER\ngroup: $GROUP\n" > /etc/fixuid/config.yml

USER chef:chef
WORKDIR /home/chef
ENTRYPOINT ["fixuid"]
