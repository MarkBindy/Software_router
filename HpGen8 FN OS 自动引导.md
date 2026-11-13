---
# SSH工具切换root用户并开启自动引导

第一步：登入后台终端（更换成自己飞牛NAS IP 地址）
```
ssh 账号@192.168.3.1
```
第二步：账号切换成 root 用户
再次输入密码，提示: root@FnOS 表示root用户切换成功
```
sudo su -
```
第三步：输入 lsblk ,根据提示上的容量大小,查看 SD 卡 / U 盘的盘符
```
lsblk
```
第四步：卸载 SD 卡 / U 盘,路径需要按具体情况填写
```
umount /vol00/Internal_SD-CARD
```
第五步：创建磁盘分区表,输入 y 确认
```
parted /dev/sdd mklabel msdos
```
第六步：创建磁盘分区
```
parted /dev/sdd mkpart primary fat32 1M 1024M
```
第七步：格式化分区
```
mkfs.fat -F32 /dev/sdd1
```
第八步：挂载 SD 卡 / U 盘到mnt下
```
mount /dev/sdd1 /mnt/
```
第九步：安装对应架构的 GRUB 软件包
HP MicroServer Gen8 默认使用传统的 Legacy BIOS (MBR) 引导方式，请先运行以下命令安装 PC 架构的 GRUB 组件（以 Debian / Ubuntu / PVE 系统为例）：
(注：如果你在 BIOS 中开启了 UEFI 引导，则需要安装 grub-efi-amd64-bin)
```
apt update
apt install grub-pc-bin
```
第十步：写入引导：等待安装完成后，回到命令行提示符，执行安装命令：
(注：如果之前报错或提示找不到目录，也可以改用 --boot-directory=/mnt/boot)
```
grub-install --target=i386-pc --root-directory=/mnt /dev/sda
```
验证成功：如果终端返回 Installation finished. No error reported
说明 GRUB 引导已经成功写入到 /dev/sda 中了

第十一步：将刚创建好的grub分区激活，否则还是无法自动引导
```
fdisk /dev/sdd
```
输入 p 打印分区情况

输入 a 激活分区

再次输入 p 查看激活情况（Boot底下有个*，表示激活成功）

输入 w 保存修改

第十二步：将 SATA5 上的 /boot/grub/grub.cfg 复制到 /mnt/boot/grub/
```
cp /boot/grub/grub.cfg /mnt/boot/grub/
```
第十三步：重启系统验证
```
reboot
```

# 示例说明：
https://am.zdmimg.com/202505/12/6821f8ebe76ba1885.jpg_e1080.jpg
https://am.zdmimg.com/202505/12/6821f8eb107d51885.jpg_e1080.jpg
https://am.zdmimg.com/202505/12/6821f8ebcdbfb1885.jpg_e1080.jpg
https://am.zdmimg.com/202505/12/6821f8eb44a0e1885.jpg_e1080.jpg
