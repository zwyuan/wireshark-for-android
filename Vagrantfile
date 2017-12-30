# -*- mode: ruby -*-
# vi: set ft=ruby :

# MobileInsight Vagrant Installation Script
# Copyright (c) 2017 MobileInsight Team
# Author: Zengwen Yuan, zyuan (at) cs.ucla.edu
# Version: 1.2

$INSTALL_BASE = <<SCRIPT
apt-get update
apt-get -y install build-essential pkg-config
apt-get -y install autoconf automake zlib1g-dev libtool
apt-get -y install bison byacc flex ccache
# apt-get -y install libc6:i386 libncurses5:i386 libstdc++6:i386 lib32z1 libbz2-1.0:i386
# gem install android-sdk-installer
# easy_install pip

SCRIPT


$CREATE_NDK_TOOLCHAIN = <<SCRIPT
# Download and setup Android NDK r15c
cd ~
wget https://dl.google.com/android/repository/android-ndk-r15c-linux-x86_64.zip
unzip android-ndk-r15c-linux-x86_64.zip
echo 'export ANDROID_NDK_HOME=/home/vagrant/android-ndk-r15c' >> ~/.bashrc
echo 'PATH=$PATH:$ANDROID_NDK_HOME' >> ~/.bashrc
source ~/.bashrc
rm android-ndk-r15c-linux-x86_64.zip

# Create MobileInsight dev folder at /home/vagrant/mi-dev
cd ~/android-ndk-r15c
./build/tools/make_standalone_toolchain.py \
    --arch arm \
    --api 26 \
    --stl gnustl \
    --unified-headers \
    --install-dir /home/vagrant/android-ndk-toolchain

cd ~
cp /vagrant/envsetup.sh .
chmod +x envsetup.sh
source envsetup.sh

SCRIPT


$DOWNLOAD_TARBALLS = <<SCRIPT
wget http://ftp.gnu.org/pub/gnu/libiconv/libiconv-1.15.tar.gz
tar xf libiconv-1.15.tar.gz
rm libiconv-1.15.tar.gz

wget http://ftp.gnu.org/pub/gnu/gettext/gettext-0.19.8.tar.gz
tar xf gettext-0.19.8.tar.gz
rm gettext-0.19.8.tar.gz

wget https://gnupg.org/ftp/gcrypt/libgpg-error/libgpg-error-1.27.tar.bz2
tar xf libgpg-error-1.27.tar.bz2
rm libgpg-error-1.27.tar.bz2

wget https://gnupg.org/ftp/gcrypt/libgcrypt/libgcrypt-1.8.1.tar.bz2
tar xf libgcrypt-1.8.1.tar.bz2
rm libgcrypt-1.8.1.tar.bz2

wget http://ftp.gnome.org/pub/gnome/sources/glib/2.54/glib-2.54.0.tar.xz
tar xf glib-2.54.0.tar.xz
rm glib-2.54.0.tar.xz

wget https://2.na.dl.wireshark.org/src/wireshark-2.4.1.tar.xz
tar xf wireshark-2.4.1.tar.xz
rm wireshark-2.4.1.tar.xz

SCRIPT


$COMPILE_LIBICONV = <<SCRIPT
cd ~/libiconv-1.15
./configure --build=${BUILD_SYS} --host=arm-eabi --prefix=${PREFIX} --disable-rpath
make
make install
SCRIPT


$COMPILE_GETTEXT = <<SCRIPT
cd ~/gettext-0.19.8
./configure --build=${BUILD_SYS} --host=arm-eabi  --prefix=${PREFIX} --disable-rpath --disable-java --disable-native-java --disable-libasprintf --disable-openmp --disable-curses
make
make install

SCRIPT


$COMPILE_LIBGPGERROR = <<SCRIPT
cd ./libgpg-error-1.27
./configure --build=${BUILD_SYS} --host=${TOOLCHAIN} --prefix=${PREFIX} --enable-static --disable-shared
make
make install

SCRIPT


$COMPILE_LIBGCRYPT = <<SCRIPT
cd ./libgcrypt
./configure --build=${BUILD_SYS} --host=${TOOLCHAIN} --prefix=${PREFIX} --enable-static --disable-shared
make
make install

SCRIPT


$COMPILE_GLIB = <<SCRIPT
cd ~/glib-2.54.0
./configure --build=${BUILD_SYS} --host=${TOOLCHAIN} --prefix=${PREFIX} --disable-dependency-tracking --cache-file=android.cache --enable-included-printf --enable-static --with-pcre=no --disable-libmount
make
make install

SCRIPT


$COMPILE_WIRESHARK = <<SCRIPT
cd ~/wireshark-2.4.1
./configure \
    --host=${TOOLCHAIN} \
    --target=${TOOLCHAIN} \
    --build=${BUILD_SYS} \
    --prefix=${PREFIX} \
    --with-sysroot=${SYSROOT} \
    --without-gnutls \
    --without-plugins \
    --without-pcap \
    --without-libgcrypt-prefix \
    --disable-wireshark \
    --disable-packet-editor \
    --disable-profile-build \
    --disable-tshark \
    --disable-editcap \
    --disable-capinfos \
    --disable-captype \
    --disable-mergecap \
    --disable-reordercap \
    --disable-text2pcap \
    --disable-dftest \
    --disable-randpkt \
    --disable-dumpcap \
    --disable-rawshark \
    --disable-sharkd \
    --disable-tfshark \
    --disable-pcap-ng-default \
    --disable-androiddump \
    --disable-sshdump \
    --disable-ciscodump \
    --disable-randpktdump \
    --disable-udpdump \
      "$@"
make
make install

SCRIPT


$COPY_LIBS = <<SCRIPT
# Copy MobileInsight apk to local folder
cd ~
mkdir ws_libs
cd ws_libs
cp ~/androidcc/lib/libglib-2.0.so .
cp ~/androidcc/lib/libgmodule-2.0.so .
cp ~/androidcc/lib/libgthread-2.0.so .
cp ~/androidcc/lib/libwireshark.so .
cp ~/androidcc/lib/libwiretap.so .
cp ~/androidcc/lib/libwsutil.so .

cp -r ~/ws_libs /vagrant/

SCRIPT

Vagrant.configure(2) do |config|
  config.vm.box = "bento/ubuntu-16.04"
  config.vm.box_version = "201708.22.0"

  config.vm.provider "virtualbox" do |vb|
    # # Display the VirtualBox GUI when booting the machine
    # vb.gui = true

    # Customize the amount of memory and cpus on the VM:
    vb.memory = "2048"
    vb.cpus = 2
  end

  config.vm.provision "shell", privileged: true, inline: $INSTALL_BASE
  config.vm.provision "shell", privileged: false, keep_color: true, inline: $CREATE_NDK_TOOLCHAIN
  config.vm.provision "shell", privileged: false, keep_color: true, inline: $DOWNLOAD_TARBALLS
  config.vm.provision "shell", privileged: false, keep_color: true, inline: $COMPILE_LIBICONV
  config.vm.provision "shell", privileged: false, keep_color: true, inline: $COMPILE_GETTEXT
  config.vm.provision "shell", privileged: false, keep_color: true, inline: $COMPILE_LIBGPGERROR
  config.vm.provision "shell", privileged: false, keep_color: true, inline: $COMPILE_LIBGCRYPT
  config.vm.provision "shell", privileged: false, keep_color: true, inline: $COMPILE_GLIB
  config.vm.provision "shell", privileged: false, keep_color: true, inline: $COMPILE_WIRESHARK
  config.vm.provision "shell", privileged: false, keep_color: true, inline: $COPY_LIBS
end