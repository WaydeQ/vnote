# strace

strace -o output.26577.log -i -v -T -tt -e trace=all -p 26577

### strace问题定位案例
#### 1、定位进程异常退出
- 问题：机器上有个叫做run.sh的常驻脚本，运行一分钟后会死掉。需要查出死因。

- 定位：进程还在运行时，通过ps命令获取其pid, 假设我们得到的pid是24298

strace -o strace.log -tt -p 24298
查看strace.log, 我们在最后2行看到如下内容:
```
22:47:42.803937 wait4(-1,  <unfinished ...>
22:47:43.228422 +++ killed by SIGKILL +++
这里可以看出，进程是被其他进程用KILL信号杀死的。
```
- 实际上，通过分析，我们发现机器上别的服务有个监控脚本，它监控一个也叫做run.sh的进程，当发现run.sh进程数大于2时，就会把它杀死重启。结果导致我们这个run.sh脚本被误杀。

- 进程被杀退出时，strace会输出killed by SIGX(SIGX代表发送给进程的信号)等，那么，进程自己退出时会输出什么呢？

- 这里有个叫做test_exit的程序，其代码如下:
```
#include <stdio.h>
#include <stdlib.h>

int main(int argc, char **argv) {
       exit(1);
}
```

- 我们strace看下它退出时strace上能看到什么痕迹。
strace -tt -e trace=process -f ./test_exit
说明: -e trace=process 表示只跟踪和进程管理相关的系统调用。

- 输出：
```
23:07:24.672849 execve("./test_exit", ["./test_exit"], [/* 35 vars */]) = 0
23:07:24.674665 arch_prctl(ARCH_SET_FS, 0x7f1c0eca7740) = 0
23:07:24.675108 exit_group(1)           = ?
23:07:24.675259 +++ exited with 1 +++
```
- 可以看出，进程自己退出时（调用exit函数，或者从main函数返回）, 最终调用的是exit_group系统调用， 并且strace会输出exited with X（X为退出码）。

- 可能有人会疑惑，代码里面明明调用的是exit, 怎么显示为exit_group?

- 这是因为这里的exit函数不是系统调用，而是glibc库提供的一个函数，exit函数的调用最终会转化为exit_group系统调用，它会退出当前进程的所有线程。实际上，有一个叫做_exit()的系统调用(注意exit前面的下划线), 线程退出时最终会调用它。

#### 2、定位共享内存异常
- 有个服务启动时报错：
```
shmget 267264 30097568: Invalid argument
Can not get shm...exit!
错误日志大概告诉我们是获取共享内存出错，通过strace看下：

strace -tt -f -e trace=ipc ./a_mon_svr     ../conf/a_mon_svr.conf
输出：

22:46:36.351798 shmget(0x5feb, 12000, 0666) = 0
22:46:36.351939 shmat(0, 0, 0)          = ?
Process 21406 attached
22:46:36.355439 shmget(0x41400, 30097568, 0666) = -1 EINVAL (Invalid argument)
shmget 267264 30097568: Invalid argument
Can not get shm...exit!
```
这里，我们通过-e trace=ipc 选项，让strace只跟踪和进程通信相关的系统调用。

- 从strace输出，我们知道是shmget系统调用出错了，errno是EINVAL。同样， 查询下shmget手册页，搜索EINVAL的错误码的说明:
```
EINVAL A new segment was to be created and size < SHMMIN or size > SHMMAX, or no new segment was to be created, a segment with given key existed, but size is greater than the size of that segment
```
- 翻译下，shmget设置EINVAL错误码的原因为下列之一：
```
要创建的共享内存段比 SHMMIN小 (一般是1个字节)

要创建的共享内存段比 SHMMAX 大 (内核参数kernel.shmmax配置)

指定key的共享内存段已存在，其大小和调用shmget时传递的值不同。

从strace输出看，我们要连的共享内存key 0x41400, 指定的大小是30097568字节，明显与第1、2种情况不匹配。那只剩下第三种情况。使用ipcs看下是否真的是大小不匹配：

ipcs  -m | grep 41400
key        shmid      owner      perms      bytes      nattch     status    
0x00041400 1015822    root       666        30095516   1
可以看到，已经0x41400这个key已经存在，并且其大小为30095516字节，和我们调用参数中的30097568不匹配，于是产生了这个错误。

在我们这个案例里面，导致共享内存大小不一致的原因，是一组程序中，其中一个编译为32位，另外一个编译为64位,代码里面使用了long这个变长int数据类型。

把两个程序都编译为64解决了这个问题。
```
- 这里特别说下strace的-e trace选项。

