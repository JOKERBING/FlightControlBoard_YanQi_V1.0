# 使用VMware虚拟机安装Ubuntu系统并搭建PX4开发环境

本教程使用VMware虚拟机安装Ubuntu18.04系统（官方推荐使用版本），搭建PX4固件版本为v1.13.0，飞控板为pixhawk2.4.8版本。

## 1.VMware安装

 VMware是一款运行在windows系统上的虚拟机软件，可以虚拟出一台计算机硬件，方便安装各类操作系统。如Windows、macos、linux等。

软件可以从官网下载，也可以从各个CSDN博主提供的链接下载。

VMware官网：https://www.vmware.com/cn.html

<div align=center><img src="https://jokerbin.oss-cn-beijing.aliyuncs.com/202210192333181.png" width="700"></div>

点击【资源】。

<div align=center><img src="https://jokerbin.oss-cn-beijing.aliyuncs.com/202210192334296.png" width="700"></div>

点击【产品下载】。

<div align=center><img src="https://jokerbin.oss-cn-beijing.aliyuncs.com/202210192334149.png" width="700"></div>

找到【VMware Workstation Pro】点击【Download Product】。

<div align=center><img src="https://jokerbin.oss-cn-beijing.aliyuncs.com/202210192336378.png" width="700"></div>

这里可以下载自己需要的版本。

****

下载之后首先运行VMware的exe安装程序。

<div align=center><img src="https://jokerbin.oss-cn-beijing.aliyuncs.com/202211082045551.png" width="500"></div>

点击【下一步】。

<div align=center><img src="https://jokerbin.oss-cn-beijing.aliyuncs.com/202211082046681.png" width="500"></div>

点击【下一步】。

<div align=center><img src="https://jokerbin.oss-cn-beijing.aliyuncs.com/202211082047332.png" width="500"></div>

选择软件安装位置，点击【下一步】。

<div align=center><img src="https://jokerbin.oss-cn-beijing.aliyuncs.com/202211082048033.png" width="500"></div>

点击【下一步】。

<div align=center><img src="https://jokerbin.oss-cn-beijing.aliyuncs.com/202211082049276.png" width="500"></div>

点击【下一步】。

<div align=center><img src="https://jokerbin.oss-cn-beijing.aliyuncs.com/202211082049989.png" width="500"></div>

点击【安装】后等待即可。

<div align=center><img src="https://jokerbin.oss-cn-beijing.aliyuncs.com/202211082050793.png" width="500"></div>

点击【许可证】。

<div align=center><img src="https://jokerbin.oss-cn-beijing.aliyuncs.com/202211082050954.png" width="500"></div>

输入秘钥即可使用。

<div align=center><img src="https://jokerbin.oss-cn-beijing.aliyuncs.com/202211082050932.png" width="500"></div>

到此即可完成VMware的安装。

## 2.Ubuntu系统镜像下载

官网下载：https://cn.ubuntu.com/

国内镜像下载：https://mirrors.tuna.tsinghua.edu.cn/

这里选择国内镜像【清华大学开源软件镜像站】进行下载。

<div align=center><img src="https://jokerbin.oss-cn-beijing.aliyuncs.com/202210181920826.png" width="700"></div>

在搜索框内输入ubuntu，选择ubuntu-releases。

<div align=center><img src="https://jokerbin.oss-cn-beijing.aliyuncs.com/202210181920159.png" width="700"></div>

选择18.04/。

<div align=center><img src="https://jokerbin.oss-cn-beijing.aliyuncs.com/202210181920822.png" width="700"></div>

选择ubuntu-18.04.6-desktop-amd64.iso下载。

<div align=center><img src="https://jokerbin.oss-cn-beijing.aliyuncs.com/202210181920506.png" width="700"></div>

## 3.使用VMware虚拟机安装Ubuntu系统

运行vmware，单击【创建新的虚拟机】。


<div align=center><img src="https://jokerbin.oss-cn-beijing.aliyuncs.com/202210111757247.png" width="700"></div>

在弹出的向导中选择【典型】，点击【下一步】。

<div align=center><img src="https://jokerbin.oss-cn-beijing.aliyuncs.com/202210181945155.png" width="500"></div>

