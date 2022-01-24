docker run -t domenicogaeni/yocto .

docker volume create --name yocto-volume
docker run -it --rm -v yocto-volume:/workdir busybox chown -R 1000:1000 /workdir

docker start samba && sudo ifconfig lo0 127.0.0.2 alias up

docker run -it -v yocto-volume:/home/dev domenicogaeni/yocto

git clone git://git.yoctoproject.org/poky
cd poky
git fetch --tags
git checkout tags/yocto-3.4 -b my-yocto-3.4
