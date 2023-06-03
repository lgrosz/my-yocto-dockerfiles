FROM centos:7 as base

# [Required packages for build host](https://docs.yoctoproject.org/3.0/mega-manual/mega-manual.html#ubuntu-packages)
RUN \
  yum update -y \
  && yum install -y epel-release \
  && yum makecache \
  && yum install -y \
    SDL-devel \
    bzip2 \
    chrpath \
    cpp \
    diffstat \
    diffutils \
    file \
    gawk \
    gcc \
    gcc-c++ \
    git \
    glibc-devel \
    gzip \
    make \
    patch \
    perl \
    perl-Data-Dumper \
    perl-Text-ParseWords \
    perl-Thread-Queue \
    python \
    python3 \
    python3-pip \
    socat \
    tar \
    texinfo \
    unzip \
    wget \
    which \
    xterm \
    xz \
  && yum clean all

RUN pip3 install GitPython jinja2

RUN localedef -c -f UTF-8 -i en_US en_US.UTF-8
ENV LC_ALL en_US.UTF-8

# Pull in build-tools
COPY --from=yocto_buildtools /x86_64-buildtools-nativesdk-standalone-3.0.4.sh /
RUN /x86_64-buildtools-nativesdk-standalone-3.0.4.sh -y

# Can't run bitbake as root
RUN useradd --create-home chef


FROM base as test
USER chef
WORKDIR /home/chef

# [Quick build](https://docs.yoctoproject.org/brief-yoctoprojectqs/index.html#use-git-to-clone-poky)
RUN \
  . /opt/poky/3.0.4/environment-setup-x86_64-pokysdk-linux \
  && git clone git://git.yoctoproject.org/poky \
  && cd poky \
  && git checkout zeus \
  && . ./oe-init-build-env \
  && echo INHERIT += \"rm_work\" >> conf/local.conf \
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