选择下载好的镜像文件，点击【下一步】。

<div align=center><img src="https://jokerbin.oss-cn-beijing.aliyuncs.com/202210181945878.png" width="500"></div>

按要求设置用户名和密码，点击【下一步】。

<div align=center><img src="https://jokerbin.oss-cn-beijing.aliyuncs.com/202210181946092.png" width="500"></div>

这里可以设置虚拟机的名称和虚拟机文件的存储位置，存储位置建议在C盘以外新建一个文件夹，点击【下一步】。

<div align=center><img src="https://jokerbin.oss-cn-beijing.aliyuncs.com/202210181949682.png" width="500"></div>

设置虚拟机的磁盘容量，按需设置，建议不少于40G，点击【下一步】。

<div align=center><img src="https://jokerbin.oss-cn-beijing.aliyuncs.com/202210181950775.png" width="500"></div>

这里可以点击【自定义硬件】进行进一步配置。

<div align=center><img src="https://jokerbin.oss-cn-beijing.aliyuncs.com/202210181951687.png" width="500"></div>

这里设置内存和处理器内核数量，建议内存不小于4096MB，处理器内核数量不小于4，点击【关闭】。

<div align=center><img src="https://jokerbin.oss-cn-beijing.aliyuncs.com/202210181952671.png" width="500"></div>

点击【完成】即可。

<div align=center><img src="https://jokerbin.oss-cn-beijing.aliyuncs.com/202210181954066.png" width="500"></div>

等待片刻即可进入Ubuntu系统安装程序，这时耐心等待安装。

<div align=center><img src="https://jokerbin.oss-cn-beijing.aliyuncs.com/202210181958567.png" width="700"></div>

安装完成后点击头像，输入密码即可进入系统。

## 4.搭建PX4开发环境

在Ubuntu系统中，使用CTRL+ALT+T快捷键进入命令行终端Terminal。

<div align=center><img src="https://jokerbin.oss-cn-beijing.aliyuncs.com/202210182017441.png" width="700"></div>

输入以下命令将用户添加至组dialout。创建独立用户的目的是让开发环境分离开来，避免出现不同用户间版本冲突等情况。将用户加入dialout用户组的目的是dialout拥有对串口tty的操作权限。

```
sudo usermod -a -G dialout $USER
```

命令执行完后点击右上角关机图标，将已登录用户LogOut，重新登录。

<div align=center><img src="https://jokerbin.oss-cn-beijing.aliyuncs.com/202210182024632.png" width="400"></div>

### 4.1 使用脚本安装

PX4官方提供了一些安装的脚本，我们直接运行就可以安装。如果你是新手，不推荐使用脚本，手动安装有助于你了解编译环境的构成。

