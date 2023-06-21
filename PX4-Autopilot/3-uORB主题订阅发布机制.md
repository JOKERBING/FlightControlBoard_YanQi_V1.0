# uORB主题订阅发布机制

## 1.PX4/Pixhawk的软件体系结构

PX4/Pixhawk的软件体系结构主要被分为四个层次，这可以让我们更好的理解PX4/Pixhawk的软件架构和运作：

- 应用程序的API：这个接口提供给应用程序开发人员，此API旨在尽可能的精简、扁平及隐藏其复杂性。
- 应用程序框架: 这是为操作基础飞行控制的默认程序集（节点）。
- 库: 这一层包含了所有的系统库和基本交通控制的函数。
- 操作系统: 最后一层提供硬件驱动程序，网络，UAVCAN和故障安全系统。

<div align=center><img src="https://jokerbin.oss-cn-beijing.aliyuncs.com/202302031623086.png" width="800"></div>


uORB(Micro Object Request Broker，微对象请求代理器)是PX4/Pixhawk系统中非常重要且关键的一个模块，它肩负了整个系统的数据传输任务，所有的传感器数据、GPS、PPM信号等都要从芯片获取后通过uORB进行传输到各个模块进行计算处理。实际上uORB是一套跨「进程」 的IPC通讯模块。在Pixhawk中， 所有的功能被独立以进程模块为单位进行实现并工作。而进程间的数据交互就由为重要，必须要能够符合实时、有序的特点。

Pixhawk使用的是NuttX实时ARM系统，uORB实际上是多个进程打开同一个设备文件，进程间通过此文件节点进行数据交互和共享。进程通过命名的「总线」交换的消息称之为「主题」(topic)，在Pixhawk 中，一个主题仅包含一种消息类型，通俗点就是数据类型。每个进程可以「订阅」或者「发布」主题，可以存在多个发布者，或者一个进程可以订阅多个主题，但是一条总线上始终只有一条消息。



## 2.PX4/Pixhawk应用程序框架

<div align=center><img src="https://jokerbin.oss-cn-beijing.aliyuncs.com/202302031622971.png" width="800"></div>

应用层中操作基础飞行的应用之间都是隔离的，这样提供了一种安保模式，以确保基础操作独立的高级别系统状态的稳定性。而沟通它们的就是uORB。

<div align=center><img src="https://jokerbin.oss-cn-beijing.aliyuncs.com/202302031622393.png" width="800"></div>



## 3.uORB文件夹说明

<div align=center><img src="https://jokerbin.oss-cn-beijing.aliyuncs.com/202301310952020.png" width="800"></div>

**uORB.cpp : uORB的实现**  
**uORB.h : uORB头文件**  



## 4.常用函数功能解析

> **发布主题  先公告，再发布**

**`orb_advert_t orb_advertise(const struct orb_metadata *meta, const void *data)`**

```
功能：公告发布者的主题；
说明：在发布主题之前是必须的；否则订阅者虽然能订阅，但是得不到数据；
参数：
    meta:uORB元对象，可以认为是主题id，一般是通过ORB_ID(主题名)来赋值;
    data:指向一个已被初始化，发布者要发布的数据存储变量的指针；
返回值：
	错误则返回ERROR;成功则返回一个可以发布主题的句柄；如果待发布的主题没有定义或声明则会返回-1，然后会将errno赋值为ENOENT;
eg:
    struct vehicle_attitude_s att;
    memset(&att, 0, sizeof(att));
    int att_pub_fd = orb_advertise(ORB_ID(vehicle_attitude), &att);
```

**`int orb_publish(const struct orb_metadata *meta, orb_advert_t handle, const void *data)`**

```
功能：发布新数据到主题；
参数：
    meta:uORB元对象，可以认为是主题id，一般是通过ORB_ID(主题名)来赋值;
    handle:orb_advertise函数返回的句柄；
    data:指向待发布数据的指针；
返回值：
	OK表示成功；错误返回ERROR；否则则有根据的去设置errno;
eg: 
    orb_publish(ORB_ID(vehicle_attitude), att_pub_fd, &att);
```

