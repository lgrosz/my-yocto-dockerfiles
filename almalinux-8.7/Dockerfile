ARG TARGETARCH


FROM almalinux:8.7 as base

# [Required packages for build host](https://docs.yoctoproject.org/ref-manual/system-requirements.html#ubuntu-and-debian)
RUN dnf -y update \
  && dnf -y install epel-release \
  && dnf config-manager --set-enabled powertools \
  && dnf makecache \
  && dnf -y install \
    SDL-devel \
    bzip2 \
    ccache \
    chrpath \
    cpp \
    diffstat \
    diffutils \
    gawk \
    gcc \
    gcc-c++ \
    git \
    glibc-devel \
    gzip \
    lz4 \
    make \
    mesa-libGL-devel \
    patch \
    perl \
    perl-Data-Dumper \
    perl-Text-ParseWords \
    perl-Thread-Queue \
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


FROM base as base-amd64

COPY --from=yocto_buildtools /x86_64-buildtools-extended-nativesdk-standalone-4.2.sh /
RUN /x86_64-buildtools-extended-nativesdk-standalone-4.2.sh -y


FROM base as base-arm64

COPY --from=yocto_buildtools /aarch64-buildtools-extended-nativesdk-standalone-4.2.sh /
RUN /aarch64-buildtools-extended-nativesdk-standalone-4.2.sh -y

# Can't run bitbake as root
RUN useradd --create-home chef


FROM base-$TARGETARCH as test
USER chef
WORKDIR /home/chef

# [Quick build](https://docs.yoctoproject.org/brief-yoctoprojectqs/index.html#use-git-to-clone-poky)
RUN \
  . /opt/poky/4.2/environment-setup-x86_64-pokysdk-linux \
  && git clone git://git.yoctoproject.org/poky \
  && cd poky \
  && git checkout master \
  && . ./oe-init-build-env \
  && bitbake quilt-native


FROM base-$TARGETARCH as development

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
