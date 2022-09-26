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

需要解决的问题：
1. WinSCP怎么用vscode
2. OpenWRT如何看到接入设备的ip
3. UCI和CBI模块详细
4. Where does the LuCI web interface get the current noise level? https://www.reddit.com/r/openwrt/comments/j7ejph/where_does_the_luci_web_interface_get_the_current/

## 1. 轻量级 LUA 语言的官方版本只包括一个精简的核心和最基本的库。这使得 LUA 体积小、启动速度快，从而适合嵌入在别的程序里。UCI 是 Openwrt 中为实现所有系统配置的一个统一接口，
英文名 Unified Configuration Interface，即统一配置接口。LuCI 即是这两个项目的合体，可以实现路由的网页配置界面。

（最好先学习 LUA 脚本编程）

## 2. MVC总结
MVC(Model，View，Controller)模式是一种软件设计模式。视图（view）既软件与用户交互的界面，模型（model）表示数据以及业务规则，控制器（Controller）连接二者的桥梁，接受视图所提交的请求，调用模型去完成请求。\
![image](https://user-images.githubusercontent.com/58734009/192149812-176c6dc2-7744-4864-a239-8ca52ed172f4.png)


LuCI 使用 MVC（模型/视图/控制）模型，在/usr/lib/lua/luci/下有三个目录 model、view、controller，它们分别对应 M、 V、C。\
LUCI从某些功能的实现来讲属于MVC框架。使用winSCP进入openwrt系统，可以发现在usr/lib/lua/luci文件夹下：

/usr/lib/lua/luci/controller/   --控制层: 负责转发请求，对请求进行处理\
/usr/lib/lua/luci/view/ 		--视图层: 界面设计人员进行图形界面设计\
/usr/lib/lua/luci/model/cbi/	--模型层：程序员编写程序应有的功能（实现算法等等）、数据库专家进行数据管理和数据库设计(可以实现具体的功能)\

view文件夹下有大量html文件，即luci的视图文件。controller文件夹下存着逻辑控制程序。model文件夹下的cbi文件夹存在着处理各种请求的程序 。

我们要做的主要工作就是基于 LuCI 框架编写 LUA 脚本、在 html 页面中嵌入 LUA 脚本。

### 2.1 Controller

控制层中的文件语法精简结构如下，主要为两个部分：***模块声明*** 和***路由定义***。
#### 例子1：
![image](https://user-images.githubusercontent.com/58734009/192149847-0e3dac34-9443-485b-99e1-d7e9c93d5ebc.png)

##### 1. 模块声明：
文件定义时需要声明其模块名称，按照其路径定义即可，比如该文件位于/luci/controller/admin/network.lua

![image](https://user-images.githubusercontent.com/58734009/192149887-d5a1dd29-73d9-47f2-843c-1c42e6c3babd.png)

##### 2. 路由定义
上面代码中我们可以看到使用了两种定义路由的方式:
* 一种是直接使用entry方法来定义一个路由，并传入以下4个参数。
* 第二种方式是使用node方法直接定义路由的路径，然后分开指定其他target, title等属性。

entry方法内部也是使用node方法，所以两种方式等效。

enrtry方法：\
![image](https://user-images.githubusercontent.com/58734009/192150383-8bce3184-8de0-42b3-97b2-be8dfb4a73bf.png)

path： 规定了此路由的访问路径，参数是一个字符串数组，比如 {“admin”, “test”, “test_view”} 这样的参数，在页面访问的时候，路径为 /admin/test/test_view ，"test" 是一级菜单。"test_view" 为二级菜单。如果使用node方法直接创建，则需要散列传参，不需要传入table。

target：规定访问此路径时，Luci框架执行的调用目标，根据调用目标的不同，LuCI也有不同的调度行为。所有的target类型，在源码文件 dispatcher.lua中可以找到。
target主要使用的有alias、firstchild、call、template、cbi、form、arcombine。

title：定义该节点在菜单上显示的名称，非必填项，如果使用了国际化配置，可以使用_(“Router”)这样的表示方式，在中文环境下显示路由，在英文环境下显示Router。如果不填title，则不会在菜单栏上面显示。但是可以通过url进行访问。

order：该节点在同级节点下的显示顺序。非必填项，从上至下，从左至右数值越小显示越靠前。

target主要有如下种类：

* firstchild() ：自动指向该节点的第一个子节点（order最小的那个节点）
* call(func_name)：调用该文件下的指定函数，比如 call(“test_function”)，则会在访问当前路径时，调用该文件下的test_function函数。在这些函数中，我们也可以处理一些数据。
* template(tpl_name)：根据给定的HTM模板名称访问指定的HTML页面，路径对应luci/view下面的文件。比如template(“admin_system/hello”)，对应luci/view/admin_system/hello.htm 文件。
* cbi(model,config)：根据给定的model文件名，渲染一个CBI模型。路径对应luci/model/cbi下面的文件。同时可以传入一些config信息，比如 {nofooter:不显示cbi_footer,noheader:不显示cbi_header} 等等。其他用法可以在源码 dispatcher.lua 的 _cbi方法中进行查看。
* form(model) : 与cbi()类似，不过渲染的是一个表单CBI模型。
* arcombine(trg1,trg2)：当需要访问同一个路由，而这个路由在有参和无参时的调用目标需要不一致的时候，可以使用arcombine把两个target进行组合起来(比如某个列表和其详情页分开时)。当有参数时调用trg2，无参时调用trg1.
* alias(Path)：重定向到另外一个节点上。
 ![image](https://user-images.githubusercontent.com/58734009/192153099-3a810683-9664-4319-9c63-068c6c316e35.png)

节点属性：使用变量接收node方法或者entry方法返回的节点后，可以选择添加的属性。

* i18n :定义了在请求页面时应自动加载哪个翻译文件
* dependent :保护插件在父节点丢失时从上下文中调用
* leaf :停止在此节点处解析请求，并且在调度树中不再进一步
* sysauth :要求用户使用指定的帐户进行身份验证
* sysauth_authenticator :验证时手动指定验证方法
* setgroup ：设置当前进程的组ID
* setuser：设置当前进程的用户ID

***上面的属性的使用，在dispatcher.lua文件中的dispatch方法中有体现***

####  例子2：lua 文件说明

1）index.lua 这个文件定义了node ，最外面的节点，最上层菜单的显示等等。\
2）其他的 lua 文件里，定义了其他菜单的显示和html 以及业务处理路径。每个文件对应一个菜单项。

例如system.lua:\
1）module是对应文件的\
2）function index 定义了菜单，

比如这一句entry({"mini", "system", "reboot"}, call("action_reboot"), i18n("reboot"), 100)

第1 项为菜单入口：{"mini", "system", "reboot"}中，mini 是最上层的菜单，即为用户项； system 为一个具体的菜单；reboot 为这个菜单的子菜单；\
第2项为菜单对应的页面，可以是lua 的源代码文件，也可以是 html 页面。alias 、cbi 、form 、call 等定义了此菜单相应的处理方式。

 * form 和 cbi 对应到 model/cbi 相应的目录下面，那里面是对应的定制好的 html 和 lua 业务处理。
 * alias是等同于别的链接
 * call 调用了相应的 action_function  
 * template直接链接到view 相应目录下面的 htm 页面

第3项为i18n 显示（即语言替换）\
第4项为现实的顺序，这个数字越小，显示越靠前，靠上。

#### 例子3
例如我们在视图（界面中）打卡wireless—>overview：\
在控制器中对应处理函数即```entry({"admin","network","wireless"},arcombine(template("admin_network/wifi_overview"), cbi("admin_network/wifi")), _("Wifi"), 15)```

其中"admin", "network","wireless"表示视图所在位置，cbi("admin_network/wifi"))表示其处理模型为/usr/lib/lua/luci/model/cbi下的wifi.lua。

#### 例子4
LUCI中某些功能的实现在model中并没有专门的模型程序，因为其在Controller文件中写了功能函数做了处理。例如中的add操作，在cbi下并没有对应的程序做处理，在Controller文件中定义了function wifi_add()函数，执行对应操作。

### 2.2 View

在controller中提到，target 可以使用 template 可以指定对应的htm文件作为界面显示。同样，在使用cbi，form等方法指定target时，最后也会根据htm文件来渲染界面。具体流程在后续解释。这个htm文件所属范畴就是View中，浏览器界面主要由两种途径产生，使用CBI模型进行生成，或者是自定义htm显示。CBI模型是使用Luci预定义的一些CBI组件来生成常用的表单。如果需要制作一些CBI文件满足不了的页面，可以自己定义htm文件来满足需求。

htm文件中通常由三大部分构成：Lua代码、HTML代码、JavaScript代码

#### 例子1：
![image](https://user-images.githubusercontent.com/58734009/192154475-6b334d15-9027-4f41-a533-03240b9f1059.png)

语法解释：\
在htm文件中，可以在HTML代码和JavaScript代码中插入lua代码，使用下面的语法格式标志代码为Lua代码。可以嵌套进行使用。具体实例在后续CBI模型渲染过程的解析中。

* 1. 包含Lua代码块:
 ![image](https://user-images.githubusercontent.com/58734009/192154538-0bf2128d-708a-4b77-b6fd-f2b482914edc.png)

* 2. 输出变量和函数值：
 ![image](https://user-images.githubusercontent.com/58734009/192154567-30f23b55-90df-4d93-92d3-44eab666300f.png)

* 3. 引用另一个模板:
 ![image](https://user-images.githubusercontent.com/58734009/192154579-b4db6759-cb93-4147-bb91-105774a45bd5.png)

* 4. 翻译转换：
 ![image](https://user-images.githubusercontent.com/58734009/192154591-0a65ed20-69c0-4e68-b7c7-f642f30969c7.png)

* 5. 注释：
 ![image](https://user-images.githubusercontent.com/58734009/192154612-3106185a-1f1c-4c82-8fe2-33e25290316e.png)

#### 例子2：
对于一些视图页面上信息动态显示，luci采用在html文件中嵌入lua程序，利用API直接获取，采用JavaScript动态显示的方法实现。\
例如关联移动台信息的显示问题，直接在view/admin_network/wifi_overview.htm文件下利用嵌入lua，与JavaScript语言进行动态显示

### 2.3 Model
在controller中提到。target 使用 cbi可以根据给出的文件创建一个CBI模型进行界面渲染。官方的说，CBI模型是Lua文件描述UCI配置文件的结构和由此参生的HTML表单来评估CBI解析器。通俗的说就是CBI模型通常由LuCI预定义的一些CBI控件组成，不同的CBI控件根据其对应的模板生成页面上可见的表单元素。这些控件可以执行大部分的UCI配置的获取或者设置的处理工作。我们也可以覆盖其本来的行为来达到我们满足项目需求的目的。

在我们定义的CBI文件中，与UCI的配置息息相关，UCI配置暂时可以理解为系统的各个应用程序的配置的统一接口。后面再详细介绍UCI。***CBI模型文件定义在 /model/cbi/ 目录下***

#### 简单介绍一下CBI模型中的语法结构。后面对CBI控件进行详细介绍。以下面的代码为例。
![image](https://user-images.githubusercontent.com/58734009/192155890-6d0bfdec-4663-4424-a87a-32953c910eeb.png)

所有CBI luci.cbi.Map类型的模型文件必须返回一个map对象。

![image](https://user-images.githubusercontent.com/58734009/192155902-867ffdb2-06c0-455d-837e-e0fa2b39d189.png)



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