> **订阅主题  先关注，检查更新，再拷贝**

**`int orb_subscribe(const struct orb_metadata *meta)`**

```
功能：订阅主题（topic）;
说明：即使订阅的主题没有被公告，但是也能订阅成功；但是在这种情况下，却得不到数据，直到主题被公告；
参数：
    meta:uORB元对象，可以认为是主题id，一般是通过ORB_ID(主题名)来赋值；
返回值：
    错误则返回ERROR;成功则返回一个可以读取数据、更新话题的句柄；如果待订阅的主题没有定义或声明则会返回-1，然后会将errno赋值为ENOENT;
eg:
    int fd = orb_subscribe(ORB_ID(topicName));
```

**`int poll(struct pollfd fds[], nfds_t nfds, int timeout)`**

```
功能：监控文件描述符（多个）；
说明：timemout=0，poll()函数立即返回而不阻塞；timeout=INFTIM(-1)，poll()会一直阻塞下去，直到检测到return > 0；
参数：
    fds:struct pollfd结构类型的数组；
    nfds:用于标记数组fds中的结构体元素的总数量；
    timeout:是poll函数调用阻塞的时间，单位：毫秒；
返回值：
    >0:数组fds中准备好读、写或出错状态的那些socket描述符的总数量；
    ==0:poll()函数会阻塞timeout所指定的毫秒时间长度之后返回;
    -1:poll函数调用失败；同时会自动设置全局变量errno；
```

**`int orb_check(int handle, bool *updated)`**

```
功能：订阅者可以用来检查一个主题在发布者上一次更新数据后，有没有订阅者调用过ob_copy来接收、处理过；
说明：如果主题在在被公告前就有人订阅，那么这个API将返回“not-updated”直到主题被公告。可以不用poll，只用这个函数实现数据的获取。
参数：
    handle:主题句柄；
    updated:如果当最后一次更新的数据被获取了，检测到并设置updated为ture;
返回值：
    OK表示检测成功；错误返回ERROR;否则则有根据的去设置errno;
eg:
    if (PX4_OK != orb_check(sfd, &updated)) {
        return printf("check(1) failed");
    }
    if (updated) {
        return printf("spurious updated flag");
    }

    //or

    bool updated;
    struct random_integer_data rd;

    /* check to see whether the topic has updated since the last time we read it */
    orb_check(topic_handle, &updated);

    if (updated) {
        /* make a local copy of the updated data structure */
        orb_copy(ORB_ID(random_integer), topic_handle, &rd);
        printf("Random integer is now %d\n", rd.r);
    }
```

**`int orb_copy(const struct orb_metadata *meta, int handle, void *buffer)`**

```
功能：从订阅的主题中获取数据并将数据保存到buffer中；
参数：
    meta:uORB元对象，可以认为是主题id，一般是通过ORB_ID(主题名)来赋值;
    handle:订阅主题返回的句柄；
    buffer:从主题中获取的数据；
返回值：
    返回OK表示获取数据成功，错误返回ERROR;否则则有根据的去设置errno;
eg:
    struct sensor_combined_s raw;
    orb_copy(ORB_ID(sensor_combined), sensor_sub_fd, &raw);
```

> **其他**

**`int orb_set_interval(int handle, unsigned interval)`**

```
功能：设置订阅的最小时间间隔；
说明：如果设置了，则在这间隔内发布的数据将订阅不到；需要注意的是，设置后，第一次的数据订阅还是由起初设置的频率来获取，
参数：
    handle:orb_subscribe函数返回的句柄；
    interval:间隔时间，单位ms;
返回值：
	OK表示成功；错误返回ERROR；否则则有根据的去设置errno;
eg:
    orb_set_interval(sensor_sub_fd, 1000);
```

