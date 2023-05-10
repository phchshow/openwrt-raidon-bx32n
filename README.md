# openwrt-raidon-bx32n

# BUILD OPENWRT WORLD
  git clone https://git.openwrt.org/openwrt/openwrt.git
  cd openwrt
  git pull
  
  git checkout v21.02.3

  #Update the feeds:
  
  ./scripts/feeds update -a
  ./scripts/feeds install -a

  #Configure the firmware image and the kernel:
  
  wget https://downloads.openwrt.org/releases/21.02.3/targets/oxnas/ox820/config.buildinfo -O .config
  make menuconfig
       Target Profile -> RAIDON BX32N
  #create target/linux/oxnas/files/arch/arm/boot/dts/oxnas-raidon-bx32n.dts
  #modify target/linux/oxnas/image/ox820.mk     
  make V=s 2>&1 | tee build.log | grep -i error
  
#Flash Kernel and UBI:

  
