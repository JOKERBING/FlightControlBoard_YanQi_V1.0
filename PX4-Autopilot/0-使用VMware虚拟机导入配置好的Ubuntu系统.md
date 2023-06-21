# 使用VMware虚拟机导入配置好的Ubuntu系统

此教程是根据阿木实验室提供的Ubuntu虚拟机的开发环境配置进行编写的。

****

阿木实验室【PX4飞控源码和QGC开发环境】内有VMware12，配置好环境的Ubuntu14.04系统

资料下载地址：

链接：http://pan.baidu.com/s/1gfJzNKv 密码：o9rx

阿木实验室【Ubuntu安装教程】内有配置好环境的Ubuntu18.04和Ubuntu16.04系统

资料下载地址：

链接：https://pan.baidu.com/s/14RrhjzzTSz6On_BL4c6j5Q 密码：d4xr

VMware15.5下载地址：

链接：https://pan.baidu.com/s/1KmySvDBrl5EtUUacJoKp7w 密码：r7f8

VMware16下载地址：

链接：https://pan.baidu.com/wap/init?surl=U5iBMbNiaD0WUE14HG7R-g 密码：z5ld

****

## 安装VMware

资料中是一个名为【PX4飞控源码和QGC开发环境】的文件夹。

首先运行文件夹下的VMware_workstation_full_12.5.0.11529 (1).exe程序安装VMware 12。

<div align=center><img src="https://jokerbin.oss-cn-beijing.aliyuncs.com/202210102232890.png" width="500"></div>

点击【下一步】，选择软件安装位置，一直点击继续到完成安装。

<div align=center><img src="https://jokerbin.oss-cn-beijing.aliyuncs.com/202210102242627.png" width="500"></div>

点击【许可证】。

<div align=center><img src="https://jokerbin.oss-cn-beijing.aliyuncs.com/202210102243564.png" width="500"></div>

输入VMware 12的密钥。

<div align=center><img src="https://jokerbin.oss-cn-beijing.aliyuncs.com/202210102243498.png" width="500"></div>

到此即可完成VMware 12的安装。

## 导入Ubuntu系统

### 导入Ubuntu14.04系统

文件夹【PX4飞控源码和QGC开发环境】中develop_ubuntu_system.part01.rar到develop_ubuntu_system.part15.rar是虚拟机文件包，解压后可以得到develop_ubuntu_system文件夹。

<div align=center><img src="https://jokerbin.oss-cn-beijing.aliyuncs.com/202210102257524.png" width="700"></div>

运行vmware，单击【打开虚拟机】，选择develop_ubuntu_system文件夹中的Ubuntu_for_QGC.vmx文件打开。

<div align=center><img src="https://jokerbin.oss-cn-beijing.aliyuncs.com/202210102325419.png" width="500"></div>

在弹出的提示中选择【获取所有权】。

<div align=center><img src="https://jokerbin.oss-cn-beijing.aliyuncs.com/202210102326774.png" width="700"></div>

单击【继续运行此虚拟机】。

<div align=center><img src="https://jokerbin.oss-cn-beijing.aliyuncs.com/202210102338395.png" width="500"></div>

在弹出的提示中选择【我已复制该虚拟机】。

<div align=center><img src="https://jokerbin.oss-cn-beijing.aliyuncs.com/202210102343244.png" width="500"></div>

在弹出的提示中选择【确认】。

<div align=center><img src="https://jokerbin.oss-cn-beijing.aliyuncs.com/202210102344799.png" width="500"></div>

在弹出的提示中选择【放弃】。

<div align=center><img src="https://jokerbin.oss-cn-beijing.aliyuncs.com/202210102346853.png" width="700"></div>

单击【开启此虚拟机】。

<div align=center><img src="https://jokerbin.oss-cn-beijing.aliyuncs.com/202210102348804.png" width="500"></div>

在弹出的提示中选择【否】。

<div align=center><img src="https://jokerbin.oss-cn-beijing.aliyuncs.com/202210102354569.png" width="500"></div>

在弹出的提示中选择【确定】。

等待片刻即可进入Ubuntu系统。

<div align=center><img src="https://jokerbin.oss-cn-beijing.aliyuncs.com/202210102350487.png" width="700"></div>

虚拟机的登录密码是`AMOV`，Ubuntu14.04，40G的配置空间，安装好了编译环境、QGC、QT。  

### 导入Ubuntu18.04或Ubuntu16.04系统

文件夹【ubuntu】中ubuntu18.04_20-02-12.iso和ubuntu-16.04.6-desktop-amd64.iso是两个版本的系统镜像。

<div align=center><img src="https://jokerbin.oss-cn-beijing.aliyuncs.com/202210111757247.png" width="700"></div>

运行vmware，单击【创建新的虚拟机】。

<div align=center><img src="https://jokerbin.oss-cn-beijing.aliyuncs.com/202210111757116.png" width="500"></div>

在弹出的向导中选择【典型】，点击【下一步】。

<div align=center><img src="https://jokerbin.oss-cn-beijing.aliyuncs.com/202210111758518.png" width="500"></div>

选择下载好的镜像文件，这里选择的是18.04版本，点击【下一步】。

<div align=center><img src="https://jokerbin.oss-cn-beijing.aliyuncs.com/202210111759691.png" width="500"></div>

