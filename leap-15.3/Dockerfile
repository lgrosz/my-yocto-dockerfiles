FROM opensuse/leap:15.3 as base

# [Required packages for build host](https://docs.yoctoproject.org/ref-manual/system-requirements.html#ubuntu-and-debian)
RUN zypper --non-interactive update && zypper --non-interactive install \
  Mesa-dri-devel \
  Mesa-libEGL1 \
  chrpath \
  diffstat \
  gcc \
  gcc-c++ \
  git \
  libSDL-devel \
  lz4 \
  make \
  makeinfo \
  patch \
  python \
  python-curses \
  python-xml \
  python3 \
  python3-Jinja2 \
  python3-curses \
  python3-pexpect \
  python3-pip \
  rpcgen \
  socat \
  tar \
  wget \
  which \
  xterm \
  xz \
  zstd \
  && zypper clean

RUN pip3 install GitPython

# Can't run bitbake as root
RUN useradd --user-group --create-home chef


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

RUN zypper --non-interactive update && zypper --non-interactive install \
  curl \
  gzip \
  && zypper clean

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
