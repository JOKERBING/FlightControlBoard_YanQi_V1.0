# Ubuntu中用终端编译并运行C文件

新建一个C文件，内部代码如下

```
/* example.c*/
#include <stdio.h>
#include <pthread.h>
void thread(void)
{
int i;
for(i=0;i<3;i++)
printf("This is a pthread.\n");
}

int main(void)
{
pthread_t id;
int i,ret;
ret=pthread_create(&id,NULL,(void *) thread,NULL);
if(ret!=0){
printf ("Create pthread error!\n");
exit (1);
}
for(i=0;i<3;i++)
printf("This is the main process.\n");
pthread_join(id,NULL);
return (0);
}
```

在命令行中进入该文件的文件夹所在位置，编译此程序

```
amov@ubuntu:~/px4_example/pthread$ gcc example1.c -lpthread -o example1
```

运行此程序显示运行结果

```
amov@ubuntu:~/px4_example/pthread$ ./example1
This is the main process.
This is the main process.
This is the main process.
This is a pthread.
This is a pthread.
This is a pthread.
```

