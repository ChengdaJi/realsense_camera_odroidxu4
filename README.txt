## Report on how to get the R200 work on Odroid_XU4

Chengda Ji and Changxin Yan

# Step 1: Build the Ubuntu 16.04 lts onto a micro usb card/emmc card

Official Release: http://east.us.odroid.in/ubuntu_16.04lts/
Choose ubuntu-16.04-mate-odroid-xu3-20160708.img.xz
Download and burn it onto the emmc or micro-usb

# Step 2: Install ROS
Connect the odroid with the ethernet, ssh into the odroid.
ssh root@192.168.1.xxx
sudo apt-get update && 'sudo apt-get dist-upgrade
Then binary install the ros according to: http://wiki.ros.org/Installation/UbuntuARM


# Step 3: Update kernel
git clone --depth 1 --single-branch -b odroidxu4-4.9.y https://github.com/hardkernel/linux
cd linux  
make odroidxu4_defconfig
make -j 8 zImage dtbs modules
sudo cp arch/arm/boot/zImage arch/arm/boot/dts/*.dtb /media/boot
sudo cp .config /media/boot/config
sudo make modules_install
sudo make firmware_install
sudo make headers_install INSTALL_HDR_PATH=/usr
kver=`make kernelrelease`
sudo cp .config /boot/config-${kver}
cd /boot
sudo update-initramfs -c -k ${kver}
sudo mkimage -A arm -O linux -T ramdisk -a 0x0 -e 0x0 -n initrd.img-${kver} -d initrd.img-${kver} uInitrd-${kver}
sudo cp uInitrd-${kver} /media/boot/uInitrd

Note 1:  that the ethernet config is not perfect, thus the ethernet is not going work. Use a ethernet to usb convert can fix that problem.
Note 2: the binary make step is take 30 mins or so for emmc and 40 mins for micro-usb card, please be patient.

# Step 4: Source install the librealsense
ssh root@192.168.1.xxx
git clone https://github.com/IntelRealSense/librealsense.git
sudo apt-get upgrade && sudo apt-get dist-upgrade
sudo apt-get install libusb-1.0-0-dev pkg-config
sudo apt-get install libglfw3-dev
mkdir build && cd build
cmake ../
cmake ../ -DBUILD_EXAMPLES=true
make && make install

# Step 5: Source install the realsense_camera package
{make a catkin workspace, name it whatever you like, we named it as ros_catkin_ws} 
cd ros_catkin_ws/src
git clone https://github.com/ChengdaJi/realsense_camera_odroidxu4.git
cd ..
catkin_make

Note: the realsense_camera package is not the origin package from the intel.co, we changed part of the code to fix the time frame issue.
The origin source code is:  https://github.com/jim1993/realsense_camera_odroid.git
 
# Step 6 Reboot the system

# Step 7 plug in the r200 and enjoy!

Accknowledge:
thankd to jim1993 https://github.com/jim1993/realsense_camera_odroid.git
