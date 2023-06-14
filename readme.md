# General

Dockerfiles which provide containerized build hosts for the [Yocto Project](https://www.yoctoproject.org/).

| Branch | Workflow Status |
|------- | --------------- |
| master | [![Build images](https://github.com/lgrosz/my-yocto-dockerfiles/actions/workflows/docker-build.yml/badge.svg?branch=master)](https://github.com/lgrosz/my-yocto-dockerfiles/actions/workflows/docker-build.yml)    |
| zeus   | [![Build images](https://github.com/lgrosz/my-yocto-dockerfiles/actions/workflows/docker-build.yml/badge.svg?branch=zeus)](https://github.com/lgrosz/my-yocto-dockerfiles/actions/workflows/docker-build.yml)    |

# How is this different than crops?

[crops](https://github.com/crops) are the official Yocto Project containerized
build hosts. However, they only support the most recent Yocto Project releases.
Professionally, I work on older releases and the crops images do not (promise
to) work with older releases. This project aims to support older releases as
well as newer releases.

# Running tests

```
user@host$ docker build --target test <base-distro>
```

# Usage


One can build poky like so...

```
user@host$ docker build --target development -t yocto_ubuntu_dev:4.1.3_20.04 ubuntu-20.04
user@host$ docker run --rm -it -v myvolume:/home/chef yocto_ubuntu_dev:4.1.3_20.04 bash
chef@yocto_ubuntu_dev$ git clone git://git.yoctoproject.org/poky poky
chef@yocto_ubuntu_dev$ cd poky
chef@yocto_ubuntu_dev$ source oe-init-build-env
chef@yocto_ubuntu_dev$ bitbake core-image-minimal
```

`myvolume` will contain the build artifacts.

