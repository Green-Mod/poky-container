# 🐳 Docker poky-container

## 🎉 Introduction

This repo is to create an image that is able to run `bitbake/poky`. In fact, to be able to build a yocto image you need a linux operating system. If you are using Mac or Windows without using a dedicated virtual machine, you can use a docker container in which to execute the various commands.

## 😉 Requirements

- Docker.
- About 50 GB of free disk space is required to run the build properly.

## 💽 **Create the volume**

Before you can run the container you need to create a volume on which to save the build result. (For more detail visit [https://github.com/crops/docker-win-mac-docs/wiki](https://github.com/crops/docker-win-mac-docs/wiki)).

The commands for creating a volume on **MacOS** are described below.

In the terminal that appears type the following command to create a volume.

```bash
docker volume create --name yocto-volume
docker run -it --rm -v yocto-volume:/workdir busybox chown -R 1000:1000 /workdir
```

### **Create and run a samba container**

For more detail visit: [https://github.com/crops/docker-win-mac-docs/wiki/Mac-Instructions](https://github.com/crops/docker-win-mac-docs/wiki/Mac-Instructions)

- This container is what will allow you to see the files in the volume.

```bash
docker create -t -p 445:445 --name samba -v yocto-volume:/workdir crops/samba
```

- Start the samba container

```bash
docker start samba
```

- Create an alias for *127.0.0.1*

OSX will not let you connect to a locally running samba share. Therefore, create an alias for *127.0.0.1* of *127.0.0.2*.

```bash
sudo ifconfig lo0 127.0.0.2 alias up
```

Since you will always need to have the alias to connect to the samba container, you could also combine the start of samba and alias like so:

```bash
docker start samba && sudo ifconfig lo0 127.0.0.2 alias up
```

## 🤟🏼 Starting container

```bash
docker build -t [CONTAINER-NAME] .
docker run -it -v yocto-volume:/home/dev [CONTAINER-NAME]
```

Inside the container you can build a Yocto project. More detail at the following link:
[Yocto Project Quick Build](https://www.yoctoproject.org/docs/current/brief-yoctoprojectqs/brief-yoctoprojectqs.html#brief-use-git-to-clone-poky)

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
