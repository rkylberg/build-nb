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