**`orb_advert_t orb_advertise_multi(const struct orb_metadata *meta, const void *data, int *instance, int priority)`**

```
功能：设备/驱动器的多个实例实现公告，利用此函数可以注册多个类似的驱动程序；
说明：例如在飞行器中有多个相同的传感器，那他们的数据类型则类似，不必要注册几个不同的话题；
参数：
    meta:uORB元对象，可以认为是主题id，一般是通过ORB_ID(主题名)来赋值;
    data:指向一个已被初始化，发布者要发布的数据存储变量的指针；
    instance:整型指针，指向实例的ID（从0开始）；
    priority:实例的优先级。如果用户订阅多个实例，优先级的设定可以使用户使用优先级高的最优数据源；
返回值：
    错误则返回ERROR;成功则返回一个可以发布主题的句柄；如果待发布的主题没有定义或声明则会返回-1，然后会将errno赋值为ENOENT;
eg:
    struct orb_test t;
    t.val = 0;
    int instance0;
    orb_advert_t pfd0 = orb_advertise_multi(ORB_ID(orb_multitest), &t, &instance0, ORB_PRIO_MAX);
```

**`int orb_subscribe_multi(const struct orb_metadata *meta, unsigned instance)`**

```
功能：订阅主题（topic）;
说明：通过实例的ID索引来确定是主题的哪个实例；
参数：
    meta:uORB元对象，可以认为是主题id，一般是通过ORB_ID(主题名)来赋值;
    instance:主题实例ID;实例ID=0与orb_subscribe()实现相同；
返回值：
    错误则返回ERROR;成功则返回一个可以读取数据、更新话题的句柄；如果待订阅的主题没有定义或声明则会返回-1，然后会将errno赋值为ENOENT;
eg:
    int sfd1 = orb_subscribe_multi(ORB_ID(orb_multitest), 1);
```

**`int orb_unsubscribe(int handle)`**

```
功能：取消订阅主题；
参数：
    handle:主题句柄；
返回值：
    OK表示成功；错误返回ERROR;否则则有根据的去设置errno;
eg:
    ret = orb_unsubscribe(handle);
```

**`int orb_stat(int handle, uint64_t *time)`**

```
功能：订阅者可以用来检查一个主题最后的发布时间；
参数：
    handle:主题句柄；
    time:存放主题最后发布的时间；0表示该主题没有发布或公告；
返回值：
    OK表示检测成功；错误返回ERROR;否则则有根据的去设置errno;
eg:
    ret = orb_stat(handle,time);
```

**`int orb_exists(const struct orb_metadata *meta, int instance)`**

```
功能：检测一个主题是否存在；
参数：
    meta:uORB元对象，可以认为是主题id，一般是通过ORB_ID(主题名)来赋值;
    instance:ORB 实例ID;
返回值：
    OK表示检测成功；错误返回ERROR;否则则有根据的去设置errno;
eg:
    ret = orb_exists(ORB_ID(vehicle_attitude),0);
```

**`int orb_priority(int handle, int *priority)`**

```
功能：获取主题优先级别；
参数：
    handle:主题句柄；
    priority:存放获取的优先级别；
返回值：
    OK表示检测成功；错误返回ERROR;否则则有根据的去设置errno;
eg:
    ret = orb_priority(handle,&priority);
```



## 5.例程实战

### 5.1 订阅和发布主题

这里的例程来自于Firmware1.13.0版本中的px4_simple_app.c文件，为了方便理解，进行了一定的改动，其位置位于Firmware/src/examples/px4_simple_app/px4_simple_app.c。

```c++
#include <px4_platform_common/px4_config.h>
#include <px4_platform_common/tasks.h>
#include <px4_platform_common/posix.h>
#include <unistd.h>
#include <stdio.h>
#include <poll.h>
#include <string.h>
#include <math.h>

#include <uORB/uORB.h>
#include <uORB/topics/vehicle_acceleration.h>
#include <uORB/topics/vehicle_attitude.h>

//主函数声明
__EXPORT int px4_simple_app_main(int argc, char *argv[]);
```

