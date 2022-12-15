# Operating-System-Project-2
Develop a custom Operating System for raspberrypi-3 from the open souce Yocto project. 
In this project we built a custom OS image for embedded system of raspberrypi-3 from the Yocto-project and added custom linux packages as per the requirements.
Finally flash the image on the SD-card and booted the raspberrypi-3 with our custom OS.

## What is a Yocto Project?
Yocto Project is a cool open-source community project that allows you to build your own Linux system from scratch to booting into your embedded hardware, which makes embedded Linux simple and accessible. It helps developers create customized systems based on the Linux kernel by providing useful templates, tools, and methods, and wide hardware architectures support on ARM, PPC, MIPS, x86 (32 & 64 bit). Whereas other Linux distributions are built for enterprise servers and workstations and then (possibly) tailored down for embedded use cases, the Yocto Project enables the build of customized distributions for embedded devices. In a disparate market with heterogeneous requirements, the project seeks to define a common ground for embedded development, independent of the underlying architecture of the hardware.
## Prerequisite

1. Runs a supported Linux distribution (i.e. recent releases of Fedora, openSUSE, CentOS, Debian, or Ubuntu). For a list of Linux distributions that support the Yocto Project, see the Supported Linux Distributions section in the Yocto Project Reference Manual. For detailed information on preparing your build host, see the Preparing the Build Host section in the Yocto Project Development Tasks Manual.

2. Git 1.8.3.1 or greater

3. tar 1.28 or greater

4. Python 3.5.0 or greater.

5. gcc 5.0 or greater.

6. If you are using Ubuntu, it is preferred to use Ubuntu 20.04 LTS.

## Steps for building the image
### Build host packages
You must install essential host packages on your build host. The following command installs the host packages based on an Ubuntu distribution:

```$ sudo apt-get install gawk wget git-core diffstat unzip texinfo gcc-multilib build-essential chrpath socat cpio python3 python3-pip python3-pexpect xz-utils debianutils iputils-ping python3-git python3-jinja2 libegl1-mesa libsdl1.2-dev pylint3 xterm python3-subunit mesa-common-dev```
>Note For host package requirements on all supported Linux distributions, see the [Required Packages for the Build Host](https://docs.yoctoproject.org/3.1.21/ref-manual/ref-system-requirements.html#required-packages-for-the-build-host) section in the Yocto Project Reference Manual.

### Download poky and metadata layers
First go to the project folder. Then run the following commands:
```
git clone -b dunfell \
    git://git.yoctoproject.org/poky.git 
```
![image](https://user-images.githubusercontent.com/79767012/207872349-3454b235-f2e3-44e3-af9d-b8d4521b459c.png)

After this go to the poky/ directory created and then run the following commands. This will create the respective directories of the layers inside the poky directory.
```
git clone -b dunfell \
    git://git.yoctoproject.org/meta-raspberrypi.git
```
![image](https://user-images.githubusercontent.com/79767012/207872923-ddd0cf4d-57eb-4ee1-a48b-0d98e579a86c.png)
```
git clone -b dunfell \
    git://git.openembedded.org/meta-openembedded
```
![image](https://user-images.githubusercontent.com/79767012/207886100-5ea74250-6d7a-49d8-97b8-f4ad6e3a786b.png)

### Create the build folder
Go the previous directory (i.e. outside poky) and run
```
source poky/oe-init-build-env
```
### Adding the layers 
Now we will add the layers that we cloned previously to the image.
Check your directory, you'll be inside the build folder inside your project folder.
Now inside the build folder run the following commands to automatically add the layers inside the bblayers.conf file inside the build/conf/
```
bitbake-layers add-layer ../poky/meta-openembedded/meta-oe
bitbake-layers add-layer ../poky/meta-openembedded/meta-python
bitbake-layers add-layer ../poky/meta-openembedded/meta-multimedia
bitbake-layers add-layer ../poky/meta-openembedded/meta-networking
bitbake-layers add-layer ../poky/meta-raspberrypi
```
To check if the layers are added successfully run the following command:
```
bitbake-layers show-layers
```
![image](https://user-images.githubusercontent.com/79767012/207894215-c94897ab-f046-469c-b9d4-06b74569a07f.png)
### Set build config
Go to the build/conf/ directory and edit the local.conf file. 
Set the target machine to raspberrypi3-64.
```
MACHINE ?= "raspberrypi3-64"
```
### To install the custom packages to the custom image.
Add the following to the local.conf file inside build/conf/
```
IMAGE_INSTALL_append = " sudo apt packagegroup-core-buildessentials grep curl man man-db"
```
### Build the image
Run the following command:
```
bitbake core-image-minimal
```
## Flash the image to the SD-card
Now we have to extract the os image built and flash it to the removable media such as SD-card or pendrive.

After the image building process finishes, go to the build/tmp/deploy/images/raspberrypi3-64 directory. 

Here we'll find the ```core-image-base-raspberrypi3-64.wic.bz2``` file. 

Extract this file to any other directory, this extracted file will be flashed on to the removable storage.

Install the raspberrypi [rpi-imager](https://snapcraft.io/install/rpi-imager/ubuntu) which can easily flash the os to the removable media.

Open the rpi-imager and choose the extracted image and the removable storage where the os needs to be flashed. Click the write button to flash.

![image](https://user-images.githubusercontent.com/79767012/207922385-542a79fc-9a52-4964-9f67-d6f19a64e719.png)

The image is ready to be booted.
