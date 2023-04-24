# General

A container which supports building Yocto images.

# Usage


This is still very early in development, but one can build an image like so...

```
user@host$ docker build ubuntu-20.04 -t yocto_ubuntu:4.1.3_20.04
user@host$ docker run --rm -it -v myvolume:/workdir ubuntu:20.04
root@ubuntu$ mkdir /workdir/poky
root@ubuntu$ chown 1000:1000 /workdir/poky
user@host$ docker run --rm -it -v myvolume:/workdir yocto_ubuntu:4.1.3_20.04
chef@yocto_ubuntu$ source /opt/poky/4.1.3/environment-setup-x86_64-pokysdk-linux
chef@yocto_ubuntu$ cd /workdir/poky
chef@yocto_ubuntu$ git clone git://git.yoctoproject.org/poky .
chef@yocto_ubuntu$ git checkout -t origin/langdale -b my-langdale
chef@yocto_ubuntu$ source oe-init-build-env
chef@yocto_ubuntu$ bitbake core-image-minimal
```

