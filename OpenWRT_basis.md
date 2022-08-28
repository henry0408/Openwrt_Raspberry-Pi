## 1. OpenWRT文件系统目录结构

FHS： File Hierarchy Standard文件层次标准

  1. bin目录
    bin: binary，负责存放程序的文件。
    e.g. usr/bin, usr/sbin；
    sbin：s是system or supervised的缩写，是存放关于权限相关的

  2. etc目录：一般是配置文件（configuration），例如DHCP，DNS

  3. mnt目录(opt目录)：挂载点：CD ROM/USB Disk

  4. Proc目录：kernel创建；和sys并存。存放CPU型号等

  5. usr目录：用户目录，没有那么重要，很杂

  6. www目录

  7. dev目录：系统kernel创建，设备

  8. lib目录：library库

  9. ROM目录：静态文件
  10. tmp目录：临时文件，系统级别文件，不能删

## 2. OpenWRT常用命令

![image](https://user-images.githubusercontent.com/58734009/187064982-4de07121-bffc-4a29-b65a-83bbd87ea8a4.png)

cat dhcp：查看dhcp文件里内容（简短的）；长的使用vi

touch test：新建test文件

rm：删除文件/文件夹

mv：move更改文件名/文件夹 mv test test01：将test改名为test01

grep nr "dhcp":检索所有有dhcp字样的文件

mkdir：创建文件夹
