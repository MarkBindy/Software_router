<!-- 官方徽标 -->
<p align="Left">
  <a href="https://github.com/MarkBindy/Software_router" target="_blank">
    <img src="https://img.shields.io/badge/GitHub-router-181717?logo=github&logoColor=white"/>
  </a>
  &nbsp;
  <a href="" target="_blank">
    <img src="https://img.shields.io/badge/YouTube-@MarkBindy-FF0000?logo=youtube&logoColor=white"/>
  </a>
  &nbsp;
  <a href="" target="_blank">
    <img src="https://img.shields.io/badge/Telegram-MarkBindy-26A5E4?logo=telegram&logoColor=white"/>
  </a>
</p>

---

# 磁盘扩容（仅适用官方原版 OpenWrt 25以上系统）
确认是 SquashFS 固件，利用系统自带 loop0 回环设备实现无损在线扩容！(不需要格式化、创建 sda3 ，只需分区表在底层被拉满)
手动强制它检测并修复 GPT 尾部指针，然后执行扩容，请严格输入以下指令：

第一步：登入后台终端
```
ssh root@192.168.3.1
```
第二步：安装 parted / losetup / resize2fs 模块
```
apk update
apk add parted
apk add losetup
apk add resize2fs
```
第三步：进入 parted 交互模式并修复 GPT 尾部指针
```
parted /dev/sda
```
第四步：请在 (parted) 后面依次输入下方命令并回车（在 parted 交互界面中修复并扩容）
```
print
```
第五步：💡关键点：输入 print 后，parted 会强制扫描整块磁盘，这时它绝对会弹出提示：Warning: Not all of the space available to /dev/sda appears to be used... Fix/Ignore? 看到这个提示后，请立刻在 Fix/Ignore? 后方输入命令并回车
```
Fix
```
第六步：将第二分区在底层完美扩容至物理磁盘大小的100%
```
resizepart 2 100%
```
第七步：退出
```
quit
```
第八步：重启路由器（让 Linux 内核认清刚刚变大的物理分区边界）
```
reboot
```
第九步：重启后无损一键刷新底层空间

等待路由器重启完成，重新 SSH 登入后台终端。由于 SquashFS 固件读写层挂载在虚拟回环设备 /dev/loop0 上，直接对其在线刷新

让系统将 loop0 虚拟设备强行扩展到刚才变大的整个物理 sda2 空间
```
losetup -c /dev/loop0
```
第十步：在线把 ext4 文件系统撑满整个虚拟空间
```
resize2fs /dev/loop0
```
第十一步：输入命令检查，可用配置空间已扩容至物理磁盘的100%
```
df -h
```

# 示例展示说明：
```
root@OpenWrt:~# parted /dev/sda
GNU Parted 3.6
Using /dev/sda
Welcome to GNU Parted! Type 'help' to view a list of commands.

(parted) print

Warning: Not all of the space available to /dev/sda appears to be used, you can fix the GPT to use all of the space (an extra 61253695 blocks)
or continue with the current setting?

Fix/Ignore? fix

Model: ATA KR 32G (scsi)
Disk /dev/sda: 31.5GB
Sector size (logical/physical): 512B/512B
Partition Table: gpt
Disk Flags:

Number  Start   End     Size    File system  Name  Flags
128     17.4kB  262kB   245kB                      bios_grub
 1      262kB   17.0MB  16.8MB  fat16              legacy_boot
 2      17.0MB  126MB   109MB

(parted) resizepart 2 100%
(parted) quit

Information: You may need to update /etc/fstab.

root@OpenWrt:~# reboot
root@OpenWrt:~# Connection to 192.168.3.1 closed by remote host.
Connection to 192.168.3.1 closed.

C:\Users\z>ssh root@192.168.3.1
root@192.168.3.1's password:


BusyBox v1.37.0 (2026-06-30 07:11:43 UTC) built-in shell (ash)

  _______                     ________        __
 |       |.-----.-----.-----.|  |  |  |.----.|  |_
 |   -   ||  _  |  -__|     ||  |  |  ||   _||   _|
 |_______||   __|_____|__|__||________||__|  |____|
          |__| W I R E L E S S   F R E E D O M
 -----------------------------------------------------
 OpenWrt 25.12.5, r33051-f5dae5ece4 Dave's Guitar
 -----------------------------------------------------

 OpenWrt recently switched to the "apk" package manager!

 OPKG Command           APK Equivalent      Description
 ------------------------------------------------------------------
 opkg install <pkg>     apk add <pkg>       Install a package
 opkg remove <pkg>      apk del <pkg>       Remove a package
 opkg upgrade           apk upgrade         Upgrade all packages
 opkg files <pkg>       apk info -L <pkg>   List package contents
 opkg list-installed    apk info            List installed packages
 opkg update            apk update          Update package lists
 opkg search <pkg>      apk search <pkg>    Search for packages
 ------------------------------------------------------------------

For more information visit:
https://openwrt.org/docs/guide-user/additional-software/opkg-to-apk-cheatsheet

root@OpenWrt:~# losetup -c /dev/loop0

root@OpenWrt:~# resize2fs /dev/loop0

resize2fs 1.47.3 (8-Jul-2025)
Filesystem at /dev/loop0 is mounted on /overlay; on-line resizing required
old_desc_blocks = 1, new_desc_blocks = 235
The filesystem on /dev/loop0 is now 30727260 (1k) blocks long.

root@OpenWrt:~# df -h
Filesystem                Size      Used Available Use% Mounted on
/dev/root                 6.0M      6.0M         0 100% /rom
tmpfs                     7.8G    372.0K      7.8G   0% /tmp
/dev/loop0               27.6G     81.9M     26.3G   0% /overlay
overlayfs:/overlay       27.6G     81.9M     26.3G   0% /
/dev/sda1                16.0M      6.2M      9.8M  39% /boot
/dev/sda1                16.0M      6.2M      9.8M  39% /boot
tmpfs                   512.0K         0    512.0K   0% /dev
```
# 安装cpu温度显示监控
图形化界面
```
apk update
apk add collectd-mod-thermal
```
```
apk update
apk add luci-app-statistics
apk add luci-i18n-statistics-zh-cn
```
首页显示
```
apk update
wget --no-check-certificate -O /tmp/luci-app-temp-status-0.8.1-r1.apk https://github.com/gSpotx2f/packages-openwrt/raw/master/25.12/luci-app-temp-status-0.8.1-r1.apk
```
```
apk --allow-untrusted add /tmp/luci-app-temp-status-0.8.1-r1.apk
```
```
rm /tmp/luci-app-temp-status-0.8.1-r1.apk
```
```
service rpcd restart
```
# 恢复默认初始配置
重置

```
firstboot
```
确认

```
y
```
重启

```
reboot
```
# 更换国内镜像源（针对国内网络下载慢挂掉）
```
sed -i 's/downloads.openwrt.org/mirrors.cloud.tencent.com\/openwrt/g' /etc/apk/repositories
```
# 安装上传包内文件
```
apk add --allow-untrusted /tmp/upload.apk
```