- 要跟踪某个具体的系统调用，-e trace=xxx即可。但有时候我们要跟踪一类系统调用，比如所有和文件名有关的调用、所有和内存分配有关的调用。

- 如果人工输入每一个具体的系统调用名称，可能容易遗漏。于是strace提供了几类常用的系统调用组合名字。
```
-e trace=file     跟踪和文件访问相关的调用(参数中有文件名)
-e trace=process  和进程管理相关的调用，比如fork/exec/exit_group
-e trace=network  和网络通信相关的调用，比如socket/sendto/connect
-e trace=signal    信号发送和处理相关，比如kill/sigaction
-e trace=desc  和文件描述符相关，比如write/read/select/epoll等
-e trace=ipc 进程见同学相关，比如shmget等
```
绝大多数情况，我们使用上面的组合名字就够了。实在需要跟踪具体的系统调用时，可能需要注意C库实现的差异。

比如我们知道创建进程使用的是fork系统调用，但在glibc里面，fork的调用实际上映射到了更底层的clone系统调用。使用strace时，得指定-e trace=clone, 指定-e trace=fork什么也匹配不上。

#### 3、 性能分析
- 假如有个需求，统计Linux 4.5.4 版本内核中的代码行数(包含汇编和C代码)。这里提供两个Shell脚本实现：

poor_script.sh:
```
!/bin/bash
total_line=0
while read filename; do
   line=$(wc -l $filename | awk ‘{print $1}’)
   (( total_line += line ))
done < <( find linux-4.5.4 -type f  ( -iname ‘.c’ -o -iname ‘.h’ -o -iname ‘*.S’ ) )
echo “total line: $total_line”

good_script.sh:

!/bin/bash
find linux-4.5.4 -type f  ( -iname ‘.c’ -o -iname ‘.h’ -o -iname ‘*.S’ ) -print0 \
| wc -l —files0-from - | tail -n 1
```
两段代码实现的目的是一样的。 我们通过strace的-c选项来分别统计两种版本的系统调用情况和其所花的时间(使用-f同时统计子进程的情况)

从两个输出可以看出，good_script.sh 只需要2秒就可以得到结果：19613114行。它大部分的调用(calls)开销是文件操作(read/open/write/close)等，统计代码行数本来就是干这些事情。

而poor_script.sh完成同样的任务则花了539秒。它大部分的调用开销都在进程和内存管理上(wait4/mmap/getpid…)。

实际上，从两个图中clone系统调用的次数,我们可以看出good_script.sh只需要启动3个进程，而poor_script.sh完成整个任务居然启动了126335个进程！

而进程创建和销毁的代价是相当高的，性能不差才怪。

### 总结
- 当发现进程或服务异常时，我们可以通过strace来跟踪其系统调用，“看看它在干啥”，进而找到异常的原因。熟悉常用系统调用，能够更好地理解和使用strace。

- 当然，万能的strace也不是真正的万能。当目标进程卡死在用户态时，strace就没有输出了。

- 这个时候我们需要其他的跟踪手段，比如gdb/perf/SystemTap等。

- 备注：

1、perf原因kernel支持

2、ftrace  kernel支持可编程

3、systemtap 功能强大，RedHat系统支持，对用户态，内核态逻辑都能探查，使用范围更广