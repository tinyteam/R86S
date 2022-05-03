# R86S

## PVE Install

> PVE Installation: https://pve.proxmox.com/pve-docs/chapter-pve-installation.html

### Create bootable USB drives

> PVE Prepare Installation Media: https://pve.proxmox.com/wiki/Prepare_Installation_Media
>
> PVE ISO Images: https://www.proxmox.com/en/downloads/category/iso-images-pve
> 
> rufus: https://rufus.ie/

```
# for linux
dd bs=1M conv=fdatasync if=./proxmox-ve_*.iso of=/dev/XYZ
```

### Start the installation in debug mode

1. Install Proxmox VE (Debug mode)
2. 在第一次提示你可以输入命令的时候输入 Ctrl-D ，继续安装过程
3. 在第二次提示你可以输入命令的时候输入 vi /usr/bin/proxinstall 编辑文件
4. 定位报错信息位置：/unable to get device，并添加 mmcblk 判断条件

```
    } elsif ($dev =~ m|^/dev/mmcblk\d+$|) {
        return "${dev}p$partnum";
    } else {
        die "unable to get device for partition $partnum on device $dev\n";
    }
```
5. 保存退出后，继续 Ctrl-D

### Mirrors configuration

```
vi /etc/apt/sources.list
```

```
# 阿里云
deb http://mirrors.aliyun.com/debian bullseye main contrib

deb http://mirrors.aliyun.com/debian bullseye-updates main contrib

deb http://mirrors.aliyun.com/debian-security bullseye-security main contrib

# 中国科学技术大学 Linux 用户协会
deb http://mirrors.ustc.edu.cn/proxmox/debian/pve bullseye pve-no-subscription
```

```
apt-get update && apt-get upgrade

reboot
```

### PCI passthrough

> https://pve.proxmox.com/wiki/Pci_passthrough

```
vi /etc/default/grub

    GRUB_CMDLINE_LINUX_DEFAULT="quiet intel_iommu=on iommu=pt"

update-grub

vi /etc/modules

    vfio
    vfio_iommu_type1
    vfio_pci
    vfio_virqfd
    
reboot

dmesg | grep 'remapping'
```

### Configuring SR-IOV for a Mellanox ConnectX-3 NIC

> https://that.guru/blog/sriov-mellanox-connectx-3/

```
apt install mstflint

lspci | grep Mellanox

mstconfig -d 04:00.0 query

mstconfig -d 04:00.0 set SRIOV_EN=1 NUM_OF_VFS=8

reboot

mstconfig -d 04:00.0 query
mstflint -d 04:00.0 q

vi /etc/modprobe.d/mlx4_core.conf

    options mlx4_core num_vfs=4,4,0 port_type_array=2,2 probe_vf=4,4,0 probe_vf=4,4,0
    
modprobe -r mlx4_en mlx4_ib
modprobe mlx4_en
```


