# OpenWRT LuCI实现网页配置界面学习

1. 轻量级 LUA 语言的官方版本只包括一个精简的核心和最基本的库。这使得 LUA 体积小、启动速度快，从而适合嵌入在别的程序里。UCI 是 Openwrt 中为实现所有系统配置的一个统一接口，
英文名 Unified Configuration Interface，即统一配置接口。LuCI 即是这两个项目的合体，可以实现路由的网页配置界面。

（最好先学习 LUA 脚本编程）

2. LuCI 使用 MVC（模型/视图/控制）模型，在/usr/lib/lua/luci/下有三个目录 model、view、controller，它们分别对应 M、 V、C。
我们要做的主要工作就是基于 LuCI 框架编写 LUA 脚本、在 html 页面中嵌入 LUA 脚本。

3. LuCI 将网页中的每一个菜单视作一个节点，如下所示：

![image](https://user-images.githubusercontent.com/58734009/187056730-6200598a-b952-4c3c-9b22-0515704e1aa7.png)

这里有一级节点 Status、System、Network、Logout，二级节点 System、Administration、Software、Startup 等。

在我们使用浏览器向路由器发起请求时，LuCI 会从 controller 目录下的 index.lua 开始组织这些节点。index.lua 中定义了根节点
root，其他所有的节点都挂在这个根节点上。

4. 我们可以将 controller 目录下的这些.lua 文件叫做模块，这样的模块文件的第一行必须是如下格式：

```
module("luci.controller.xx.xx.xx", package.seeall)
```

上面的 luci.controller.xx.xx.xx 表示该文件的路径，luci.controller 表示/usr/lib/lua/luci/controller/，比如上面的 index.lua 其
第一行为：module("luci.controller.admin.index", package.seeall)，表示其路径为：/usr/lib/lua/luci/controller/admin/index.lua

![image](https://user-images.githubusercontent.com/58734009/187058219-b2f73b5a-d5c0-4052-9106-b777fbdbdd37.png)


5. 接着是定义一个 index 方法，其主要工作是调用 entry 方法创建节点，可以多次调用创建多个节点。其调用方式如下：

```
entry (path, target, title, order)
```
  * path 指定该节点的位置（例如 node1.node2.node3）
  * target 指定当该节点被调度（即用户点击）时的行为，主要有三种：call、template 和 cbi，后面有 3 个实例。
  * title：标题，即我们在网页中看到的菜单
  * order：同一级节点之间的顺序，越小越靠前，反之越靠后（可选）


6. 实例3：cbi（P74）
该方法与 UCI 配置息息相关。主要用来修改 UCI 配置文件以及使配置生效