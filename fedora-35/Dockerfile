FROM fedora:35 as base

# [Required packages for build host](https://docs.yoctoproject.org/ref-manual/system-requirements.html#ubuntu-and-debian)
RUN dnf -y update && dnf -y install \
  SDL-devel \
  bzip2 \
  ccache \
  chrpath \
  cpio \
  cpp \
  diffstat \
  diffutils \
  file \
  findutils \
  gawk \
  gcc \
  gcc-c++ \
  git \
  glibc-devel \
  gzip \
  hostname \
  lz4 \
  make \
  mesa-libGL-devel \
  patch \
  perl \
  perl-Data-Dumper \
  perl-File-Compare \
  perl-File-Copy \
  perl-FindBin \
  perl-Text-ParseWords \
  perl-Thread-Queue \
  perl-bignum \
  perl-locale \
  python \
  python3 \
  python3-GitPython \
  python3-jinja2 \
  python3-pexpect \
  python3-pip \
  rpcgen \
  socat \
  tar \
  texinfo \
  unzip \
  wget \
  which \
  xterm \
  xz \
  zstd \
  && dnf clean all

# Locale setup
RUN dnf -y update && dnf -y install \
  glibc-langpack-en \
  && dnf clean all
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
  && bitbake quilt-native


FROM base as development

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
