This file contains information for build a kernel linux 3.12 with patch ethernet for olimex am3352_SOM



A) Toolchain CC:
-----------------------------------------

in home/user:

$ wget https://launchpadlibrarian.net/129963014/gcc-linaro-arm-linux-gnueabihf-4.7-2013.01-20130125_linux.tar.xz
$ tar xf gcc-linaro-arm-linux-gnueabihf-4.7-2013.01-20130125_linux.tar.xz

$ mv gcc-linaro-arm-linux-gnueabihf-4.7-2013.01-20130125_linux arm-gnueabihf-4.7-2013.01
$ sudo mkdir /usr/local/cc/linaro
$ sudo mv arm-gnueabihf-4.7-2013.01 /usr/local/cc/linaro

on .bashrc add:
export CC=/usr/local/cc/linaro/arm-gnueabihf-4.7-2013.01/bin/arm-linux-gnueabihf-

control version:
$ ${CC}gcc --version
arm-linux-gnueabihf-gcc (crosstool-NG linaro-1.13.1-4.8-2014.03 - Linaro GCC 2014.03) 4.8.3 20140303 (prerelease)
Copyright (C) 2013 Free Software Foundation, Inc.
This is free software; see the source for copying conditions.  There is NO
warranty; not even for MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE. 
  
  
B) Kernel:
-----------------------------------------

in home/user:

$ git clone https://github.com/hehopmajieh/linux-3.12.10-ti2013.12.01-am3352_som 
$ cd linux-3.12.10-ti2013.12.01-am3352_som/ 

patch ethernet

$ wget -c https://rcn-ee.com/repos/git/u-boot-patches/v2016.03/0001-am335x_evm-uEnv.txt-bootz-n-fixes.patch
$ patch -p1 < ethernet.patch

$ make ARCH=arm ${CC} am3352_som_defconfig 
$ make ARCH=arm menuconfig 
$ make ARCH=arm CROSS_COMPILE=${CC} -j4 zImage dtbs 

$ make ARCH=arm CROSS_COMPILE=${CC} -j4 INSTALL_MOD_PATH=out modules
$ make ARCH=arm CROSS_COMPILE=${CC} -j4 INSTALL_MOD_PATH=out modules_install 

$ make ARCH=arm CROSS_COMPILE=${CC} -j4 INSTALL_MOD_PATH=out firmware
$ make ARCH=arm CROSS_COMPILE=${CC} -j4 INSTALL_MOD_PATH=out firmware_install


C) Copy in rootfs
------------------------------------------

$ sudo cp arch/arm/boot/zImage /mnt/sd/boot/ 
$ sudo cp arch/arm/boot/dts/am335x-olimex-som.dtb /mnt/sd/boot/am335x-olimex-som.dtb

$ sudo rm -rf /mnt/sd/lib/modules/* 
$ sudo cp -rfv out/lib/modules/3.x.xx+/ /mnt/sd/lib/modules/ 
$ sudo cp -rfv out/lib/firmware/* /mnt/sd/lib/firmware/ 
$ sync


D) Resolve errors apt get update:
--------------------------------------------

in /home/olimex:

wget http://rcn-ee.com/repos/debian/pool/main/r/rcn-ee-archive-keyring/rcn-ee-archive-keyring_2016.04.24~bpo90+20160424+1_all.deb	
sudo dpkg -i rcn*.deb
sudo apt-get update && upgrade