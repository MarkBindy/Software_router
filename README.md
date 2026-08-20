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
# 安装上传包内文件
```
apk add --allow-untrusted /tmp/upload.apk
```