> 这里`__EXPORT`是一个宏定义，其定义在Firmware/src/modules/systemlib/visibility.h中  
>
> 此处`__EXPORT`的拓展内容参考教程：[预处理命令使用详解](预处理命令使用详解.md)
>
> ```c++
> #ifdef __EXPORT
> #  undef __EXPORT//这里#后面的空格语法上是正确的，但不推荐这样写
> #endif
> #define __EXPORT __attribute__((visibility("default")))
> 
> #ifdef __PRIVATE
> #  undef __PRIVATE//这里#后面的空格语法上是正确的，但不推荐这样写
> #endif
> #define __PRIVATE __attribute__((visibility("hidden")))
> ```
>
> `__attribute__()`函数用于设置动态链接库中函数的可见性，将变量或函数设置为default，则该符号可在其他动态链接库中可见；将变量或函数设置为hidden，则该符号仅在本动态链接库中可见，在其他库中不可见。
>
> 编写大型程序时默认隐藏，针对特定变量和函数，在代码中使用`__attribute__((visibility("default")))`令该符号外部可见，这种方法可有效避免动态链接库之间的符号冲突。  
>
> （近似于类中的访问权限:public, private, protected）

```c++
int px4_simple_app_main(int argc, char *argv[])
{
	//在终端打印Hello Sky!
    PX4_INFO("Hello Sky!\n");

    //订阅vehicle_acceleration主题
    int sensor_sub_fd = orb_subscribe(ORB_ID(vehicle_acceleration));
```

> 这里调用了两个函数。
>
> **`int orb_subscribe(const struct orb_metadata *meta)`**
>
> ```
> 功能：订阅主题（topic）;
> 说明：即使订阅的主题没有被公告，但是也能订阅成功；但是在这种情况下，却得不到数据，直到主题被公告；
> 参数：
> 	meta:uORB元对象，可以认为是主题id，一般是通过ORB_ID(主题名)来赋值；
> 返回值：
> 	错误则返回ERROR;成功则返回一个可以读取数据、更新话题的句柄；如果待订阅的主题没有定义或声明则会返回-1，然后会将errno赋值为ENOENT;
> eg:
> 	int fd = orb_subscribe(ORB_ID(topicName));
> ```
>
> **`ORB_ID(topicName)`**
>
> ```
> 功能：得到主题ID
> 参数：topicName主题的名字
> 返回值：主题ID
> ```
>
> **这里先由主题名字得到主题ID，再通过主题ID订阅vehicle_acceleration主题，返回值是这个主题的句柄。**
> 
> vehicle_acceleration主题中存放的是加速度计的数据。  

```c++
	//限制更新速率5Hz
	orb_set_interval(sensor_sub_fd, 200);
```

> **`int orb_set_interval(int handle, unsigned interval)`**
>
> ```
> 功能：设置订阅的最小时间间隔；
> 说明：如果设置了，则在这间隔内发布的数据将订阅不到；需要注意的是，设置后，第一次的数据订阅还是由起初设置的频率来获取，
> 参数：
> 	handle:orb_subscribe函数返回的句柄；
> 	interval:间隔时间，单位ms;
> 返回值：
> 	OK表示成功；错误返回ERROR；否则则有根据的去设置errno;
> eg:
> 	orb_set_interval(sensor_sub_fd, 1000);
> ```
>
> 这里设置了每200ms去订阅一次，看是否到来。

```c++
	//这里vehicle_attitude_s跟sensor_combined_s是一样的，是系统自动定义的结构体
	struct vehicle_attitude_s att;
	//这个函数的作用是把结构体att中的所有成员都赋值0
    memset(&att, 0, sizeof(att));
```

> vehicle_attitude主题中的数据在Firmware/msg/vehicle_attitude.msg文件中定义。系统自动生成的`vehicle_attitude_s`结构体类型中的成员就是下列中的各个变量。
> ```
> float32 rollspeed	# Angular velocity about body north axis (x) in rad/s
> float32 pitchspeed	# Angular velocity about body east axis (y) in rad/s
> float32 yawspeed	# Angular velocity about body down axis (z) in rad/s
> float32[4] q		# Quaternion (NED)
> ```

```c++
	//公告vehicle_attitude主题
	orb_advert_t att_pub = orb_advertise(ORB_ID(vehicle_attitude), &att);
```

> **`orb_advert_t orb_advertise(const struct orb_metadata *meta, const void *data)`**
>
> ```
> 功能：公告发布者的主题；
> 说明：在发布主题之前是必须的；否则订阅者虽然能订阅，但是得不到数据；
> 参数：
> 	meta:uORB元对象，可以认为是主题id，一般是通过ORB_ID(主题名)来赋值;
> 	data:指向一个已被初始化，发布者要发布的数据存储变量的指针；
> 返回值：
> 	错误则返回ERROR;成功则返回一个可以发布主题的句柄；如果待发布的主题没有定义或声明则会返回-1，然后会将errno赋值为ENOENT;
> eg:
> 	struct vehicle_attitude_s att;
> 	memset(&att, 0, sizeof(att));
> 	int att_pub_fd = orb_advertise(ORB_ID(vehicle_attitude), &att);
> ```

```c++
	/*一个应用可以等待多个主题，在这里只等待一个主题*/
    px4_pollfd_struct_t fds[] = 
    {
        { .fd = sensor_sub_fd,   .events = POLLIN },
        /* 这里可以添加更多的文件描述符；
         * { .fd = other_sub_fd,   .events = POLLIN },
         */
    };
```

> 这里`px4_pollfd_struct_t`是类型名，`fds`是结构体数组名，这里在Firmware/src/platforms/px4_posix.h文件中使用`typedef struct pollfd px4_pollfd_struct_t`语句将结构体类型名改成`px4_pollfd_struct_t`。  
>
> 这里找到了`px4_pollfd_struct_t`这个结构体的定义，里边存放的主要的三个变量分别为：`fd`，`events`，`revents`。`fd`用来存放文件描述符（可以读取数据、更新话题的句柄），`events`用来存放文件描述符fd上感兴趣的事件，`revents`用来存放文件描述符fd上实际发生的事件。
>
> ```
> typedef struct {
> 	/* This part of the struct is POSIX-like */
> 	int		fd;       /* The descriptor being polled */
> 	pollevent_t 	events;   /* The input event flags */
> 	pollevent_t 	revents;  /* The output event flags */
> 
> 	/* Required for PX4 compatibility */
> 	px4_sem_t   *sem;  	/* Pointer to semaphore used to post output event */
> 	void   *priv;     	/* For use by drivers */
> } px4_pollfd_struct_t;
> ```
>
> 经常检测的事件标记： POLLIN/POLLRDNORM(可读)、POLLOUT/POLLWRNORM(可写)、POLLERR(出错)
>
> 如果是对一个描述符上的多个事件感兴趣的话，可以把这些常量标记之间进行按位或运算就可以了，如：`.events=POLLIN | POLLOUT | POLLERR；`

```c++
    int error_counter = 0;

    for (int i = 0; i < 5; i++)
    {
        /*****poll函数调用阻塞的时间为1s*****/
        int poll_ret = poll(fds, 1, 1000);
```

