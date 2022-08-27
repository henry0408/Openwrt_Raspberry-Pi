# Openwrt

# 使用树莓派+openwrt配置旁路由以及科学上网工具:wink:

## Steps::bulb:
1. Openwrt树莓派烧录:white_check_mark:
   1. 官网找到对应固件的openwrt版本，https://firmware-selector.openwrt.org/
      1. 或者使用其他大神已编译好的固件，我用的是SuLingGG https://github.com/SuLingGG/OpenWrt-Rpi 可以找到对应的树莓派版本：https://doc.openwrt.cc/2-OpenWrt-Rpi/
   2. 下载带FACTORY的镜像文件
   3. 电脑安装烧录软件：balenaEtcher
   4. SD格式化软件：SD card Formatter，https://www.sdcard.org/downloads/formatter/
   5. 电脑插入SD卡，用烧录软件balenaEtcher将.img文件烧录进SD卡（若卡中有内容，则需要先格式化）

2. 用一根网线将树莓派和电脑链接，并给树莓派上电，断掉电脑其他网络连接:white_check_mark:
3. 在电脑中更改**以太网**属性（主要是防止冲突）:white_check_mark:

   <img width="800" height="500" src="https://github.com/henry0408/screenshots/blob/09b152f2bf1435fc6d20f64465d3ea6396f18254/img1.png">
   
   1. p.s. 默认网关为树莓派openwrt的默认地址：192.168.1.1

4. 浏览器输入192.168.1.1进入openwrt后台:white_check_mark:
   1. 如果使用官方版本的话，第一次无需密码直接login
   2. 如果使用SuLingGG版本的话，默认密码为password
5. 进入后台后设置系统密码,到system--> administration--> router password（我设置的为123456）:white_check_mark:
6. 开启树莓派本身的wifi功能:white_check_mark:
   1. Network--> Wireless--> 选择SSID为OpenWrt--> 点击Enable--> Save
   
      <img width="800" height="350" src="https://github.com/henry0408/screenshots/blob/67a411e77a09ef5c0031e8b1828d079ed128f58e/img2.png">
   
   2. 点击Edit，在Operating frequency中把Mode改成Legacy，channel改成auto，点击保存
   
      <img width="800" height="500" src="https://github.com/henry0408/screenshots/blob/87f26a117e5cdb4c0c0e29f865c979babef965a7/img3.png">
      
7. 更改LAN口配置:white_check_mark:
   1. 在Interface中选择LAN的edit
   
      <img width="800" height="350" src="https://github.com/henry0408/screenshots/blob/a975bede140b3e3bc7f7b7b04bb5e828da51a725/img4.png">
   
   2. 更改Ipv4地址和网关
   
      * IPv4 address改成：192.168.3.10
      * IPv4 gateway改成：192.168.3.1
      * Advanced setting中custom DNS改成114.114.114.114
      
      注意：网关为我的主路由的IPv4，为了避免冲突，openwrt的IPv4改成了同一网段下不同的地址（2-254都可以），建议在电脑终端cmd--> 输入ipconfig查看一下
      
         <img width="400" height="150" src="https://github.com/henry0408/screenshots/blob/ae8aee5a0c373eaf9281126a24a60cf14479456e/img6.png">
         
         <img width="800" height="180" src="https://github.com/henry0408/screenshots/blob/ae8aee5a0c373eaf9281126a24a60cf14479456e/img7.png">
      
   ***为了可玩性更高，下面截图都为使用SuLingGG的GitHub中下载的固件并使用的截图***
      
      
   3. 保存并应用后会一直保持在加载状态，是因为此时openwrt访问已经不再是192.168.1.1了（下图），此时拔掉与电脑的网线，插到路由器（主路由）的一个LAN口上
      
      * 注意：使用官方版本要在90s内完成拔掉网线并插入LAN口的操作，否则会启动rollback
      
      <img width="800" height="470" src="https://github.com/henry0408/screenshots/blob/12b195662410e911a21d279c5ed5eeff04d57678/img5.png">
      
8. 重新登陆到Openwrt:white_check_mark:

   浏览器中输入新设定的地址：192.168.3.10，输入密码“password”便可重新登陆
   
      <img width="900" height="680" src="https://github.com/henry0408/screenshots/blob/13eaac551b39b80b0212186dbb86281a410b589b/img8.png">
      
9. 手机或其他设备此时连接名为“Openwrt”的wifi可以成功连接并上网（主路由为HUAWEI-CA1P7S）:white_check_mark:

   <img width="280" height="400" src="https://github.com/henry0408/screenshots/blob/850ec31085a6652dd402c94fa4a1cfc346500914/img9.png"><img width="280" height="500" src="https://github.com/henry0408/screenshots/blob/850ec31085a6652dd402c94fa4a1cfc346500914/img10.png">

   上图可以看到主路由依旧为192.168.3.1
   
10. 设置旁路由“OpenWrt”密码，网络--> 无线 --> 无线安全， 我设置的为15042237015:white_check_mark:

   <img width="800" height="480" src="https://github.com/henry0408/screenshots/blob/8bc0b4ea2949b7d06e7019453d5752efe4a9bc97/img11.png">
   
11. 一些reference:white_check_mark:
   1. 主要的reference视频讲解：https://www.v2ex.com/t/835585 Or https://www.youtube.com/watch?v=iyQjjgOfPnQ
   2. https://www.youtube.com/watch?v=w7rwNF2Q3lM
   3. https://zhuanlan.zhihu.com/p/509064156


# OpenWRT的Web界面

## 1. 要访问openwrt文件，首先需要远程登陆到开发板（树莓派）
   1. 在网页中访问192.168.3.10进入openwrt，在系统--> 管理权中找到“网关端口”，并确认其已经打开
   
   ![image](https://user-images.githubusercontent.com/58734009/187030054-378676b0-b0d1-4ef8-89af-13f82e0cc002.png)
   
   否则之后的步骤会出现“由于目标计算机积极拒绝%2C无法连接。+连接失败”等错误

   2. 下载并安装WinSCP，用于在Windows和开发板之间传输并可以直接修改文件
   
      * 点击新建会话，协议选择FTP
      
      ![image](https://user-images.githubusercontent.com/58734009/187031532-33b22c0a-da3c-4724-adfa-675c2b341aac.png)

      * 端口切换称22，主机名为OPenWrt开发板的ip：我们的为192.168.3.10，用户名和密码分别为OpenWRT网页登陆时用的用户名和密码：我们的分别为root和password
      
      * 点击登陆，效果如图
      
      ![image](https://user-images.githubusercontent.com/58734009/187030255-7f1c10c4-9530-482a-a792-272ff2158973.png)
      
      其中左侧为Windows目录，右侧为OpenWRT目录，可以直接拖拽

      