[PX4使用者手册：https://docs.px4.io/main/zh/](https://docs.px4.io/main/zh/)

打开官网用户手册，选择【开发】-【入门指南】-【工具链安装】-【Ubuntu设置】，其中有详细的教程。

<div align=center><img src="https://jokerbin.oss-cn-beijing.aliyuncs.com/202210182228296.png" width="900"></div>

下载官网上的ubuntu.sh和requirements.txt文件，存放在Ubuntu系统中，使用命令运行该脚本进行安装。

```
wget https://raw.githubusercontent.com/PX4/PX4-Autopilot/main/Tools/setup/ubuntu.sh
wget https://raw.githubusercontent.com/PX4/PX4-Autopilot/main/Tools/setup/requirements.txt
bash ubuntu.sh
```

等待脚本运行完即可。

### 4.2 手动安装


我们需要去github上下载PX4的源码，因此我们需要装一个git。

```
sudo apt install git
```

需要安装通用的盘python包管理工具pip。

```
sudo apt-get install python-pip
```

PX4的编译环境需要调用Cmakelist，因此我们要装一个cmake。

```
sudo apt-get install cmake
```

新建文件夹，文件夹名字可以任意取。

```
mkdir -p px4src_v1.13.0
```

之后我们使用`ls`、`cd`命令进入这个文件夹

```
cd px4src_v1.13.0/
```

从官网下载最新版本源码固件。

```
git clone https://github.com/PX4/Firmware.git
```

如果需要下载旧版本固件，请使用以下命令。

```
git clone -b v1.13.0 https://github.com/PX4/Firmware.git
```

clone成功之后，你会发现文件夹路径下面多了一个文件夹Firmware，我们使用`ls`、`cd`命令进入Firmware文件夹中。

```
cd Firmware/
```

Firmware里面就是PX4的源码，但是它依赖了很多其他的库，所以此时不完整还不能用，我们需要更新他的依赖。

```
git submodule update --init --recursive
```

在Ubuntu中开发基于ARM架构的STM32芯片，需要安装交叉编译器gcc-arm-none-eabi。

```
sudo apt-get install gcc-arm-none-eabi
```

需要安装常用的python依赖包jinja2、empy、yaml、numpy、toml、pyserial、catkin_pkg、future、kconfigllib、packaging、jsonschema。

```
sudo apt-get install python-jinja2
sudo apt-get install python-empy
sudo apt-get install python-yaml
sudo -H pip install numpy toml
sudo -H pip install pyserial
sudo -H pip install catkin_pkg
sudo -H pip install future
pip3 install kconfiglib
pip3 install --user packaging
pip3 install --user jsonschema
```

完成以上命令后，在Firmware文件夹中输入命令即可对固件进行编译。

```
make px4_fmu-v2_default
```

等待进度到100%即可完成固件编译。

<div align=center><img src="https://jokerbin.oss-cn-beijing.aliyuncs.com/202210182123853.png" width="500"></div>

将编译好的固件下载到无人机，需要输入命令。

```
make px4_fmu-v2_default upload
```

这里运行时会有报错。

```
WARNING: You should uninstall ModemManager as it conflicts with any non-modem serial device (like Pixhawk)
```

<div align=center><img src="https://jokerbin.oss-cn-beijing.aliyuncs.com/202210182129637.png" width="500"></div>

Ubuntu配备了一系列代理管理，这会严重干扰相关的串口（或usb串口），最明显的表现就是硬件连接到PC机后，无法读出硬件，无法烧录上传固件。所以需要卸载modemmanager模式管理器。

```
sudo apt-get remove modemmanager
```

在进行上述命令时可能会报下列两个错误。

<div align=center><img src="https://jokerbin.oss-cn-beijing.aliyuncs.com/202210182145197.png" width="500"></div>

<div align=center><img src="https://jokerbin.oss-cn-beijing.aliyuncs.com/202210182149988.png" width="500"></div>

可以通过执行下列命令来解除锁定，再执行卸载命令。

```
sudo rm /var/lib/dpkg/lock-frontend
sudo rm /var/lib/dpkg/lock
```

再次运行烧录程序，发现没有报错了。

<div align=center><img src="https://jokerbin.oss-cn-beijing.aliyuncs.com/202210182154161.png" width="500"></div>

这里提示等待连接无人机的数据线。

<div align=center><img src="https://jokerbin.oss-cn-beijing.aliyuncs.com/202210182157717.png" width="500"></div>

将USB插入飞控板之后等待进度条读完即可完成烧录固件。

到这里已经完成PX4基础开发环境的搭建了，如果需要 Gazebo 9 和 jMAVSim 功能，请在Firmware文件夹中执行以下代码。

```
bash ./Tools/setup/ubuntu.sh
```

可以通过添加`--no-nuttx` 或 `--no-sim-tools` 代码跳过 NuttX 或 simulation 的安装。

## 5.搭建jMAVSim仿真环境

在运行ubuntu.sh命令后（这里可能需要多次运行），最后一行显示以下提示，多次运行还是显示以下提示，确认不是网络的问题。

```
0 upgraded, 0 newly installed, 0 to remove and 119 not upgraded.
```

<div align=center><img src="https://jokerbin.oss-cn-beijing.aliyuncs.com/202210201624080.png" width="500"></div>

这时如果运行jMAVSim仿真命令，程序会在输出一堆提示后再输出这两行提示，然后在这里卡住没有反应了。

```
jokerbin@ubuntu:~/px4src_v1.9.2/Firmware$ sudo make px4_sitl jmavsim
[  1%] Built target logs_symlink
[  1%] Built target git_jmavsim
...
INFO  [dataman] Unknown restart, data manager file './dataman' size is 11798680 bytes
INFO  [simulator] Waiting for simulator to connect on TCP port 4560
```

调查原因是因为软件包没升级，调用以下命令来进行升级。

```
sudo apt-get dist-upgrade
```

之后再次运行命令。

```
bash ./Tools/setup/ubuntu.sh --fix-missing
```

最后一行显示以下提示，说明环境安装成功。

```
0 upgraded, 0 newly installed, 0 to remove and 0 not upgraded.
```

这时理论上已经可以通过运行以下命令进行仿真了，但实际操作中会报错。

```
sudo make px4_sitl jmavsim
```


> 接下来继续运行jMAVSim仿真命令，会出现一系列错误，这里提前说一下原因，是因为要安装的一个java开发工具包jdk8，一定要选8版本，（测试过11 、13 、10 都不行，会报错）。
>
> 可以直接跳过此部分进行之后的操作，但还是记录一下遇到的错误和解决方法。
>
> 第一次运行时报错。
>
> ```
> java.lang.UnsatisfiedLinkError: Can't load library /usr/lib/jvm/java-11-openjdk-amd64/lib/libawt_xawt.so
> ```
>
> <div align=center><img src="https://jokerbin.oss-cn-beijing.aliyuncs.com/202210201640122.png" width="500"></div>
>
> 解决方法是在网上下载libawt_xawt.so文件并添加到/usr/lib/jvm/java-11-openjdk-amd64/lib文件夹中。
>
> 第二次运行时报错。
>
> ```
> java.lang.UnsatisfiedLinkError: /tmp/jogamp_0000/file_cache/jln10188095814152762077/jln17241601840665512207/libnativewindow_awt.so: libjawt.so: cannot open shared object file: No such file or directory
> ```
>
> <div align=center><img src="https://jokerbin.oss-cn-beijing.aliyuncs.com/202210201641832.png" width="500"></div>
>
> 解决方法是在网上下载libjawt.so文件并添加到/usr/lib/jvm/java-11-openjdk-amd64/lib文件夹中。
>
> 解决完这两个问题之后再次运行还是报错。
>
> ```
> Inconsistency detected by ld.so: dl-lookup.c: 111: check_match: Assertion `version->filename == NULL || ! _dl_name_match_p (version->filename, map)' failed!
> ```
>
> <div align=center><img src="https://jokerbin.oss-cn-beijing.aliyuncs.com/202210201654772.png" width="500"></div>
>
> 查询原因就是java开发工具包jdk的版本问题。

使用命令安装openjdk-8-jdk软件包。

```
sudo apt install openjdk-8-jdk
```

安装完成之后需要用命令切换jdk软件包版本。

```
sudo update-alternatives --config java
```

输入java-8-openjdk-amd64对应的数字后按回车键即可切换版本。

使用以下命令查看jdk版本。

```
java -version
```

之后需要删除一个文件。

```
sudo rm -rf Tools/jMAVSim/out
```

<div align=center><img src="https://jokerbin.oss-cn-beijing.aliyuncs.com/202210201702268.png" width="500"></div>

完成版本切换之后再次运行jMAVSim仿真命令，出现错误提示。

```
Exception in thread "main" java.lang.reflect.InvocationTargetException
```

解决方法是编辑文件/etc/java-8-openjdk/accessibility.properties，在行首添加 # 注释掉assistive_technologies=org.GNOME.Acessibility.AtkWrapper这一行。

```
sudo gedit /etc/java-8-openjdk/accessibility.properties
```

<div align=center><img src="https://jokerbin.oss-cn-beijing.aliyuncs.com/202210201708814.png" width="500"></div>

再次运行jMAVSim仿真命令，即可出现仿真界面并进入pxh终端。

<div align=center><img src="https://jokerbin.oss-cn-beijing.aliyuncs.com/202210201711393.png" width="800"></div>

在pxh终端中输入命令使飞机起飞，如果起飞成功说明仿真环境运行成功。

```
commander takeoff
```

在输入起飞指令后，如果出现Failsafe enabled: no datalink无法起飞，可以输入以下指令。

```
param set NAV_RCL_ACT 0
param set NAV_DLL_ACT 0
```

之后再次运行起飞指令即可。

<div align=center><img src="https://jokerbin.oss-cn-beijing.aliyuncs.com/202210201712910.png" width="800"></div>

## 6.搭建ROS和Gazebo仿真环境

理论上运行完ubuntu.sh命令文件后就可以进行Gazebo仿真，使用以下命令进行仿真。

```
sudo make px4_sitl gazebo
```

但是实际运行中可能会报错。

```
CMake Error at CMakeLists.txt:34 (find_package):
  By not providing "Findgazebo.cmake" in CMAKE_MODULE_PATH this project has
  asked CMake to find a package configuration file provided by "gazebo", but
  CMake did not find one.

  Could not find a package configuration file provided by "gazebo" with any
  of the following names:

    gazeboConfig.cmake
    gazebo-config.cmake

  Add the installation prefix of "gazebo" to CMAKE_PREFIX_PATH or set
  "gazebo_DIR" to a directory containing one of the above files.  If "gazebo"
  provides a separate development package or SDK, be sure it has been
  installed.
```

<div align=center><img src="https://jokerbin.oss-cn-beijing.aliyuncs.com/202210212336516.png" width="500"></div>

解决方法是运行以下命令。

```
sudo apt-get install libgazebo9-dev
```

再次运行还是报错。

```
CMake Error at CMakeLists.txt:38 (find_package):
  By not providing "FindOpenCV.cmake" in CMAKE_MODULE_PATH this project has
  asked CMake to find a package configuration file provided by "OpenCV", but
  CMake did not find one.

  Could not find a package configuration file provided by "OpenCV" with any
  of the following names:

    OpenCVConfig.cmake
    opencv-config.cmake

  Add the installation prefix of "OpenCV" to CMAKE_PREFIX_PATH or set
  "OpenCV_DIR" to a directory containing one of the above files.  If "OpenCV"
  provides a separate development package or SDK, be sure it has been
  installed.
```

<div align=center><img src="https://jokerbin.oss-cn-beijing.aliyuncs.com/202210220032592.png" width="500"></div>

这里原因是没有安装opencv导致的，本来想进行非ROS下的Gazebo仿真，但在网上搜索说是容易出各种奇怪的bug，遂进行配置ROS下的Gazebo仿真。

首先下载官方给的安装脚本文件。

```
wget https://raw.githubusercontent.com/PX4/Devguide/master/build_scripts/ubuntu_sim_ros_melodic.sh
```

之后运行。

```
bash ubuntu_sim_ros_melodic.sh
```

<div align=center><img src="https://jokerbin.oss-cn-beijing.aliyuncs.com/202210220157075.png" width="500"></div>

之后是安装mavros。

```
sudo apt install ros-melodic-mavros ros-melodic-mavros-extras
```

再次运行Gazebo仿真命令，报错。

```
[Err] [ModelDatabase.cc:235] No <database> tag in the model database database.config found here[http://gazebosim.org/models/]
[Err] [ModelDatabase.cc:294] Unable to download model manifests
```

<div align=center><img src="https://jokerbin.oss-cn-beijing.aliyuncs.com/202210220211255.png" width="500"></div>

这是因为http://gazebosim.org/models/地址已经变换http://models.gazebosim.org，因此无法下载所需模型。

解决方法是修改两个配置文件中的网址。

```
sudo gedit /usr/share/gazebo/setup.sh
sudo gedit /usr/share/gazebo-9/setup.sh
```

在打开的文件中注释掉旧网址添加新网址。

```
#export GAZEBO_MODEL_DATABASE_URI=http://gazebosim.org/models
export GAZEBO_MODEL_DATABASE_URI=http://models.gazebosim.org/
```

<div align=center><img src="https://jokerbin.oss-cn-beijing.aliyuncs.com/202210220217930.png" width="500"></div>

保存后关闭，再次运行还是报错，但不影响使用，这个报错很玄学。

再次运行Gazebo仿真命令会报错。

```
[Err] [REST.cc:205] Error in REST request
```

<div align=center><img src="https://jokerbin.oss-cn-beijing.aliyuncs.com/202210220944078.png" width="500"></div>

解决方法是修改配置文件中的网址。

```
sudo gedit ~/.ignition/fuel/config.yaml
```

在打开的文件中注释掉旧网址添加新网址。

```
url: https://api.ignitionrobotics.org  
# url: https://api.ignitionfuel.org
```

<div align=center><img src="https://jokerbin.oss-cn-beijing.aliyuncs.com/202210220254858.png" width="500"></div>

保存后关闭。

再次运行Gazebo仿真命令会报错。

```
VMware: vmw_ioctl_command error Invalid argument.
```

解决方案是关闭虚拟机设置中的【显示器】-【加速3D图形】功能，但是这样会损失部分性能，建议运行以下命令。

```
echo "export SVGA_VGPU10=0" >> ~/.bashrc
echo "export SVGA_VGPU10=0" >> ~/.profile
```

最后再次运行Gazebo仿真命令，出现仿真界面，说明配置成功。

<div align=center><img src="https://jokerbin.oss-cn-beijing.aliyuncs.com/202210220952195.png" width="700"></div>

如果不需要ROS控制，只是仿真的话，已经满足要求了。但是如果需要使用ROS连接，还需要将Gazebo升级，删除默认的Gazebo版本，默认安装Gazebo9.0，可通过以下命令卸载。

```
sudo apt-get remove libgazebo*
sudo apt-get remove gazebo*
```

设置计算机以接受来自package.osrfoundation.org的软件。

```
sudo sh -c 'echo "deb http://packages.osrfoundation.org/gazebo/ubuntu-stable `lsb_release -cs` main" /etc/apt/sources.list.d/gazebo-stable.list'
```

设置密钥。

```
wget http://packages.osrfoundation.org/gazebo.key -O - | sudo apt-key add -
```

更新源并安装gazebo9.1版本。

```
sudo apt-get update
sudo apt-get install gazebo9=9.1*
sudo apt-get upgrade
```

之后需要设置环境变量，让ROS可以识别这些功能包。

```
gedit ~/.bashrc
```

在最后加入这四行，注意这里的jokerbin是我的用户名，px4src_v1.9.2是我起的源码文件夹名，需要更换成自己的路径。

```
source /home/jokerbin/px4src_v1.9.2/Firmware/Tools/setup_gazebo.bash /home/jokerbin/px4src_v1.9.2/Firmware /home/jokerbin/px4src_v1.9.2/Firmware/build/px4_sitl_default
export ROS_PACKAGE_PATH=$ROS_PACKAGE_PATH:/home/jokerbin/px4src_v1.9.2/Firmware
export ROS_PACKAGE_PATH=$ROS_PACKAGE_PATH:/home/jokerbin/px4src_v1.9.2/Firmware/Tools/sitl_gazebo
export GAZEBO_MODEL_DATABASE_URI=""
```

关闭终端，再次打开终端，形成下图样式即可。

<div align=center><img src="https://jokerbin.oss-cn-beijing.aliyuncs.com/202210221005462.png" width="500"></div>

这时运行Gazebo仿真命令可能会报错。

<div align=center><img src="https://jokerbin.oss-cn-beijing.aliyuncs.com/202210260239511.png" width="500"></div>

根据提示，需要进入/Firmware/Tools/sitl_gazebo/include文件夹下的gazebo_opticalflow_plugin.h文件，将第43行的`TRUE`改为`1`。

```
//#define HAS_GYRO TRUE
#define HAS_GYRO 1
```

之后可以使用命令进行单机的仿真或集群的仿真。

```
roslaunch px4 posix_sitl.launch
```

```
roslaunch px4 multi_uav_mavros_sitl.launch
```

在运行上述两个命令时可能会报错。

```
ERROR [px4] Failed to communicate with daemon: Permission denied
```

<div align=center><img src="https://jokerbin.oss-cn-beijing.aliyuncs.com/202210241515095.png" width="500"></div>

该问题定位到/tmp下的px4_lock-0和px4-sock-0上。其中px4_lock-0是一个单纯的锁文件，和操作系统中的锁是一个概念，本身不具有任何信息；px4-sock-0是一个socket文件，主要是通信作用。报错是权限拒绝，说明我们的用户无法占有这两个文件，而我们又无法使用sudo执行roslaunch命令，使用root用户又存在很大的风险，因此非常简单的一个方法就是将这两个文件易主。

```
cd /tmp
sudo chmod 777 px4_lock-0
sudo chmod 777 px4-sock-0
sudo chown username px4_lock-0	#这里username换成自己的用户名！
sudo chown username px4-sock-0	#这里username换成自己的用户名！
```

可以通过运行以下命令来判断mavros与SITL是否通信成功，若connected: True则通信成功。

```
rostopic echo /mavros/state
```

## 7.安装QGroundControl地面站

官网教程：[Download and Install · QGroundControl User Guide](https://docs.qgroundcontrol.com/master/en/getting_started/download_and_install.html)

在安装地面站之前，首先在终端运行以下命令。

```
sudo usermod -a -G dialout $USER
sudo apt-get remove modemmanager -y
sudo apt install gstreamer1.0-plugins-bad gstreamer1.0-libav gstreamer1.0-gl -y
sudo apt install libqt5gui5 -y
sudo apt install libfuse2 -y
```

命令执行完后点击右上角关机图标，将已登录用户LogOut，重新登录。

<div align=center><img src="https://jokerbin.oss-cn-beijing.aliyuncs.com/202210182024632.png" width="400"></div>

下载QGC地面站安装包。

```
wget https://s3-us-west-2.amazonaws.com/qgroundcontrol/latest/QGroundControl.AppImage
```

安装。

```
chmod +x ./QGroundControl.AppImage
```

安装之后可以使用命令运行或者双击QGroundControl.AppImag图标运行。

```
./QGroundControl.AppImage
```

<div align=center><img src="https://jokerbin.oss-cn-beijing.aliyuncs.com/202210212222466.png" width="700"></div>

显示QGC界面说明安装成功。

## 8.安装Qt Creator

更新源。

```
sudo apt-get update
```

安装Qt Creator。

```
sudo apt-get install qtcreator
```

<div align=center><img src="https://jokerbin.oss-cn-beijing.aliyuncs.com/202210212250791.png" width="700"></div>

在应用内找打图标运行即可。

<div align=center><img src="https://jokerbin.oss-cn-beijing.aliyuncs.com/202210212251710.png" width="700"></div>







****

参考资料：

[Ubuntu系统的下载与安装（超详细）_fr16021028的博客-CSDN博客_ubuntu下载](https://blog.csdn.net/fr16021028/article/details/125888815)

[PX4二次开发环境搭建及报错解决（v1.9.2）_小仙女要努力的博客-CSDN博客_px4二次开发环境](https://blog.csdn.net/qq_36692645/article/details/100088809)

[PX4原生固件jMAVSim仿真时没有仿真界面_沣爸爸的博客-CSDN博客](https://blog.csdn.net/helloworldsyf/article/details/104245198)

[PX4_jmavsim仿真之问题解决篇_Chasing中的小强的博客-CSDN博客](https://blog.csdn.net/qq_15390133/article/details/116406039)

[PX4开发环境搭建（Ubuntu1804+QGC+Qt Creator ）_Beyonderwei的博客-CSDN博客_px4环境](https://blog.csdn.net/CSDN_X_W/article/details/105142281)

[gazebo常见问题（1）_weixin_46078434的博客-CSDN博客](https://blog.csdn.net/weixin_46078434/article/details/126199599)

[搭建无人机仿真环境之PX4安装_Djarea的博客-CSDN博客](https://blog.csdn.net/wangdongjiab/article/details/107230585)

[ROS+Gazebo+PX4仿真步骤_chen果冻的博客-CSDN博客](https://blog.csdn.net/qq_19469271/article/details/119963938)

[gazebo学习时遇到的问题（PX4篇）_GKSama的博客-CSDN博客](https://blog.csdn.net/qq_41301435/article/details/111240834)

[使用PX4 模拟无人机起降 jmavsim或Gazebo环境下_Diedider的博客-CSDN博客](https://blog.csdn.net/weixin_46808875/article/details/123682582)

[搭建无人机仿真环境之PX4安装中出现的一些问题的解决_TYINY的博客-CSDN博客](https://blog.csdn.net/sinat_16643223/article/details/113075944)
