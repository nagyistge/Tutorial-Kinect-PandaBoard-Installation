# Kinect-PandaBoard-Installation
PandaBoard ES + OpenNI + Kinect + Ubuntu Install Guide 

In this article, I'll explain how to install OpenNI framework and Kinect drivers to Ubuntu 12.04 Server which runs at PandaBoard-ES.

## Notation :
host# => The command to be executed at host machine(PC)
panda# => The command to be executed at pandaboard

## Requirements :

### Host Machine : 
- Ubuntu 12.04 Desktop (It may be used via VMware or VirtualBox)
- Gparted  (sudo apt-get install gparted)
- Ubuntu 12.04 Server for Arm image. Downloadable from :
http://cdimage.ubuntu.com/releases/12.04.2/release/ubuntu-12.04-preinstalled-server-armhf+omap4.img.gz
- Terminal program like PuTTy. Downloadable from:
http://www.putty.org/

### Devices:
- 2GB+ SD Card (I've used 8GB)
- Card Reader
- DB9 Cable for COM Port Communication
- Ethernet Cable
- PandaBoard-ES

## Installation:

### 1) Format the SD Card by using Gparted
```
host# sudo gparted
```

#### Possible Errors:
  - If you re-install OS to SD Card which contains the old one, the new files will be merged and may cause to malfunction. That's why when you install OS, make a clean one.

### 2) Note the partition


### 3) Untar the image 

```
host# gunzip -c ubuntu-12.04-preinstalled-server-armhf+omap4.img.gz
```

#### Possible Errors:
  - If you try to untar the downloaded file, you may get .raw file. Don't use GUI for this.

### 4) Write the image to the SD Card

```
host# sudo dd bs=4M if=ubuntu-12.04-preinstalled-server-armhf+omap4.img of=/dev/sdd
```

#### Possible Error:
   - Do not increase bs size. It is a command which indicates how many bytes will be written at once.
   - Do not forget to change "of=/dev/sdd" section. It must be the one which you have noted at step 2.

You must see something like this at the end:


### 5) Synchronize the card.

```
host# sudo sync
```

### 6) Connect ethernet & serial cables.
    Insert SD card to PandaBoard-ES.
    Power on the board.

### 7) Open Putty and connect with related COM port.

### 8) Wait for OS to be installed. In this installation, the OS will be re-sized to the maximum size of your SD Card.

#### Possible Errors:
   - If you get "Segmentation Fault", it is possible that your SD Card is damaged. Try to change with another one and repeat all the steps.

### 9) When System Configuration is prompted at PuTTy, select the language you desire.


### 10) Select the area as "United States" as you may not find your location.

### 11) Select your time zone







### 12) Enter your linux username and password.







### 13) Select your interface to connect internet. If you aim to use wireless network, be sure that the network is encrypted with WEP password type or there is no password. WPA and WPA2 encryption cannot be resolved by default.


### 14) Wait for board to get network configuration by using DHCP server.


#### Possible Errors:
   - If you are not using a DHCP server, you may need to configure the ethernet ip/mask/gateway/dns

### 15) Choose a hostname for board to make it recognizable at network


### 16) Choose your server type. Basic Ubuntu Server is raw OS installation. You may install the other servers explicitly after.


### 17) Wait for installation to be finished & login.

### 18) Get an update

```
panda# sudo apt-get update
```

### 19) Install required packages, it will take some time

```
panda# sudo apt-get install gcc-multilib libusb-1.0.0-dev git-core build-essential
panda# sudo apt-get install doxygen graphviz default-jdk freeglut3-dev libopencv-dev
```

#### Hint:
You can create an install.sh file at board, copy the code below and  and execute it.

```
sudo apt-get install gcc-multilib
sudo apt-get install libusb-1.0.0-dev
sudo apt-get install git-core
sudo apt-get install build-essential
sudo apt-get install doxygen
sudo apt-get install graphviz
sudo apt-get install default-jdk
sudo apt-get install freeglut3-dev
sudo apt-get install libopencv-dev

panda# chmod 777 install.sh
panda# ./install.sh
```

#### Hint: 
You don't have to install doxygen & graphviz as they are used to build documents. Eventhough it will cause the OpenNI installation to fail, the necessary libraries will be installed. The error will only be related with creating documents.

#### Possible Errors:
  - You can combine all the libraries to one apt-get command but it may cause problem for apt. This problem may be resolved by "panda# sudo dpkg-reconfigure -a" command.

### 20) Checkout OpenNI

```
panda# cd 
panda# mkdir kinect
panda# cd kinect
panda# git clone git://github.com/OpenNI/OpenNI.git
panda# git checkout unstable
```

#### Critical: 
If you don't install unstable release, it may cause a problem. The installation version MUST be 
"1.5.4.0 unstable"

### 21) Edit floating point information so that the build will be for Arm structure

```
panda# sudo vi OpenNI/Platform/Linux/Build/Common/Platform.Arm
```

-Find the line  

```
"CFLAGS += -march=armv7-a -mtune=cortex-a8 -mfpu=neon -mfloat-abi=softfp #-mcpu=cortex-a8"
```

-Delete -mfloat-abi=softfp :

```
"CFLAGS += -march=armv7-a -mtune=cortex-a8 -mfpu=neon #-mcpu=cortex-a8"
```

-Save the file.

#### Hint: 

Vi keys are
- :w  => to save the file
- Ins => to edit the file
- :q   => to quit
- :q! => to quit without save

### 22) Build OpenNI

```
panda# cd ~/kinect/OpenNI/Platform/Linux/CreateRedist
panda# sudo ./RedistMaker.Arm
panda# cd ~/kinect/OpenNI/Platform/Linux/Redist/OpenNI-Bin-Dev-Linux-Arm-v1.5.4.0
panda# sudo ./install.sh
```

### 23) Checkout Kinect Drivers

```
panda# cd ~/kinect
panda# git clone git://github.com/avin2/SensorKinect.git
panda# cd SensorKinect/Platform/Linux/Build/Common/
panda# sudo vi Platform.Arm
```

Like in step 21, delete -mfloat-abi=softfp line

### 24) Install Kinect Drivers

```
panda# cd ~/kinect/SensorKinect/Platform/Linux/CreateRedist
panda# sudo ./RedistMaker
panda# cd  ~/kinect/SensorKinect/Platform/Linux/Redist/Sensor-Bin-Linux-Arm-v5.1.2.1/Config
panda# sudo vi GlobalDefaultsKinect.ini
```

-Find the line ";UsbInterface=2" and edit it like "UsbInterface=1"

```
panda# cd ~/kinect/SensorKinect/Platform/Linux/Redist/Sensor-Bin-Linux-Arm-v5.1.2.1
panda# sudo ./install.sh
```

#### Hint: 

GlobalDefaultsKinect.ini file is the configuration file for OpenNI. You may edit and configure the video output as you want. (i.e. flipped, only IR, resolution etc..)

#### Possible Errors:
   - If you didn't checkout to unstable version, the build will be failed here.

### 25) Restart Board & Run Sample

```
panda# sudo reboot
panda# cd ~/kinect/OpenNI/Platform/Linux/Redist/OpenNI-Bin-Dev-Linux-Arm-v1.5.4.0/Samples/Bin/Arm-Release
panda# ./Sample-NiSimpleRead
```

You will see something like this:






