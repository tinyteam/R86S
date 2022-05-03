# Build openwrt image

## Build system setup

> https://openwrt.org/docs/guide-developer/toolchain/install-buildsystem

```
# Debian 11
sudo apt update
sudo apt install build-essential gawk gcc-multilib flex git gettext libncurses5-dev libssl-dev python3-distutils zlib1g-dev rsync unzip

git clone https://git.openwrt.org/openwrt/openwrt.git
git checkout v21.02.3

./scripts/feeds update -a
./scripts/feeds install -a

wget https://downloads.openwrt.org/releases/21.02.3/targets/x86/64/config.buildinfo -O .config
make menuconfig
make -j $(nproc) kernel_menuconfig

# https://downloads.openwrt.org/releases/21.02.3/targets/x86/64/kmods/5.4.188-1-db222399ef90ea6bd3453fe08b323a74/
vi .vermagic
    db222399ef90ea6bd3453fe08b323a74
    
vi include/kernel-defaults.mk
    grep '=[ym]' $(LINUX_DIR)/.config.set | LC_ALL=C sort | mkhash md5 > $(LINUX_DIR)/.vermagic
    cp $(TOPDIR)/.vermagic $(LINUX_DIR)/.vermagic

vi package/kernel/linux/Makefile
  # STAMP_BUILT:=$(STAMP_BUILT)_$(shell $(SCRIPT_DIR)/kconfig.pl $(LINUX_DIR)/.config | mkhash md5)
  STAMP_BUILT:=$(STAMP_BUILT)_$(shell cat $(LINUX_DIR)/.vermagic)
  

make -j $(nproc) defconfig download clean world
```
