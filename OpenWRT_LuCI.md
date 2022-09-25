# OpenWRT LuCI实现网页配置界面学习

HTML教程：https://www.w3schools.com/tags/tag_th.asp

一些LuCI教程（未整理）：
1. openwrt web管理luci界面修改：https://www.shuzhiduo.com/A/KE5QlAg4JL/
2. OpenWrt路由器系统开发与网页设计：https://www.shuzhiduo.com/A/LPdokQmyd3/
3. OpenWRT - WEB界面开发思路和基本方法：https://www.shuzhiduo.com/A/n2d9Y070dD/
4. MT7628学习笔记（15）——修改LuCI界面左上角LOGO：https://blog.csdn.net/p1279030826/article/details/109099952
5. openwrt luci管理的Web界面实例（佐大）：https://www.openwrt.pro/post-494.html
6. Openwrt Luci界面开发（佐大）：https://www.openwrt.pro/post-349.html
7. openwrt luci管理的Web界面实例（hello world, ip地址）：https://juejin.cn/post/6844903638280699912
8. OpenWrt Luci web开发-luci mvc架构讲解及实例：https://zhuanlan.zhihu.com/p/566238777
9. openwrt web管理luci界面修改：https://blog.csdn.net/viewsky11/article/details/29838517

## 1. 轻量级 LUA 语言的官方版本只包括一个精简的核心和最基本的库。这使得 LUA 体积小、启动速度快，从而适合嵌入在别的程序里。UCI 是 Openwrt 中为实现所有系统配置的一个统一接口，
英文名 Unified Configuration Interface，即统一配置接口。LuCI 即是这两个项目的合体，可以实现路由的网页配置界面。

（最好先学习 LUA 脚本编程）

## 2. MVC总结
MVC(Model，View，Controller)模式是一种软件设计模式。视图（view）既软件与用户交互的界面，模型（model）表示数据以及业务规则，控制器（Controller）连接二者的桥梁，接受视图所提交的请求，调用模型去完成请求。

LuCI 使用 MVC（模型/视图/控制）模型，在/usr/lib/lua/luci/下有三个目录 model、view、controller，它们分别对应 M、 V、C。\
LUCI从某些功能的实现来讲属于MVC框架。使用winSCP进入openwrt系统，可以发现在usr/lib/lua/luci文件夹下：

/usr/lib/lua/luci/controller/   --控制层: 负责转发请求，对请求进行处理\
/usr/lib/lua/luci/view/ 		--视图层: 界面设计人员进行图形界面设计\
/usr/lib/lua/luci/model/cbi/	--模型层：程序员编写程序应有的功能（实现算法等等）、数据库专家进行数据管理和数据库设计(可以实现具体的功能)\

view文件夹下有大量html文件，即luci的视图文件。controller文件夹下存着逻辑控制程序。model文件夹下的cbi文件夹存在着处理各种请求的程序 。

我们要做的主要工作就是基于 LuCI 框架编写 LUA 脚本、在 html 页面中嵌入 LUA 脚本。

### 2.1 Model





## 3. LuCI 将网页中的每一个菜单视作一个节点，如下所示：

![image](https://user-images.githubusercontent.com/58734009/187056730-6200598a-b952-4c3c-9b22-0515704e1aa7.png)

这里有一级节点 Status、System、Network、Logout，二级节点 System、Administration、Software、Startup 等。

在我们使用浏览器向路由器发起请求时，LuCI 会从 controller 目录下的 index.lua 开始组织这些节点。index.lua 中定义了根节点
root，其他所有的节点都挂在这个根节点上。

## 4. 我们可以将 controller 目录下的这些.lua 文件叫做模块，这样的模块文件的第一行必须是如下格式：

```
module("luci.controller.xx.xx.xx", package.seeall)
```

上面的 luci.controller.xx.xx.xx 表示该文件的路径，luci.controller 表示/usr/lib/lua/luci/controller/，比如上面的 index.lua 其
第一行为：module("luci.controller.admin.index", package.seeall)，表示其路径为：/usr/lib/lua/luci/controller/admin/index.lua

![image](https://user-images.githubusercontent.com/58734009/187058219-b2f73b5a-d5c0-4052-9106-b777fbdbdd37.png)


## 5. 接着是定义一个 index 方法，其主要工作是调用 entry 方法创建节点，可以多次调用创建多个节点。其调用方式如下：

```
entry (path, target, title, order)
```
  * path 指定该节点的位置（例如 node1.node2.node3）
  * target 指定当该节点被调度（即用户点击）时的行为，主要有三种：call、template 和 cbi，后面有 3 个实例。
  * title：标题，即我们在网页中看到的菜单
  * order：同一级节点之间的顺序，越小越靠前，反之越靠后（可选）

LuCI 默认开启了缓存机制，这样当我们修改了代码后，不会立即生效，除非删除缓存，操作如下：

![image](https://user-images.githubusercontent.com/58734009/191983451-823d94ab-1064-47fb-bed0-3f007d8919df.png)

## 6. 实例1
![image](https://user-images.githubusercontent.com/58734009/191985893-a6245819-a5e3-40d2-a02f-870f02bee073.png)

![image](https://user-images.githubusercontent.com/58734009/191990973-49a7933c-1ca5-402a-bd20-141afe49ecb1.png)

## 7. 实例2：template

效果和上面一样，不同的是上节使用 call 调度执行一个函数，本节直接调用一个 html 页面。
创建 html 文件：/usr/lib/lua/luci/view/example# ls example.htm，其内容如下

![image](https://user-images.githubusercontent.com/58734009/191991452-e7f57795-bf53-4b4b-ae3d-2dc2ce19bb47.png)

这里的<% %>用来在 html 代码中嵌入 LUA 脚本，这里的<%+header%>表示首先加载/usr/lib/lua/luci/view/header.htm

修改后的/usr/lib/lua/luci/controller/example.lua 内容如下：

![image](https://user-images.githubusercontent.com/58734009/191992222-af228ff1-49a9-44a6-932b-4c2aeafad24a.png)

对比前一种方法，entry只是从call变成了template

firstchild (): Alias the first (lowest order) page automatically

## 8. 实例3：cbi（P74）
该方法与 UCI 配置息息相关。主要用来修改 UCI 配置文件以及使配置生效


