# 分析移动设备耗电情况

## 零、电量概述

移动设备可以从两个层面统计电量的消耗，分别为 **软件排行榜** 及 **硬件排行榜**。它们各有自己的耗电榜单，软件排行榜为机器中每个 App 的耗电榜单，硬件排行榜则为各个硬件的耗电榜单。这两个排行榜的统计是互为独立，互不干扰的。

本次主要讲述**软件层面**的耗电情况，其实我们可以在每台设备的**设置→电量**中可以直观的看到，需要注意的是Android的功耗统计是通过代码估算的，没有任何硬件的参与，所以信息和信息的准确度取决于各家厂商。

我们可以通过以下这几个维度来对一个APP的功耗进行分析

| cpu                | CpuPower         |
| ------------------ | ---------------- |
| 保持唤醒锁         | WakelockPower    |
| 无线电（2G/3G/4G） | MobileRadioPower |
| wifi               | WifiPower        |
| 蓝牙               | BluetoothPower   |
| 传感器             | SensorPower      |
| 相机               | CameraPower      |
| 闪光灯             | FlashlightPower  |

## 一、使用Battery Historian & Batterystats分析Android设备耗电情况

### 简介

Batterystats 是包含在 Android 框架中的一种工具，用于收集设备上的电池数据。您可以使用```adb```将收集的电池数据转储到开发机器，并生成可使用 Battery Historian 分析的报告。Battery Historian支持Android 5.0（API 21）以上系统设备，并且会将报告从 Batterystats 转换为可在浏览器中查看的 HTML 直观视图。

适合的场景：

- 显示进程从什么位置以及通过何种方式消耗电池电量。
- 识别系统为了延长电池续航时间可能会延迟甚至移除应用中的哪些任务

>源码路径：https://github.com/google/battery-historian
>
>官方文档：https://developer.android.com/topic/performance/power/battery-historian?hl=zh-cn#asd



### 安装



#### docker安装

由于官方镜像需要科学上网等问题，这里使用一个改造版本

##### 1、拉取镜像

```
docker pull y837979117/android-battery-historian
```

##### 2、构建镜像

```
docker run -d --name mybattery -p 9999:9999 y837979117/android-battery-historian
```

>演示地址：https://bathist.ef.lc/（科学）  http://121.40.190.86:9999/（比较慢，可能会访问不了） 

##### 3、初始页截图

![image.png](https://i.loli.net/2021/08/02/zgNCbwoIeV3ESBW.png)



#### 物理机安装

##### 1、Python环境

必须是2.7版本

##### 2、Go环境

最低1.8.1



### 使用

**数据的采集是机器单方面的行为，不需要依赖第三方的辅助**，因为这是 Android 系统级的功能。但在这之前，需要做一些准备。

请事先打开开发者模式。USB 接入计算机。在终端中执行：

```
adb shell dumpsys batterystats --enable full-wake-history
```

默认情况下，唤醒（wake）数据是不会被采集的，因此我们需要将其启用。如果采集的不是全量 wake up 数据，在分析阶段则不能很好的观测数据。

随后终端执行：

```
adb shell dumpsys batterystats --reset
```

此命令会清空历史采集的信息。

**最后，拔出 USB。**现在，耗电统计已经开始了。没错，耗电统计就是一直开着的，并且无法关闭：这是一个系统级别的功能。

Batterystats 只会记录最后一次充满电后的记录，因此建议测试时可以先把电充满，完成以上操作

测试执行完成后，连接到 USB，终端执行：

```
adb bugreport bugreport.zip
```

>如果是6.0以下的设备执行：
>
>```
>adb bugreport > bugreport.txt
>```



最后终端执行：

```
adb shell dumpsys batterystats --disable full-wake-history
```

**关闭全量记录唤醒。**保持开启会影响设备性能。

















http://www.cxyzjd.com/article/qq_23114525/100586285   这个有一些参数的介绍

https://www.jianshu.com/p/27ba2759b221 这个老哥分析的historian很到位

https://www.jianshu.com/p/b2ab0b620362 介绍了batterystats