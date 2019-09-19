### 本镜像 移植自espressobin的Armbian_5.75

具有下述特性：

1. 干净
2. armbian系列工具，包括可一键安装omv的armbianconfig
3. 可选择性使用sata或emmc做系统盘，如需emmc做系统盘，进入系统后输入nand-sata-install

##### 对比原镜像修改过的地方:
```
/boot/image/
/boot/dtb/marvell/catdrive.dtb
/etc/update-motd.d/10-armbian-header
/usr/sbin/nand-sata-install
/etc/hostname
```
##### 刷机参考 [hanwckf 猫盘 (ARMADA A3720) 刷机教程](https://www.jianshu.com/p/77e529fb35f9)
