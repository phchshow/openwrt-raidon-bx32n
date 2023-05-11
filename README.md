# openwrt-raidon-bx32n

# BUILD OPENWRT WORLD
    git clone https://git.openwrt.org/openwrt/openwrt.git
    cd openwrt
    git pull
  
    git checkout v21.02.3

    # Update the feeds:
 
    ./scripts/feeds update -a
    ./scripts/feeds install -a
 
    # Configure the firmware image and the kernel:
  
    wget https://downloads.openwrt.org/releases/21.02.3/targets/oxnas/ox820/config.buildinfo -O .config
    
    # create target/linux/oxnas/files/arch/arm/boot/dts/oxnas-raidon-bx32n.dts
    # modify target/linux/oxnas/image/ox820.mk     
    
    make menuconfig
       Target Profile -> RAIDON BX32N    
    make V=s 2>&1 | tee build.log | grep -i error
  
# Flash Kernel and UBI:
*Ensure reboot from oem website
    
    #open nas case to connect uart serial
    #I use FTDI Cable 5V VCC-3.3V I/O connect. Left to right VCC RXD GND TXD, connect CTS<--->RTS.
    
    #use connect uart terminal ex: minicom
    minicom -D /dev/ttyUSB0 //FTDI device
    
    #wait u-boot
    run load_kernel2
    bootm 60500000
    
    #mount usb. //busybox, openwrt-kernel and openwrt-filesystem.bin in usb.
    mount /dev/sdc1 /mnt
    cd /mnt
    #flash_earse mtd3 and use busybox nandwrite
    flash_earse /dev/mtd3
    busybox nandwrite -p /dev/mtd3 openwrt-kernel
    ubiformat /dev/mtd6 -f openwrt-filesystem.bin
    reboot
    #should can run openwrt
    
    #if eth can't work, reinstall oem uImage.bin and filesystem.bin
    #flash oem system.
    run load_kernel2
    bootm 60500000
    #mount usb. //oem's uImage.bin and filesystem.bin in usb.
    mount /dev/sdc1 /mnt
    cd /mnt
    flash_earse /dev/mtd3
    nandbd -k uImage /dev/mtd3
    ubiflash /dev/mtd6 -f filesystem.bin
    reboot
    #if still can't use eth. unplug power line, re-plugin power ling,click power.
    #oem system will check eth status and repair it.// will try check oem how to repair eth  
    #oem system is back. re-flash openwrt step again.
 # openwrt 
     
     install kmod-ata-oxnas-sata kmod-usb-storage kmod-usb2-oxnas
     //hdd, usb storage will be find.
     //LED should can't use. I have't learned i2c and gpio.