> **`int poll(struct pollfd fds[], nfds_t nfds, int timeout)`**
>
> ```
> 功能：监控文件描述符（多个）；
> 说明：timemout=0，poll()函数立即返回而不阻塞；timeout=INFTIM(-1)，poll()会一直阻塞下去，直到检测到return > 0；
> 参数：
>    	fds:struct pollfd结构类型的数组；
>    	nfds:用于标记数组fds中的结构体元素的总数量；
>    	timeout:是poll函数调用阻塞的时间，单位：毫秒；
> 返回值：
>    	>0:数组fds中准备好读、写或出错状态的那些socket描述符的总数量；
>    	==0:poll()函数会阻塞timeout所指定的毫秒时间长度之后返回;
>    	-1:poll函数调用失败；同时会自动设置全局变量errno；
> ```
> 这里返回值会赋值给`poll_ret`，后续会对返回值进行分类处理。

```c++
        /*处理poll返回的结果 */
        if (poll_ret == 0) 
        {
            /* 这表示时间溢出了，在1s内没有获取到发布者的数据 */
            PX4_ERR("Got no data within a second");
            //这个函数会将信息记录在日志中
        } 
        else if (poll_ret < 0) 
        {
            /* 出现问题 */
            if (error_counter < 10 || error_counter % 50 == 0) 
            {
                /*使用计数器来防止溢出（并减缓我们的速度）*/
                PX4_ERR("ERROR return value from poll(): %d", poll_ret);
                //这个函数会将信息记录在日志中
            }
            error_counter++;
        } 
        else 
        {
            if (fds[0].revents & POLLIN) 
            {
                /*从文件描述符中获取订阅的数据*/
                struct vehicle_acceleration_s accel;
```

> `if (fds[0].revents & POLLIN)`，如果结构体数组中的第1项`fds[0].revents`是`POLLIN`，说明此时主题是可读的。那么`fds[0].revents & POLLIN`的值就是1。
>
> 结构体类型`vehicle_acceleration_s`是系统根据vehicle_acceleration主题中的数据类型和个数自动创建的一个结构体类型，后面会说。

```c++
                /*****复制主题携带的数据到指定的结构体*****/
                orb_copy(ORB_ID(vehicle_acceleration), sensor_sub_fd, &accel);
```

> **`int orb_copy(const struct orb_metadata *meta, int handle, void *buffer)`**
>
> ```
> 功能：从订阅的主题中获取数据并将数据保存到buffer中；
> 参数：
>    	meta:uORB元对象，可以认为是主题id，一般是通过ORB_ID(主题名)来赋值;
>    	handle:订阅主题返回的句柄；
>    	buffer:从主题中获取的数据；
> 返回值：
>    	返回OK表示获取数据成功，错误返回ERROR;否则则有根据的去设置errno;
> eg:
>    	struct sensor_combined_s raw;
>    	orb_copy(ORB_ID(sensor_combined), sensor_sub_fd, &raw);
> ```
> 这里把vehicle_acceleration主题中的数据赋值到结构体`accel`中。

```c++
                PX4_INFO("Accelerometer:\t%8.4f\t%8.4f\t%8.4f",
                	(double)accel.xyz[0],
					(double)accel.xyz[1],
					(double)accel.xyz[2]);
```

> 这里将加速度计数据打印出来。
>
> vehicle_acceleration主题中的数据在Firmware/msg/vehicle_acceleration.msg文件中定义。系统自动生成的`vehicle_acceleration_s`结构体类型中的成员就是下列中的各个变量。
>
> ```
> uint64 timestamp		# time since system start (microseconds)
> 
> uint64 timestamp_sample		# the timestamp of the raw data (microseconds)
> 
> float32[3] xyz			# Bias corrected acceleration (including gravity) in the FRD body frame XYZ-axis in m/s^2
> ```
> 
> 所以引用时，我们可以通过`accel.xyz[0]`来引用结构体`raw`中成员`xyz`数组中的第2个元素。
> 
> 其中结构体成员`xyz`数组的数据类型为float32，所以需要使用`(double)`进行类型转换。

