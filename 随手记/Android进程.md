```shell
adb shell ps
ps -ef
ps -A
# 上面三个命令均可
```

无意观察了一下ps的具体信息

```
USER           PID  PPID     VSZ    RSS WCHAN            ADDR S NAME                       
root             1     0   66824   1480 0                   0 S init
root             2     0       0      0 0                   0 S [kthreadd]
root             3     2       0      0 0                   0 S [ksoftirqd/0]
root             5     2       0      0 0                   0 S [kworker/0:0H]
root             6     2       0      0 0                   0 S [kworker/u16:0]
root             7     2       0      0 0                   0 S [rcu_preempt]
root             8     2       0      0 0                   0 S [rcu_sched]
root             9     2       0      0 0                   0 S [rcu_bh]
root            10     2       0      0 0                   0 S [rcuop/0]
root            11     2       0      0 0                   0 S [rcuos/0]
root            12     2       0      0 0                   0 S [rcuob/0]
root            13     2       0      0 0                   0 S [migration/0]
root            14     2       0      0 0                   0 S [migration/1]
root            15     2       0      0 0                   0 S [ksoftirqd/1]
root            17     2       0      0 0                   0 S [kworker/1:0H]
root            18     2       0      0 0                   0 S [rcuop/1]
root            19     2       0      0 0                   0 S [rcuos/1]
root            20     2       0      0 0                   0 S [rcuob/1]
root            21     2       0      0 0                   0 S [migration/2]
root            22     2       0      0 0                   0 S [ksoftirqd/2]
root            24     2       0      0 0                   0 S [kworker/2:0H]
root            25     2       0      0 0                   0 S [rcuop/2]
root            26     2       0      0 0                   0 S [rcuos/2]
root            27     2       0      0 0                   0 S [rcuob/2]
root            28     2       0      0 0                   0 S [migration/3]
root            29     2       0      0 0                   0 S [ksoftirqd/3]
root            31     2       0      0 0                   0 S [kworker/3:0H]
root            32     2       0      0 0                   0 S [rcuop/3]
root            33     2       0      0 0                   0 S [rcuos/3]
root            34     2       0      0 0                   0 S [rcuob/3]
root            35     2       0      0 0                   0 S [migration/4]
root            36     2       0      0 0                   0 S [ksoftirqd/4]
root            38     2       0      0 0                   0 S [kworker/4:0H]
root            39     2       0      0 0                   0 S [rcuop/4]
root            40     2       0      0 0                   0 S [rcuos/4]
root            41     2       0      0 0                   0 S [rcuob/4]
root            42     2       0      0 0                   0 S [migration/5]
```

上面截取了部分ps打印出来的信息，其中每列代表的含义如下

**USER:**进程当前用户

**PID:当前进程id

**PPID:**父进程ID

**VSZ:**当前进程虚拟内存的大小

**RSS:**实际驻留在内存中的内存大小

**WCHAN:**休眠进程在内核中的地址

**ADDR:** oreo之后才有的，之前叫做pc，后面记得查查是干啥的

**NAME:**进程的名称

| 状态 | 含义                            |
| ---- | ------------------------------- |
| D    | 不可中断的睡眠态                |
| R    | 运行态                          |
| S    | 睡眠态                          |
| T    | 被跟踪或已停止                  |
| Z    | 僵尸态                          |
| W    | 进入内存交换（从内核2.6开始无效 |
| X    | 死掉的进程                      |
| <    | 高优先级                        |
| N    | 低优先级                        |
| L    | 有些页被锁进内存                |
| s    | 包含子进程                      |
| l    | 多线程，克隆线程                |
| +    | 位于后台的进程组                |

随后查看了一下微信的进程信息，微信的包名```com.tencent.mm```发现了微信有这么几个进程

```
adb shell ps | grep com.tencent.mm
```

```
u0_a196       3472   988 4781136 136556 0                   0 S com.tencent.mm:appbrand0
u0_a196       5183   988 4760956 201704 0                   0 S com.tencent.mm:sandbox
u0_a196      20108   988 4840448  59232 0                   0 S com.tencent.mm:push
u0_a196      20209   988 30837284 244696 0                  0 S com.tencent.mm
u0_a196      22626   988 9590240 119172 0                   0 S com.tencent.mm:toolsmp
u0_a196      30371   988 4919116  79072 0                   0 S com.tencent.mm:tools
u0_a196      31215   988 9104540 105488 0                   0 S com.tencent.mm:appbrand1
u0_a196      31325   988 9451616 137620 0                   0 S com.tencent.mm:appbrand2
```

这几个进程的父进程都是988，所有又去看了下988是什么

```
root           988     1 4352272  17484 0                   0 S zygote64
```

988叫做zygote64，他的ppid是1

一个简单的理解：

在Android系统中，所有的应用程序进程以及系统服务进程SystemServer都是由Zygote进程孕育（fork）出来的，这也许就是为什么 要把它称为Zygote（受精卵）的原因吧

