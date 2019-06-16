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
1. Linux的启动过程
* system startup BIOS
* MBR, GRUB (Bootloader)
* Kernel
* Init(进入user space)

2. 启动的过程（上面的1,2）
* 开机并且跳转到包含特定BIOS代码的地址
* BIOS进行开机自检
* 寻找可启动设备
* 从MBR中加载和执行启动扇区
* 启动OS

3. MBR
主引导记录，存储在硬盘上的第一个sector,包含主引导加载程序
BIOS在执行后会将控制权转交给MBR

4. MBR的结构
* Bootloader代码
* 分区表（逻辑分区）
* Magic Number

5. Bootloader
任务：将Kernel Code加载进内存
常见: GRUB, LILO

6. GRUB启动
BIOS寻找硬盘，将控制权转交给MBR
MBR包含GRUB的第一阶段代码，然后负责跳转到第1.5阶段代码
第1.5阶段代码紧接第一扇区，负责加载第二阶段代码
第二阶段代码显示控制菜单，将用户选择的kernel版本加载到内存中

7. Kernel代码
总是存储在内存中，直到关机

8. init过程（进入user space）
基于SystemV启动系统
第一个启动的脚本是/etc/rc.d/rc.sysinit
基于不同的run-level开始执行不同的启动脚本
第一个进程，init进程，ID为1，启动在inittab中声明的系统进程，启动多个getty实例（零号进程是scheduler）

9. inittab文件
计算机将启动在/etc/inittab中由initdefault定义的运行级别

10. 运行级别run-level
0-6
会根据运行级别启动/etc/rc.d/rc#.d下对应的文件

11. 守护进程
/etc/init.d/
这些runlevel脚本启动是整个过程中的最后一步

12.upstart
事件驱动的启动系统
新硬件被发现时动态启动任务

13. systemd 
一个用来管理守护进程的守护进程
target取代了runlevel的概念

14.源代码
* 0x7C00
* bootsector: 0x7C00 -> 0x90000
* bootsect.S 载入setup.S 然后setup.S跳转到32位保护模式
* 保护模式下进行初始化idt,gdt,跳转到start_kernel()
* 保护模式下解压缩核心代码，初始化页表，idt,gdt
* init 1号进程，完成user level初始化，比如smp等等


### 第三课
模块前面说过了，可以动态加载卸载，为系统添加更多的功能

1. /proc文件系统
伪文件系统，实时变化，一直驻留在内存中，大小为0，修改时间是上次启动的时间

2. 可以通过修改/proc/sys的值来修改内核的参数 系统重启后配置失效！

3. /proc/kcore 具体物理内存的别名，不占用空间，但是有大小，为具体内存+4KB

### 第四课
1. 进程 程序运行的一个实例
2. 线程 进程的一个执行流 程序执行的单位
3. 内核线程
内核线程是内核的一个clone 仅在内核空间运行 kernel_thread()
4. 轻量级进程 Linux对现成的实现与支持
进程与LWP: 一对多映射

5. PID 同一组的LWP具有一个PID, thread group是LWP的集合，对应的PID是第一个LWP的PID
6. task_struct 进程的描述符
进程描述符的管理 一共8KB 两个page 包含一个thread_info的struct 以及 内核态的进程堆栈

7. 进程的管理
通过一个链表管理 list_head存储在task_struct中的tasks字段 prev next分别指向前一个和后一个task_struct 
RUNNING状态的链表 通过runqueue的机制实现 切分为140个链表，根据不同的runlevel

8. 进程的生成关系
process 0, 1由内核创建，然后process中会存储内核的生成关系，比如parent 

9. PID的实现
通过哈希表 pid_hash数组包含四个哈希表以及描述符中对应的字段 pid, tgid, pgrp, session
通过chaining来解决冲突
10. 等待队列
处于TASK_INTERRUPTABLE和TASK_UNINTERRUPTABLE两种类型的process放在wait Queue中

11. 进程的切换机制
在老版本上硬件切换 a far jump
新版本上软件切换 一组mov指令
切换的流程 1. 切换PGD 2. 切换内核态堆栈以及硬件上下文(TSS段)

12. 创建进程
clone() fork() vfork() 系统调用 
Copy On Write的机制
clone() 相比fork()更具有选择性