```c++
				//赋值结构体att中的变量
                //这里q是四元数，本不应该赋值加速度数据，这里只是举一个例子
				att.q[0] = accel.xyz[0];
				att.q[1] = accel.xyz[1];
				att.q[2] = accel.xyz[2];

				//发布vehicle_attitude主题
				orb_publish(ORB_ID(vehicle_attitude), att_pub, &att);
```

> **`int orb_publish(const struct orb_metadata *meta, orb_advert_t handle, const void *data)`**
>
> ```
> 功能：发布新数据到主题；
> 参数：
> 	meta:uORB元对象，可以认为是主题id，一般是通过ORB_ID(主题名)来赋值;
> 	handle:orb_advertise函数返回的句柄；
> 	data:指向待发布数据的指针；
> 返回值：
> 	OK表示成功；错误返回ERROR；否则则有根据的去设置errno;
> eg: 
> 	orb_publish(ORB_ID(vehicle_attitude), att_pub_fd, &att);
> ```

```c++
			}
			/* 如果有更多的文件描述符，可以这样：
             * if (fds[1..n].revents & POLLIN) {}
             */
        }
    }
	PX4_INFO("exiting");
    return 0;
}
```

程序流程图如下：

<div align=center><img src="https://jokerbin.oss-cn-beijing.aliyuncs.com/202302031623272.png" width="500"></div>



### 5.2 编译并上传px4_simple_app应用程序到飞控固件中

为了运行它，首先需要确保它是作为PX4的一部分构建的。 应用程序被将依据目标的适当板级*cmake*文件添加到编译/固件中。

<div align=center><img src="https://jokerbin.oss-cn-beijing.aliyuncs.com/202301301024483.png" width="800"></div>

首先需要在px4board文件中添加px4_simple_app，位置在Firmware/boards/px4/fmu-v2/default.px4board，在空白行添加以下语句。

```
CONFIG_EXAMPLES_PX4_SIMPLE_APP=y
```

<div align=center><img src="https://jokerbin.oss-cn-beijing.aliyuncs.com/202301301029972.png" width="800"></div>

保存之后退出。

打开命令行终端Terminal，使用`ls`、`cd`命令进入Firmware文件夹中。

<div align=center><img src="https://jokerbin.oss-cn-beijing.aliyuncs.com/202301301037151.png" width="800"></div>

在Firmware文件夹中输入命令即可对固件进行编译。

```
make px4_fmu-v2_default
```

<div align=center><img src="https://jokerbin.oss-cn-beijing.aliyuncs.com/202301301039330.png" width="800"></div>

等待进度到100%即可完成固件编译。

<div align=center><img src="https://jokerbin.oss-cn-beijing.aliyuncs.com/202301301041268.png" width="800"></div>

将编译好的固件下载到无人机，需要输入命令。

```
make px4_fmu-v2_default upload
```

<div align=center><img src="https://jokerbin.oss-cn-beijing.aliyuncs.com/202301301042272.png" width="800"></div>

这里提示需要连接无人机的数据线，等待进度条读完即可完成烧录固件。

<div align=center><img src="https://jokerbin.oss-cn-beijing.aliyuncs.com/202301301046794.png" width="800"></div>



### 5.3 在QGC地面站MavlinkConsole终端运行px4_simple_app应用程序

打开QGC地面站，进入MavlinkConsole终端。

<div align=center><img src="https://jokerbin.oss-cn-beijing.aliyuncs.com/202301301049058.png" width="800"></div>

在终端中输入`help`命令调出所有进程，这时会发现px4_simple_app已经在列表中。

<div align=center><img src="https://jokerbin.oss-cn-beijing.aliyuncs.com/202301301049764.png" width="800"></div>

在终端中输入`px4_simple_app start`命令调用该进程。

<div align=center><img src="https://jokerbin.oss-cn-beijing.aliyuncs.com/202302031610878.png" width="800"></div>

运行后会输出加速度计的数据，说明程序运行成功。



### 5.4 创建自己的主题

