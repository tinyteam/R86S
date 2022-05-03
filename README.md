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

