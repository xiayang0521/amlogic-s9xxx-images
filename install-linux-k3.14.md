#0.按新版boot修改和分区重新布局
将新版镜像的boot文件覆盖过来除了（不覆盖uInitrd，zImage），修改对应启动项比如dtb等
启动后，对分区重新布局
```
ampart /dev/mmcblk0  -m dclone boot::256M:1 data::-1:4

===================================================================================
ID| name            |          offset|(   human)|            size|(   human)| masks
-----------------------------------------------------------------------------------
 0: bootloader                      0 (   0.00B)           400000 (   4.00M)      0
    (GAP)                                                 2000000 (  32.00M)
 1: reserved                  2400000 (  36.00M)          4000000 (  64.00M)      0
    (GAP)                                                  800000 (   8.00M)
 2: cache                     6c00000 ( 108.00M)                0 (   0.00B)      0
    (GAP)                                                  800000 (   8.00M)
 3: env                       7400000 ( 116.00M)           800000 (   8.00M)      0
    (GAP)                                                  800000 (   8.00M)
 4: boot                      8400000 ( 132.00M)         10000000 ( 256.00M)      1
    (GAP)                                                  800000 (   8.00M)
 5: data                     18c00000 ( 396.00M)        392800000 (  14.29G)      4
===================================================================================
```

#1.晶晨分区下，挂载data分区
```
mount /dev/data /mnt
```
把rootfs数据写进/dev/data
#2. 进行分区操作
```
parted -s /dev/mmcblk0 mklabel msdos
parted -s /dev/mmcblk0 mkpart  primary  fat32  132MiB  387MiB
mkfs.vfat -F 32 -n "BOOT_EMMC"  /dev/mmcblk0p1
mount /dev/mmcblk0p1 /mnt/boot
```
同样，按新版镜像安装到emmc的时对boot相关文件处理办法，进行修改，即可完成。