官方提供的通用接口标准主题都放在了msg文件夹下了。如果要定义我们自己的主题，比如我们新添加了超声波传感器，为了将超声波传感器的数据发布出去给其他需要的应用订阅，那么就需要创建我们的主题了。

在msg文件夹中新建mytopic.msg主题文件，输入以下代码。

<div align=center><img src="https://jokerbin.oss-cn-beijing.aliyuncs.com/202302031645995.png" width="800"></div>

在msg/CMakeLists.txt中set()函数添加mytopic.msg作为索引。

<div align=center><img src="https://jokerbin.oss-cn-beijing.aliyuncs.com/202302031646487.png" width="800"></div>

对固件进行编译。

在编译生成的build_px4fmu-v2_default文件夹中进入build/px4_fmu-v2_default/uORB/topics/mytopic.h中，系统会自动创建名为mytopic_s的结构体。

<div align=center><img src="https://jokerbin.oss-cn-beijing.aliyuncs.com/202302031646826.png" width="800"></div>

自动生成的结构体会包括时间戳`timestamp`、时间戳样式`timestamp_sample`和自动填充`_padding0[]`。其中自动填充会将整个消息填充到64位的整数倍，比如内容是uint64 timestamp、uint8 a，这时就会自动生成一个uint8 _padding0[7]，7个8位元素的一个数组和a凑成64位。

对于订阅者来说，就可以参考上文【订阅和发布主题】了。不过还是提供下简单例程。

```c++
#include <px4_platform_common/px4_config.h>
#include <px4_platform_common/tasks.h>
#include <px4_platform_common/posix.h>
#include <unistd.h>
#include <stdio.h>
#include <poll.h>
#include <string.h>
#include <math.h>

#include <uORB/uORB.h>
#include <uORB/topics/vehicle_acceleration.h>
#include <uORB/topics/vehicle_attitude.h>
#include <uORB/topics/Mytest.h>

__EXPORT int Mytest_main(int argc, char *argv[]);

int Mytest_main(int argc, char *argv[])
{
    struct mytopic_s orbtest;
    memset(&orbtest, 0, sizeof(orbtest));

    orb_advert_t pub_fd = orb_advertise(ORB_ID(mytopic), &orbtest);

    orbtest.roll = 1;
    orbtest.pitch = 2;
    orbtest.yaw = 3;

    orb_publish(ORB_ID(mytopic), pub_fd, &orbtest);

    int sub_fd = orb_subscribe(ORB_ID(mytopic));

    struct mytopic_s data_copy;
    orb_copy(ORB_ID(mytopic), sub_fd, &data_copy);

    printf("DATA:\t%4.2f\t%4.2f\t%4.2f\t%4.2f",
             (double)data_copy.roll,
             (double)data_copy.pitch,
             (double)data_copy.yaw);
    PX4_INFO("exiting");

    return 0;
}
```

不要忘了配套的CMakeLists.txt文件。

```
px4_add_module(
	MODULE examples__Mytest
	MAIN Mytest
	SRCS
		Mytest.c
	DEPENDS
	)
```



****

参考资料：

[PX4/Pixhawk---uORB深入理解和应用_别打名名的博客-CSDN博客_uorb](https://blog.csdn.net/FreeApe/article/details/46880637?ops_request_misc=%7B%22request%5Fid%22%3A%22166281780116782427410527%22%2C%22scm%22%3A%2220140713.130102334.pc%5Fblog.%22%7D&request_id=166281780116782427410527&biz_id=0&utm_medium=distribute.pc_search_result.none-task-blog-2~blog~first_rank_ecpm_v1~rank_v31_ecpm-1-46880637-null-null.nonecase&utm_term=PX4&spm=1018.2226.3001.4450)

[PX4/Pixhawk---uORB深入理解和应用（最新版）_写代码的小姜的博客-CSDN博客_pixhawk软件架构](https://blog.csdn.net/qq_42955211/article/details/113666959)

