# PX4FMU和PX4IO最底层启动过程分析（上）

## 主处理器和协处理器的固件烧写和运行过程
<div align=center><img src="https://jokerbin.oss-cn-beijing.aliyuncs.com/202209041251265.png" width="600"/></div>

PX4FMU：各种传感器数据读取、姿态解算、PWM控制量的计算、与PX4IO通信。负责飞控最主要的工作。  
PX4IO(STM32F103)：为PIXHAWK中专用于处理输入输出的部分,输入为支持的各类遥控器(PPM,SPKT/DSM,SBUS),输出为电调的PWM驱动信号,它与PX4FMU(STM32F427)通过串口进行通信。

<div align=center><img src="https://jokerbin.oss-cn-beijing.aliyuncs.com/202209041252974.jpg" height="400"/><img src="https://jokerbin.oss-cn-beijing.aliyuncs.com/202209041252162.jpg" height="400"/></div>


两个处理器固件烧写流程如下：
<div align=center><img src="https://jokerbin.oss-cn-beijing.aliyuncs.com/202209041254732.jpg" width="600"/></div>


主处理器和协处理器，固件下载到固件运行的流程图。值得说明的一点是分别给两个处理器下载blootloader以后，主处理器等待下载飞控固件，用地面站通过USB向飞控固件下载完以后，主处理器的飞控固件启动，这时候通过串口给协处理器下载固件(固件只是下载一次)。这个工作完成之后，主处理器和协处理器同时开始工作。

详细的这段代码在px4io.cpp中，随着px4io.cpp进程启动而启动。功能就是由主处理器向协处理器烧写代码。

其中协处理器的固件源代码在：E:\Firmware-master\Firmware\src\modules\px4iofirmware这里面主要是是一些，IO口的操作和安全开关的操作。他会在编译飞控固件的时候，一起被编译成为px4io-v2.bin，放在NUTTX文件系统中，在主处理器运行的时候把这个固件烧写进入协处理器。具体的烧写到单片机的FLASH哪个位置可以在px4io.cpp相关代码找到。

在软件层面的详细流程图如下：
<div align=center><img src="https://jokerbin.oss-cn-beijing.aliyuncs.com/202209041256506.png" width="600"/></div>

## BootLoader和NUTTX启动详解

在嵌入式操作系统中，BootLoader是在操作系统内核运行之前运行。可以初始化硬件设备、建立内存空间映射图，从而将系统的软硬件环境带到一个合适状态，以便为最终调用操作系统内核准备好正确的环境。在嵌入式系统中，通常并没有像BIOS那样的固件程序（注，有的嵌入式CPU也会内嵌一段短小的启动程序），因此整个系统的加载启动任务就完全由BootLoader来完成。Bootloader是嵌入式系统在加电后执行的第一段代码，在它完成CPU和相关硬件的初始化之后，再将操作系统映像或固化的嵌入式应用程序装在到内存中然后跳转到操作系统所在的空间，启动操作系统运行。

进入px4/Bootloader可以看到main_f1.c、main_f4.c，分别对应着PX4IO、PX4FMU的Bootloader。值得注意的是单片机的二进制代码在单片机的启动位置。

STM32芯片CortexM3的内核有三种启动方式，其分别是：  
A.	通过boot引脚设置可以将中断向量表定位于SRAM区，即起始地址为0x2000000，同时复位后PC指针位于0x2000000处；  
B.	通过boot引脚设置可以将中断向量表定位于FLASH区，即起始地址为0x8000000，同时复位后PC指针位于0x8000000处；  
C.	通过boot引脚设置可以将中断向量表定位于内置Bootloader区。

M3单片机复位后，从0x00000000取栈指针（SP），从0x00000004取复位向量（PC），有了栈指针和复位向量后，单片机就按照正常流程运行了，单片机启动默认先运行BootLoader，所以默认的中断向量表位置是BootLoader的中断向量表。我们知道Bootloader是上电执行的第一个代码，Pixhawk的启动是通过B的方式来启动的，所以我们在下Bootloader的时候也是向FLASH这0x8000000地址烧写的固件(具体可以看固件烧写这篇文章)，Bootloader烧写成功以后每次上电就会启动，启动的起始地址就是FLASH的0x8000000。Bootloader启动执行到最后，会用汇编语句，自动跳转到FLASH中固件下载的位置，开始执行固件代码。

NUTTX启动(也就是上面的固件代码，汇编语句就是跳转到这个位置来执行stm32_start.c）:  
代码位置：Firmware/build_px4fmu-v2_default/px4fmu-v2/Nuttx/nuttx/arch/arm/src/stm32/stm32_start.c

**__start负责STM32芯片的底层初始化,包括时钟,FPU,GPIO等单元的初始化。**  
**os_start负责OS的底层初始化,包括各种队列和进程结构的初始化。**  
**os_bringup负责OS基本进程的启动以及用户进程的启动,用户启动入口由(CONFIG_USER_ENTRYPOINT)宏进行指定是执行nsh_main还是user_start。**

<div align=center><img src="https://jokerbin.oss-cn-beijing.aliyuncs.com/202209041257718.png" width="1000"/></div>