### 第五课
1. 时间片的长度适中， 在保证系统低响应时间的前提下，尽量保证时间片长
2. Linux进程可抢占，被抢占进程仍处于TASK_RUNNING状态
3. 普通进程的调度算法
普通进程有100-139的静态优先级 系统根据消耗的时间片分配新的时间片
基本时间片的优先级越高，基本时间片越长
4. 动态优先级DP = max(100, min(SP - bonus + 5, 139)) bonus根据平均睡眠时间决定
5. 交互式相比批处理式有更高的动态优先级
6. 活动进程是未耗尽时间片的可运行进程；过期进程是已耗尽时间片的可运行进程
7.实时进程有1-99的优先级 实时进程总是处于活动状态，直到遇到切换的条件
8. 多级调度算法 MLFQ 
9. CPU具有runqueue结构，作为系统进行调度的根据
10. prio_array_t 结构
* nr_active 描述进程符的数量
* bitmap 优先级bitmap
* queue 140个不同runlevel的list_head
11. schedule() 直接调用/延迟调用
12. 通过调度域来实现CPU对runqueue的负载均衡
13. CFS调度解决的问题：有些进程始终得不到资源，starve
具体设计：每一个进程具有相同的virtual runtime, sleeping task的优先级提升
使用红黑树记录任务的运行时间 选择最左边的结点运行 选择的进程从树中移除，更新时间信息
虚拟时间是相对nice为0的权重的比例值
通过时钟中断来更新vruntime,判断是否需要rescedule(),从而抛弃了时间片的传统概念
结构 runqueue->struct rq->struct cfs_eq->struct sched_entity
调度时机：阻塞操作、TIF位、Wakeups

### 第六课
1. 中断，轮询，两种Handler的实现机制
2. 同步中断，异步中断 同步终端可以理解为异常， 异步中断理解为我们平时认知的中断
3. fault,trap,abort abort后程序无法继续正常运行
4. 终端的处理机制 外设产生中断，CPU处理中断，最后返回
5. 由于中断需要有快速响应、可以嵌套的能力，一般的系统里把中断分为上、下两个部分
在上半部分，只处理一些可以快速返回的事务
6. x86对于中断处理有自己的架构 IDT表 
IDT的架构 256项 每项8个字节
* Task Gate 存放需要取代当前进程的进程
* Interrupt Gate 包含段选择符和段内偏移量
* Trap Gate 用来激活Linux异常处理程序
7. 中断向量表 根据中断的类型存储相应的中断处理程序入口
8. ARM v7的中断处理
* 把当前CPSR寄存器的值存储在SPSR寄存器内
* lr寄存器保存返回地址
* 修改CPSR的值，进入对应异常模式
* PC跳转
9. 中断上下文
中断发生后，软件需要做下面的事情
* 保存中断现场 包括 lr_irq寄存器和spsr_irq寄存器（trapframe）
* 跳转到中断处理函数
* 恢复中断线程，跳转到中断点继续执行

10. 中断控制器
pending寄存器 表示中断源
mask寄存器 屏蔽中断源
11. 中断号 硬件中断号 软件中断号
12. 每个中断控制器用一个irq domain来描述，
中断映射过程：
* 从allocated_irqs位图里分配一个空间的比特位
* 分配一个struct irq_desc数据结构 与一个中断号对应
13. irqaction描述符 形成一个链表
14. 中断上半部 保证不被其他中断打断，与硬件有关的处理部分放在这里
15. 中断上下文
软中断 softirq_pending位图 与CPU相关
中断处理函数结束，返回现场之前， 检查softirq_pending寄存器， 处理对应的软中断softirq_vec->action()
软中断算是中断上下文的一部分，优先级高于普通进程，不能被自己打断，只能被中断上半部分打断，执行点在硬件中断处理函数返回之前
可以在都处理器上并行执行
16. Tasklet是一种下半部机制，是软中断的一个变种
基于软中断实现，特定类型的tasklet只能在一个CPU上序列执行
每个CPU维护 tasklet_vec 以及 tasklet_hi_vec 
17. workqueue 中断下半部的一种处理机制，把需要延迟执行的部分放到一个线程里执行
允许重新调度、sleep
CMWQ 高并发性的工作队列
可以在queue上声明一个work和该work的回调函数



* 



