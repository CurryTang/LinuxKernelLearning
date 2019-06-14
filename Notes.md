## Linux Kernel 复习笔记

### 第一课
1. Linux的两种运行模式 User mode, Kernel mode 两种模式之间通过system call来进行交互

2. 内核的基本任务:
* 控制对硬件的访问
* 实现并支持对设备文件的基本抽象
* 调度分配系统资源
* 增强系统安全性和保护
* 响应system call

3. 内核设计的目标：性能、稳定性、功能性、保护性、兼容性、可扩展性

4. Linux采用宏内核的设计，所有内核代码都编译成一个二进制文件，所有内核代码都运行在一个大内核地址空间中；同时也参考了微内核的设计，实现了模块式设计、抢占式内核、动态加载内存

5. linux内核目录结构
![](https://www.google.com/url?sa=i&source=images&cd=&cad=rja&uact=8&ved=2ahUKEwi34Z_2uOjiAhUOVt8KHYPSCdQQjRx6BAgBEAU&url=%2Furl%3Fsa%3Di%26source%3Dimages%26cd%3D%26ved%3D%26url%3D%252Furl%253Fsa%253Di%2526source%253Dimages%2526cd%253D%2526ved%253D%2526url%253Dhttps%25253A%25252F%25252Fslideplayer.com%25252Fslide%25252F4795346%25252F%2526psig%253DAOvVaw3Zkp0mlnafXhBNbCfcTwe9%2526ust%253D1560583491821624%26psig%3DAOvVaw3Zkp0mlnafXhBNbCfcTwe9%26ust%3D1560583491821624&psig=AOvVaw3Zkp0mlnafXhBNbCfcTwe9&ust=1560583491821624)

6. 常见的子目录与其作用
* bin: 对于普通用户需要的常用系统命令(启动时)
* sbin: 命令，系统调用类
* proc: 虚拟文件系统，可以查看系统运行信息
* /usr 包含基本所有的用户软件
  * /usr/bin 几乎所有的用户命令
  * /usr/sbin 大部分的root指令，比如server
  * /usr/include C语言头文件
  * /usr/lib library文件,不变的data files
  * /usr/local 用户的本地应用程序
  * /usr/man manual页面
  * ...
  * /usr/tmp 
* /boot 启动时用到的文件
* /lib 共享的programming library
* /modules 可动态加载的linux modules
* /dev 设备文件
* /etc 配置文件
* /skel 初始化/home时，会使用这个文件夹里的files
* /sysconfig 设备的配置文件
* /var 包含与邮件、日志等相关的文件,例如local,log,lock
* /mnt 临时挂载点，缺省挂载点
* /tmp 存储临时文件
* linux/arch 存放architecture dependent的文件
* linux/drivers 驱动设备文件
* linux/fs Virtual File System,包含super.c, inode.c等等
* linux/include 一些系统和用户头文件
* linux/init version.c, main.c main.c里面包含系统entry point
* linux/ipc 主要实现了semaphore, shared memory与messgae queue
* linux/kernel 系统内核代码，包括调度等等
* linux/lib 给kernel code用的library
* linux/mm 系统内存管理
* linux/scripts 系统脚本，包括kernel patching等等

### 第二课
