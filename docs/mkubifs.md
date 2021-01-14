# 制作UBIFS镜像

标签： Linux

利用NFS rootfs启动目标机，然后进行Flash的格式化并创建UBI卷，这种方法操作非常繁琐

直接创建可直接写入Flash的根文件系统镜像，利用barebox或者uboot直接写入，这种方法简单快捷。
1.首先建立文件系统镜像（文件系统目录为roofs）
```shell
mkfs.ubifs -r rootfs -m 2048 -e 126976 -c 1979 -o rootfs.ubifs
```
-o指定了输出的镜像名为rootfs.ubifs，-m参数指定了最小的I/O操作的大小，也就是NAND FLASH一个page的大小，-e参数指定了逻辑擦除快的大小，-c指定了最大的逻辑块号。

通过此命令制作的出的UBIFS文件系统镜像可在u-boot下使用ubi write命令烧写到NAND FLASH上。

2.使用ubinize命令可将使用mkfs.ubifs命令制作的UBIFS文件系统镜像转换成可直接在FLASH上烧写的格式
```shell
ubinize  -o rootfs.img -m 2048 -p 128KiB -s 512 -O 2048 ubinize.cfg
```
ubifnize.cfg文件内容如下：
>[ubifs]  
>mode=ubi  
>image=rootfs.ubifs  
>vol_id=0  
>vol_size=230MiB  
>vol_type=dynamic  
>vol_name=rootfs  
>vol_flags=autoresize  