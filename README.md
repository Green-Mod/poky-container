# 🐳 Docker poky-container

## 🎉 Introduction

This repository is used to create a Docker image with all the necessary dependencies to run [Yocto Project][yocto]'s `bitbake` command. Indeed, to be able to build a Yocto image you need a **Linux** operating system. So, if you are using **Mac** or **Windows** without using a dedicated virtual machine, you can use this Docker container to build a Yocto image.

## 😉 Requirements

- [Docker][docker];
- About **50 GB of free disk space** is required to run the build properly.

## 💽 Create the volume

Before you can run the container you need to create a Docker volume where you can store the build result ([more info here][crops-volume-wiki]).

The commands to create a volume on **MacOS** are the following:

```bash
docker volume create --name yocto-volume
docker run -it --rm -v yocto-volume:/workdir busybox chown -R 1000:1000 /workdir
```

### Create and run a samba container

[More info here][crops-samba-wiki].

First let's create the Samba container, which will allow you to access the files in the volume:

```bash
docker create -t -p 445:445 --name samba -v yocto-volume:/workdir crops/samba
```

Then start it:

```bash
docker start samba
```

MacOS will not let you connect to a locally running Samba share. Therefore, create an alias for _127.0.0.1_ of _127.0.0.2_:

```bash
sudo ifconfig lo0 127.0.0.2 alias up
```

Since you will always need to have the alias to connect to the Samba container, you could also combine the start of Samba and alias like so:

```bash
docker start samba && sudo ifconfig lo0 127.0.0.2 alias up
```

## 🤟🏼 Starting the container

```bash
docker build -t [CONTAINER-NAME] .
docker run -it -v yocto-volume:/home/dev [CONTAINER-NAME]
```

Inside the container you can build a Yocto Project image ([more info here][yocto-project-quick-build]).

For example you can run the following commands:

```bash
git clone git://git.yoctoproject.org/poky
cd poky
git fetch --tags
git checkout tags/yocto-3.4 -b my-yocto-3.4

source oe-init-build-env
# Build a minimal image.
bitbake core-image-minimal
```

[yocto]: https://www.yoctoproject.org/
[docker]: https://www.docker.com/
[crops-volume-wiki]: https://github.com/crops/docker-win-mac-docs/wiki
[crops-samba-wiki]: https://github.com/crops/docker-win-mac-docs/wiki/Mac-Instructions
[yocto-project-quick-build]: https://www.yoctoproject.org/docs/current/brief-yoctoprojectqs/brief-yoctoprojectqs.html#brief-use-git-to-clone-poky