选择【Linux】和【Ubuntu64位】，点击【下一步】。

<div align=center><img src="https://jokerbin.oss-cn-beijing.aliyuncs.com/202210111800988.png" width="500"></div>

这里可以设置虚拟机的名称和虚拟机文件的存储位置，自行设置，点击【下一步】。

<div align=center><img src="https://jokerbin.oss-cn-beijing.aliyuncs.com/202210111801264.png" width="500"></div>

设置虚拟机的磁盘容量，按需设置，点击【下一步】。

<div align=center><img src="https://jokerbin.oss-cn-beijing.aliyuncs.com/202210111801050.png" width="500"></div>

这里可以点击【自定义硬件】进行进一步配置，如果需要内存较多可以自行配置，点击【完成】即可。

<div align=center><img src="https://jokerbin.oss-cn-beijing.aliyuncs.com/202210111806888.png" width="700"></div>

单击【开启此虚拟机】，等待片刻即可进入Ubuntu系统。虚拟机的登录密码是`amov`。

阿木实验室提供的虚拟机镜像文件是使用systemback工具创建的，开机后会出现引导界面。

<div align=center><img src="https://jokerbin.oss-cn-beijing.aliyuncs.com/202211021947575.png" width="500"></div>

选择第二项【Boot system installer】进行系统安装，直接跳到输入新用户名新密码的界面，或者选择第一项进入【Boot Live system】后打开终端执行命令。

```
sudo systemback
```

<div align=center><img src="https://jokerbin.oss-cn-beijing.aliyuncs.com/202211021949350.png" width="500"></div>

选择【System install】选项，会弹出一个界面输入你的新用户名和密码，后点击【Next】。

<div align=center><img src="https://jokerbin.oss-cn-beijing.aliyuncs.com/202211021951138.png" width="500"></div>

以下的设置都是针对虚拟机，如果是双系统请另行找教程设置存储配置。选择【/dev/sda】之后点击右侧的【!Delete!】按钮。

<div align=center><img src="https://jokerbin.oss-cn-beijing.aliyuncs.com/202211021954730.png" width="500"></div>

之后选择【/dev/sda？】之后点击右侧的【左箭头】按钮。

<div align=center><img src="https://jokerbin.oss-cn-beijing.aliyuncs.com/202211021955494.png" width="500"></div>

之后选择【/dev/sda1】之后将右侧的Mount point设置为`/`，点击【左箭头】按钮。`Transfer user configuration and data files`务必设置成打钩，`Install GRUB 2 bootloader`设置为`Auto`，之后点击【Next】。

<div align=center><img src="https://jokerbin.oss-cn-beijing.aliyuncs.com/202211021955509.png" width="500"></div>

在弹出的窗口选择【Start】。

<div align=center><img src="https://jokerbin.oss-cn-beijing.aliyuncs.com/202211021955341.png" width="500"></div>

之后等待安装系统即可。

<div align=center><img src="https://jokerbin.oss-cn-beijing.aliyuncs.com/202211021956762.png" width="500"></div>

安装进度条到100%时可能会出现以下报错。

```
The restore point creation is aborted!
There has been critical changes in the file system during this operation.
```

<div align=center><img src="https://jokerbin.oss-cn-beijing.aliyuncs.com/202211021934188.png" width="500"></div>

查看终端中的报错说明，发现是snap文件夹下的文件复制出错导致报错。

```
An error occurred while cloning the following synbolic link:
/.systenbacklivepoint/snap/gnone-42-2204/current
Target synlink:
/.sbsystencopy/snap/gnone-42-2204/current
```

<div align=center><img src="https://jokerbin.oss-cn-beijing.aliyuncs.com/202211021935277.png" width="500"></div>

解决方法是删除/snap下所有的文件。

可以使用命令列出所有snap安装包。

```
sudo snap list
```

可以使用以下语句一次性卸载所有安装包。

```
sudo apt autoremove --purge snapd
```

执行过程中可能会有进程占用的问题，需要使用以下命令删除文件。

```
sudo rm /var/lib/dpkg/lock-frontend
sudo rm /var/lib/dpkg/lock
```

成功运行卸载命令后，再次列出所有snap安装包发现提示已经没有snap这个文件夹了。

这时再次使用systemback恢复系统即可。

<div align=center><img src="https://jokerbin.oss-cn-beijing.aliyuncs.com/202211021957728.png" width="500"></div>

这时等待进度条到100%后会显示安装完成，之后重启系统即可。



****

参考资料：

[win10/Ubuntu vmware使用Ubuntu18.04虚拟机_超维空间科技的博客-CSDN博客](https://mbot1.blog.csdn.net/article/details/121270282)

[VMware安装时failed to install the hcmon drivers和failed to install the USB inf 解决办法_今天又有什么bug的博客-CSDN博客](https://blog.csdn.net/weixin_46019681/article/details/121240993)

[Ubuntu server 18.04 利用Systemback制作系统镜像和还原_独孤冷者的博客-CSDN博客_systemback制作系统镜像](https://blog.csdn.net/weixin_39178682/article/details/116206030)

[封装ubuntu系统 - 往事已成昨天 - 博客园 (cnblogs.com)](https://www.cnblogs.com/cherishthepresent/p/16610652.html)
