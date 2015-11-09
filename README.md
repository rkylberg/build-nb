Build Niobium
=============

```
# prepare directories:
cd ~
mkdir -p yocto/shared/downloads
mkdir -p yocto/shared/sstate-cache

# install Yocto Poky:
cd yocto
git clone git://git.yoctoproject.org/poky

# checkout latest version: Fido
cd poky
git checkout -b fido origin/fido

# clone additional layers into poky directory (will be ignored):
git clone https://github.com/rkylberg/oe-meta-audio.git meta-audio
git clone git://git.openembedded.org/openembedded-core meta-core
git clone git://git.openembedded.org/meta-openembedded meta-oe
git clone git://github.com/bmwcarit/meta-ros.git meta-ros

# checkout latest version (fido) where available:
cd ../meta-core
git checkout -b fido origin/fido
cd ../meta-oe
git checkout -b fido origin/fido
cd ..

# update max_user_watches:
echo fs.inotify.max_user_watches=524288 | sudo tee -a /etc/sysctl.conf && sudo sysctl -p
# sysctl -n fs.inotify.max_user_watches

# init build-nb:
source oe-init-build-env ../build-nb

# replace bblayers.conf and local.conf templates with provided resources:
cp res/bblayers.conf conf/bblayers.conf
cp res/local.conf conf/local.conf

# build image:
bitbake core-image-full-cmdline
```

Burn Niobium
============

Insert SD card into system, erase it's content and peform the following commands to copy the Niobium build onto it:

```
dmesg | tail
sudo fdisk /dev/sdb
n, p, 1, 2048, +32M
n, p, 2, 67584, 7774207
a, 1, t, 1, c
w

sudo mkfs.vfat -n "BOOT" /dev/sdb1
sudo mkfs.ext4 -L "ROOT" /dev/sdb2

cd tmp/deploy/images/beaglebone/

sudo cp MLO-beaglebone /media/rkylberg/BOOT/MLO
sudo cp u-boot.img /media/rkylberg/BOOT/
sudo cp zImage-beaglebone.bin /media/rkylberg/BOOT/zImage
sudo cp zImage-am335x-boneblack.dtb /media/rkylberg/BOOT/

sudo tar -xf core-image-full-cmdline-beaglebone.tar.bz2 -C /media/rkylberg/ROOT/
sudo tar -xf modules-beaglebone.tgz -C /media/rkylberg/ROOT
sudo cp zImage-am335x-bone.dtb /media/rkylberg/ROOT/boot/am335x-bone.dtb
sudo cp zImage-am335x-boneblack.dtb /media/rkylberg/ROOT/boot/am335x-boneblack.dtb
```

Boot Niobium
============

1. Insert SD card into beaglebone.
1. Attach FTDI cable between beaglebone and host computer.
1. Configure host serial program: 115,200 baud, 8 bits, N parity, 1 stop bit, no handshake
1. Connect network connected ethernet cable to beaglebone.
1. Connect power to the beaglebone while holding the SD card boot switch.
1. Release the boot switch when serial program starts logging boot messages.
1. Examine the boot messages in order to determine the ip address assigned to the beaglebone.

Niobium Log-in
==============

From serial port, no password is needed to login as user root.

From host terminal, no password is needed to login as user root with this command: ```ssh root@192.168.0.18```

