# Openwrt-

# 使用树莓派+openwrt配置旁路由以及科学上网工具

## Steps:
1. Openwrt树莓派烧录
   1. 官网找到对应固件的openwrt版本，https://firmware-selector.openwrt.org/
      1. 或者使用其他大神已编译好的固件，我用的是SuLingGG https://github.com/SuLingGG/OpenWrt-Rpi 可以找到对应的树莓派版本：https://doc.openwrt.cc/2-OpenWrt-Rpi/
   2. 下载带FACTORY的镜像文件
   3. 电脑安装烧录软件：balenaEtcher
   4. SD格式化软件：SD card Formatter，https://www.sdcard.org/downloads/formatter/
   5. 电脑插入SD卡，用烧录软件balenaEtcher将.img文件烧录进SD卡（若卡中有内容，则需要先格式化）

2. 用一根网线将树莓派和电脑链接，并给树莓派上电，断掉电脑其他网络连接
3. 在电脑中更改**以太网**属性（主要是防止冲突）

   <img width="800" height="500" src="https://github.com/henry0408/screenshots/blob/09b152f2bf1435fc6d20f64465d3ea6396f18254/img1.png">
   
   1. p.s. 默认网关为树莓派openwrt的默认地址：192.168.1.1

4. 浏览器输入192.168.1.1进入openwrt后台（第一次无需密码直接login）
5. 进入后台后设置系统密码,到system--> administration--> router password（我设置的为123456）
6. 开启树莓派本身的wifi功能
   1. Network--> Wireless--> 选择SSID为OpenWrt--> 点击Enable--> Save
   
      <img width="800" height="350" src="https://github.com/henry0408/screenshots/blob/67a411e77a09ef5c0031e8b1828d079ed128f58e/img2.png">
   
   2. 点击Edit，在Operating frequency中把Mode改成Legacy，channel改成auto，点击保存
   
      <img width="800" height="500" src="https://github.com/henry0408/screenshots/blob/87f26a117e5cdb4c0c0e29f865c979babef965a7/img3.png">
