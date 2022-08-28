# Openwrt

[使用树莓派+openwrt配置旁路由](#head1)

[OpenWRT的Web界面开发](#head2)

# <Span id = "head1">1. 使用树莓派+openwrt配置旁路由以及科学上网工具:wink:

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

   ![img1](https://user-images.githubusercontent.com/58734009/187032137-9dd291a7-f90f-4391-8275-e965c95bd0fc.png)
   
   1. p.s. 默认网关为树莓派openwrt的默认地址：192.168.1.1

4. 浏览器输入192.168.1.1进入openwrt后台:white_check_mark:
   1. 如果使用官方版本的话，第一次无需密码直接login
   2. 如果使用SuLingGG版本的话，默认密码为password
5. 进入后台后设置系统密码,到system--> administration--> router password（我设置的为123456）:white_check_mark:
6. 开启树莓派本身的wifi功能:white_check_mark:
   1. Network--> Wireless--> 选择SSID为OpenWrt--> 点击Enable--> Save
   
      ![img2](https://user-images.githubusercontent.com/58734009/187032145-192b54c7-fdec-4138-ba9b-bd0bff473ff6.png)
   
   2. 点击Edit，在Operating frequency中把Mode改成Legacy，channel改成auto，点击保存
   
      ![img3](https://user-images.githubusercontent.com/58734009/187032154-8c8e32c2-7e31-4350-8d87-b22025bfab86.png)
      
7. 更改LAN口配置:white_check_mark:

   1. 在Interface中选择LAN的edit
   
      ![img4](https://user-images.githubusercontent.com/58734009/187032164-df46a3b1-05f0-4ed6-94f6-57f54f15cde7.png)
   
   2. 更改Ipv4地址和网关
   
      * IPv4 address改成：192.168.3.10
      * IPv4 gateway改成：192.168.3.1
      * Advanced setting中custom DNS改成114.114.114.114
      
      注意：网关为我的主路由的IPv4，为了避免冲突，openwrt的IPv4改成了同一网段下不同的地址（2-254都可以），建议在电脑终端cmd--> 输入ipconfig查看一下
      
         ![img6](https://user-images.githubusercontent.com/58734009/187032179-56f4a1a6-47e8-487c-b7de-7605f29e5729.png)
         
         ![img7](https://user-images.githubusercontent.com/58734009/187032184-721abc0f-78a6-4b66-b270-d2e7e7c6f9f9.png)
      
   ***为了可玩性更高，下面截图都为使用SuLingGG的GitHub中下载的固件并使用的截图***
      
      
   3. 保存并应用后会一直保持在加载状态，是因为此时openwrt访问已经不再是192.168.1.1了（下图），此时拔掉与电脑的网线，插到路由器（主路由）的一个LAN口上
      
      * 注意：使用官方版本要在90s内完成拔掉网线并插入LAN口的操作，否则会启动rollback
      
      ![img5](https://user-images.githubusercontent.com/58734009/187032175-f75a7f18-e3d4-4f6e-8af0-9bdf60c2fde7.png)
      
8. 重新登陆到Openwrt:white_check_mark:

   浏览器中输入新设定的地址：192.168.3.10，输入密码“password”便可重新登陆
   
      ![img8](https://user-images.githubusercontent.com/58734009/187032187-67b40d5c-836c-45a3-938a-cf86303437ee.png)
      
9. 手机或其他设备此时连接名为“Openwrt”的wifi可以成功连接并上网（主路由为HUAWEI-CA1P7S）:white_check_mark:

   ![img9](https://user-images.githubusercontent.com/58734009/187032191-0bb6ac83-2237-4271-90fe-226a4ca44093.png)![img10](https://user-images.githubusercontent.com/58734009/187032194-2e9df5ba-ef46-4aaf-b67d-6d075b918700.png)

   上图可以看到主路由依旧为192.168.3.1
   
10. 设置旁路由“OpenWrt”密码，网络--> 无线 --> 无线安全， 我设置的为15042237015:white_check_mark:

   ![img11](https://user-images.githubusercontent.com/58734009/187032202-8df57820-8a98-4f29-9a67-5815053a943c.png)
   
11. 一些reference:white_check_mark:
   1. 主要的reference视频讲解：https://www.v2ex.com/t/835585 Or https://www.youtube.com/watch?v=iyQjjgOfPnQ
   2. https://www.youtube.com/watch?v=w7rwNF2Q3lM
   3. https://zhuanlan.zhihu.com/p/509064156


# <Span id = "head2">2. OpenWRT的Web界面</span>

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

## 2. 新建一个二级界面（在菜单栏System导航下添加HelloWorld选项卡） 

   * 使用WinSCP登录开发板，进入/usr/lib/lua/luci/controller/admin目录下，打开system.lua文件进行编辑，添加一行语句：
   
      ```
      entry({"admin", "system", "Helloworld"}, template("admin_system/helloworld"), _("Helloworld"), 99)
      ```

   ![image](https://user-images.githubusercontent.com/58734009/187031900-c6afabde-991f-4da4-a167-64e4daf84b33.png)

   * 进入/usr/lib/lua/luci/view/admin_system目录下，新建helloworld.htm文件，并添加一行：
   
      ```
      <%+header%> <h1><%: HelloWorld %></h1> <%+footer%>
      ```
   
   * 保存后重启树莓派并登陆OPenWRT网页，可以看到效果：
   
   ![image](https://user-images.githubusercontent.com/58734009/187032098-562add46-2a12-477f-af45-dfc0bac402c1.png)
   
   ![image](https://user-images.githubusercontent.com/58734009/187032104-38147383-e373-4622-b858-bf6f4fa9212e.png)

## 3. 新建一个一级界面，其中包含一个二级界面（菜单栏新建“Example1”导航，并在其中添加“First”选项卡）
   * 创建 lua 脚本文件：/usr/lib/lua/luci/controller/example1.lua
   * 添加内容如下
      ```
      --第一行声明模块路径
      module("luci.controller.example1", package.seeall)
      
      function index()
      --[[
         创建一级菜单 example，firstchild()表示链接到其第一个子节点，即
         当我们单击菜单 Example 时，LuCI 将调度其第一个子节点。"Example"即
         在网页中显示的菜单。60 表示其顺序，LuCI 自带的模块的顺序为：
         Administration(10),Status(20),System(30),Network(50),Logout(90)。
         call("first_action")表示当子节点被调度时将执行下面定义的方法 first_action()
       --]]
         entry({"admin", "example1"}, firstchild(), "Example1", 60)
         entry({"admin", "example1", "first"}, call("first_action"), "First")
      end
      
      function first_action()
      --加载/usr/lib/lua/luci/view/header.htm
      luci.template.render("header")
      --输出 html 内容
      luci.http.write("<h1>Hello World</h1>")
      end
      ```

      ![image](https://user-images.githubusercontent.com/58734009/187058868-832c0afe-0644-4477-81e3-93b1f338f779.png)
      
   * 查看网页端变化
   
      ![image](https://user-images.githubusercontent.com/58734009/187058886-78f0f1d9-2bc5-4715-80d4-d2b786b51dca.png)

   p.s. LUA中，用--表示单行注释，--[[ --]]表示多行注释
