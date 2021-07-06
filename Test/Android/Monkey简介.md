# Monkey基础知识

## 1.1 Monkey概况

在Android的官方自动化测试领域有一只非常著名的“猴子”叫Monkey，这只“猴子”一旦启动，就会让被测的Android应用程序像 猴子一样活蹦乱跳，到处乱跑。人们常用这只“猴子”来对被测程序进行压力测试，检查和评估被测程序的稳定性。

Android官方对这只“猴子”的描述是这样的:Monkey是Google提供的一个命令行工具，可运行在模拟器或实际设备中。它向系统发送伪随机的用户事件，模拟用户的按键输入、触摸屏输入、手势输入等，从而对正在运行的应用程序进行压力测试，目的是看设备多长时间会出现异常，并观察系统的稳定性和容错性能。

Monkey程序是Android系统自带的，其启动脚本是位于Android系统的/system/bin目录的monkey文件，其jar包是位于Android系统 的/system/framework目录的monkey.jar文件。用户主要是通过adb命令来启动Monkey的，Monkey在运行时，会根据命令行参数的配置，生成伪随机的事件流，并在Android设备上执行对应的测试事件。同时，Monkey还会对测试系统进行监测，当出现以下三种情况时会进行特殊处理:

1、如限定了Monkey运行在特定包上，当监测到试图转到其他包的操作，将对其进行阻止。 

2、如应用程序崩溃或接收到任何失控异常，Monkey将记录对应的错误日志，并根据命令行参数判断是停止运行还是继续运行。

3、如果应用程序发生了程序无响应(application not responding)的错误，Monkey将记录对应的错误日志，并根据命令行参数判断是停止运行还是继续运行。

按照选定的不同级别的反馈信息，在Monkey中还可以看到其执行过程报告和生成的事件。

## 1.2 Monkey参数

Monkey启动的命令行脚本为:

```monkey [options] <count>```

其中，options表示Monkey执行的可配置参数，是可选项(如果不指定options，Monkey将以无反馈模式启动，并把事件任意发送到安装在目标环境中的全部包);count表示Monkey执行的事件数，为必选项。

Options可简单划分为五类:

- 基本配置类参数。
- 事件类型和频率参数。
- 约束限制类参数。
- 调试类参数。
- 其他参数。

### 1.2.1 基本配置类参数

|  参数  | 说明                                                         |
| :----: | :----------------------------------------------------------- |
| --help | 输出monkey的命令行使用方法                                   |
|   -v   | 表示反馈信息的级别，Monkey命令行中每增加一个-v参数，Monkey日志反馈信息的级别会对应增加一个Level。Level 0 (缺省值)除启动提示、测试完成和最终结果之外，提供较少信息。Level 1( -v-v) 提供较为详细的测试信息，如逐个发送到Activity的事件。Level2 ( -v -v -v)提供更加详细的设置信息，如测试中被选中的或未被选中的Activity等<br/>   举例: adb shell monkey -v -v 10 |

### 1.2.2 事件类型和频率参数

Monkey的事件类参数的作用是对随机事件进行调控，从而使其遵照设定运行，如设置各种事件的百分比、设置事件生成所使用的种子值等。频率参数主要限制事件执行的时间间隔。

|          参数           | 说明                                                         |
| :---------------------: | :----------------------------------------------------------- |
|        -s\<seed>        | 伪随机数生成器的种子值。如果用相同的种子值再次运行Monkey,它将生成相同的事件序列<br/>举例adb shell monkey -1111 -v 10 |
|   --throttle<毫秒数>    | 在事件之间插入固定延迟。通过这个选项可以减缓Monkey的执行速度。如果不指定该选项，Monkey 将不会被延迟，事件将尽可能快地被生成 |
|   --pct-touch<百分比>   | 调整触摸事件的百分比(触摸事件是一个 down-up事件，它发生在屏幕上的某单一位置) |
|  --pct-motion<百分比>   | 调整动作事件的百分比(动作事件由屏幕上某处的一个down事件、一系列的伪随机事件和一个 up事件组成) |
| --pct-pinchzoom<百分比> | 调整二指缩放事件的百分比(二指缩放事件即智能机上的放大缩小手势操作) |
| --pct-trackball<百分比> | 调整轨迹事件的百分比(轨迹事件由一个或几个随机的移动组成，有时还伴随点击) |
| --pct-rotation<百分比>  | 调整屏幕旋转事件的百分比(横屏和竖屏)                         |
|    --pct-nav<百分比>    | 调整“基本”导航事件的百分比(导航事件由来自方向输入设备的up、down、left、right 组成) |
| --pct-majornav<百分比>  | 调整“主要”导航事件的百分比(这些导航事件通常引发图形界面中的动作，如键盘的中间按键、回退按键、菜单按键) |
|  --pct-syskeys<百分比>  | 调整“系统”按键事件的百分比(这些按键通常被保留，由系统使用，如Home、Back、Start Call、End Call及音量控制键) |
| --pct-appswitch<百分比> | 调整启动Activity的百分比(在随机间隔里，Monkey通过调用startActivity方法最大限度地开启该package下的全部Activity 的一种方法) |
|   --pct-flip<百分比>    | 调整键盘事件的百分比(键盘事件如点击输入框、键盘弹起、点击输人框以外区域、键盘收回等) |
| --pct-anyevent<百分比>  | 调整其他类型事件的百分比(包罗了所有其他类型的事件，如按键、其他不常用的设备按钮等) |



### 1.2.3 约束限制类参数

|                             参数                             | 说明                                                         |
| :----------------------------------------------------------: | :----------------------------------------------------------- |
| &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;-p< 包名  >&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; | 如果用此参数指定了一个或几个包，Monkey 将只允许系统启动这些包里的Activity。 如果应用程序还需要访问其他包里的Activity (如选择一个联系人)， 那些包也需要在此同时指定。如果不指定任何包，Monkey将允许系统启动全部包里的Activity。如果要指定多个包，需要使用多个-p选项，每个-p选项只能用于一个包 |
|                          -c<类别名>                          | 如果用此参数指定了一个或几个类别( Category), Monkey 将只允许系统启动被这些类别中的某个类别列出的Activity。如果不指定任何类别，Monkey 将选择下列类别中列出的Activity:Intent.CATEGORY_ LAUNCHER或Intent.CATEGORY_ MONKEY。要指定多个类别，需要使用多个-c选项，每个-c选项只能用于一个类别 |

### 1.2.4 调试类参数

通过调试类命令，可以对Monkey进行一些简单的调试，可以快速定位Monkey执行过程中的一些问题。如果用户想监控应用程序所调用的包之间的转换，则可以用--dbg-no-events参数;如果用户想监控内存泄漏，可以用--hprof参数

|             参数             | 说明                                                         |
| :--------------------------: | ------------------------------------------------------------ |
|       --dbg-no-events        | 设置此选项，Monkey 将执行初始启动，进入一个测试Activity,不会再进一步生成事件。为了得到最佳结果，把它与-V、一个或几个包约束，以及一个保持Monkey运行30秒或更长时间的非零值联合起来，从而提供一个可以监视应用程序所调用的包之间的转换的环境 |
|           --hprof            | 设置此选项，将在Monkey事件执行之前和执行之后生成内存快照文件存放于手机的data/misc目录。通过对比执行前后的内存快照文件，可以协助定位内存泄漏问题。由于内存快照文件比较大，所以要小心使用. |
|       --ignore-crashes       | 通常，当应用程序崩溃或发生任何失控异常时，Monkey 将停止运行。如果设置此选项，Monkey 将继续向系统发送事件，直到计数完成 |
|      --ignore-timeouts       | 通常，当应用程序发生任何超时错误(如出现“Application Not Responding对话框)时，Monkey 将停止运行。如果设置了此选项，Monkey将继续向系统发送事件，直到计数完成 |
| --ignore-security-exceptions | 通常，当应用程序发生许可错误(如启动一个需要某些许可的Activity),Monkey将停止运行。如果设置了此选项，Monkey 将继续向系统发送事件，直到计数完成 |
|  --kill-process-after-error  | 通常，当Monkey由于一个错误而停止时，出错的应用程序将继续处于运行状态。当设置了此选项时，将会通知系统停止发生错误的进程 |
|   --monitor-native-crashes   | 监视并报告Android系统中本地代码的崩溃事件                    |
|          --wait-dbg          | 停止执行中的Monkey，直到有调试器和它相连接                   |

### 1.2.5 其他参数

|               参数               | 说明                                                         |
| :------------------------------: | ------------------------------------------------------------ |
| --pkg-blacklist-file<黑名单文件> | 限制Monkey不测试于指定黑名单文档中记录的包( Package)。若没有使用这个参数，Monkey会测试系统内所有的包。若使用了该参数，可通过在黑名单文档内记录所有不要测试的包名称，来限制Monkey的执行范围。黑名单文档中每一-行只能放一个包名 |
| --pkg-whitelist-file<白名单文件> | 限制Monkey只测试于指定的白名单文档中记录的包( Package)。若没有使用这个参数，Monkey会测试系统内所有的包。若使用了该参数，可通过在白名单文档内记录所有要测试的包名，来限制Monkey的执行方位。白名单文档中每一-行只能放一个包名<br/>注意:若要测试的包与其他的包有关联，则必须一起指定这些包来执行这项参数 |
|           -f<脚本文件>           | 指定Monkey执行用户自定义的脚本文件                           |

## 1.3 Monkey事件

Monkey所执行的随机事件流中包含11大事件，分别是触摸事件、手势事件、二指缩放事件、轨迹事件、屏幕旋转事件、基 本导航事件、主要导航事件、系统按键事件、启动Activity事件、键盘事件、其他类型事件。Monkey通过这11大事件来模拟用户 的常规操作，对手机App进行稳定性测试。

### 1.3.1 触摸事件

触摸事件是指在屏幕某处按下并抬起的操作，可通过--pct-touch参数来配置其事件百分比。从Monkey执行该事件对外输出的

日志可以看到:

```
:Sending Touch (ACTION_DOWN): 0,(444.0,1716.0) 
:Sending Touch (ACTION_UP): 0,(447.18365,1728,0087)
```

该事件由一组Touch(ACTION_DOWN)和Touch(ACTION_UP)事件组成，在手机上看到实际操作类似于点击。

### 1.3.2 手势事件

手势事件是指在屏幕某处的按下、随机移动、抬起的操作，即直线滑动操作。可通过--pct-motion参数来配置其事件百分比。 从Monkey执行该事件对外输出的日志可以看到:

```
:Sending Touch (ACTION_DOWN): 0:(282.0,750.0) 
:Sending Touch (ACTION_MOVE): 0:(281.0507,745.5253) 
:Sending Touch (ACTION_MOVE): 0:(274.9443,743.3276) 
:Sending Touch (ACTION_MOVE): 0:(269.18774,738.50525) 
:Sending Touch (ACTION_MOVE): 0:(260.14917,733.6212) 
:Sending Touch (ACTION_UP): 0:(254.1414,730.6132)
```

该事件是由一个ACTION_DOWN事件、一系列ACTION_MOVE事件和一个ACTION_UP事件组成的，在手机上看到的实际操 作是一个没有拐弯的直线滑动操作。

### 1.3.3 二指缩放事件

二指缩放事件是指在屏幕上的两处同时按下，并同时移动，最后同时抬起的操作，即智能机上的放大缩小手势操作。可通

过--pct-pinchzoom参数来配置其事件百分比。从Monkey执行该事件对外输出的日志可以看到:

```
:Sending Touch (ACTION_DOWN): 0:(274.0,193.0)
:Sending Touch (ACTION_POINTER_DOWN 1): 0:(272.80875,198.17978) 1:(26.0,312.0) 
:Sending Touch (ACTION_MOVE): 0:(251.31396,198.5104) 1:(24.973522,308.64676)
:Sending Touch (ACTION_MOVE): 0:(240.28494,202.44012) 1:(23.442032,307.8576)
:Sending Touch (ACTION_MOVE): 0:(221.90855,206.75597) 1:(22.903313,306.47507) 
:Sending Touch (ACTION_MOVE): 0:(210.28592,212.24286) 1:(17.78174,303.11304)
:Sending Touch (ACTION_POINTER_UP 1): 0:(171.06334,236.1724) 1:(10.3147135,293.79877) :Sending Touch (ACTION_UP): 0:(161.06638,240.22447)
```

该事件起始是一个ACTION_DOWN事件和一个ACTION_POINTER_DOWN事件，即模拟两个手指同时点下;中间是一系列 的ACTION_MOVE事件，即两个手指同时在屏幕上直线滑动;结束是由一个ACTION_POINTER_UP事件和一个ACTION_UP事件 组成的，即两个手指同时放开。

### 1.3.4 轨迹事件

轨迹事件是由一个或多个随机的移动组成的，有时会伴随着点击。很早之前的Android手机带有轨迹球，这个事件就是模拟 的轨迹球的操作。现在的手机几乎都没有轨迹球，但轨迹球事件中包含曲线滑动操作，如果被测程序需要曲线滑动时可以选用此 参数。可通过--pct-trackball参数来配置其事件百分比。从Monkey执行该事件对外输出的日志可以看到:

```
:Sending Trackball (ACTION_MOVE): 0:(2.0,3.0)
:Sending Trackball (ACTION_MOVE): 0:(-1.0,4.0) 
:Sending Trackball (ACTION_MOVE): 0:(2.0,-3.0)
```

该事件是由一系列的Trackball(ACTION_MOVE)事件组成的，观察手机上的操作，即为一系列的曲线滑动操作。

### 1.3.5 屏幕旋转事件

屏幕旋转事件是一个隐藏事件，在Android官方文档中并没有记录这个事件。它其实是模拟的Android手机的横屏和竖屏切 换。可通过--pct-rotation参数来配置其事件百分比。从Monkey执行该事件对外输出的日志可以看到:

```
:Sending rotation degree=1, persist=false 
:Sending rotation degree=3, persist=true 
:Sending rotation degree=2, persist=true 
:Sending rotation degree=0, persist=true
```

该事件由一个rotation事件组成，其中degree表示的是旋转方向，顺时针旋转，0表示旋转90度的方向，1表示旋转180度的方 向，2表示旋转270度的方向，3表示旋转360度的方向。在执行过程中，可以看到手机屏幕在横竖屏之间不断地切换。

### 1.3.6 基本导航事件

基本导航事件是指点击方向输入设备的上、下、左、右按键的操作，现在手机上很少有上、下、左、右按键，这种事件一般 用得比较少。可通过--pct-nav参数来配置其事件百分比。从Monkey执行该事件对外输出的日志可以看到:

```
:Sending Key (ACTION_DOWN): 19   // KEYCODE_DPAD_UP
:Sending Key (ACTION_UP): 19     // KEYCODE_DPAD_UP
:Sending Key (ACTION_DOWN): 20   // KEYCODE_DPAD_DOWN
:Sending Key (ACTION_UP): 20     // KEYCODE_DPAD_DOWN
:Sending Key (ACTION_DOWN): 21   // KEYCODE_DPAD_LEFT 
:Sending Key (ACTION_UP): 21     // KEYCODE_DPAD_LEFT
:Sending Key (ACTION_DOWN): 22   // KEYCODE_DPAD_RIGHT
:Sending Key (ACTION_UP): 22     // KEYCODE_DPAD_RIGHT
```

该事件是由一个Key(ACTION_DOWN)和一个Key(ACTION_UP)组成的，点击的就是上、下、左、右四个方向按键。

### 1.3.7 主要导航事件

主要导航事件是指点击“主要导航”按键的操作，这些按键通常会导致UI界面中的动作，如5-way键盘的中间键、回退按键、 菜单按键。可通过--pct-majornav参数来配置其事件百分比。从Monkey执行该事件对外输出的日志可以看到:

```
:Sending Key (ACTION_DOWN): 23  // KEYCODE_DPAD_CENTER
:Sending Key (ACTION_UP): 23    // KEYCODE_DPAD_CENTER
:Sending Key (ACTION_DOWN): 82  // KEYCODE_MENU
:Sending Key (ACTION_UP): 82    // KEYCODE_MENU
```

该事件是由一个Key(ACTION_DOWN)和一个Key(ACTION_UP)组成的，点击的按键就是中间键和菜单键。

### 1.3.8 系统按键事件

系统按键事件是指点击系统保留使用的按键的操作，如点击Home键、返回键、音量调节键等。可通过--pct-syskeys参数来配置其事件百分比。从Monkey执行该事件对外输出的日志可以看到:

```
:Sending Key (ACTION_DOWN): 5   // KEYCODE_CALL
:Sending Key (ACTION_UP): 5     // KEYCODE_CALL
:Sending Key (ACTION_DOWN): 4   // KEYCODE_BACK
:Sending Key (ACTION_UP): 4     // KEYCODE_BACK
:Sending Key (ACTION_DOWN): 3   // KEYCODE_HOME
:Sending Key (ACTION_UP): 3     // KEYCODE_HOME
:Sending Key (ACTION_DOWN): 24  // KEYCODE_VOLUME_UP
:Sending Key (ACTION_UP): 24    // KEYCODE_VOLUME_UP
:Sending Key (ACTION_DOWN): 25  // KEYCODE_VOLUME_DOWN
:Sending Key (ACTION_UP): 25    // KEYCODE_VOLUME_DOWN
```

该事件是由一个Key(ACTION_DOWN)和一个Key(ACTION_UP)组成的，点击的就是上面说到的几个系统按键。

### 1.3.9 启动Activity事件

启动Activity事件是指在手机上启动一个Activity的操作。在随机的时间间隔中，Monkey将执行一个startActivity()方法，作为 最大限度上覆盖被测包中全部Activity的一种方法。可通过--pct-appswitch参数来配置其事件百分比。从Monkey执行该事件对外输 出的日志可以看到:

```
:Switch: #Intent;action=android.intent.action.MAIN;category=android.intent. category.LAUNCHER;launchFlags=0x10200000;component=com.android.settings/. Settings;end
// Allowing start of Intent { act=android.intent.action.MAIN cat=[android. intent.category.LAUNCHER] cmp=com.android.settings/.Settings } in package com. android.settings
```

该事件是由一个Switch操作组成的，从手机上看，上面的操作实际是打开了com.android.settings这个应用的一个 com.android.settings.Settings的Activity界面。

### 1.3.10 键盘事件

键盘事件主要是一些与键盘相关的操作。比如点击输入框、键盘弹起、点击输入框以外区域、键盘收回等。可通过--pct-flip

参数来配置其事件百分比。从Monkey执行该事件对外输出的日志可以看到:

```
:Sending Flip keyboardOpen=false
:Sending Flip keyboardOpen=true
```

如日志所示，这里主要是键盘的打开和关闭操作。

### 1.3.11 其他类型事件

其他类型事件包括了除前面提到的10种事件外其他所有的事件，如按键、其他不常用的设备上的按钮等。可通过--pct- anyevent参数来配置其事件百分比。从Monkey执行该事件对外输出的日志可以看到:

```
:Sending Key (ACTION_DOWN): 59    // KEYCODE_SHIFT_LEFT
:Sending Key (ACTION_UP): 59      // KEYCODE_SHIFT_LEFT 
:Sending Key (ACTION_DOWN): 138   // KEYCODE_F8
:Sending Key (ACTION_UP): 138     // KEYCODE_F8
:Sending Key (ACTION_DOWN): 45    // KEYCODE_Q
:Sending Key (ACTION_UP): 45      // KEYCODE_Q
:Sending Key (ACTION_DOWN): 192   // KEYCODE_BUTTON_5
:Sending Key (ACTION_UP): 192     // KEYCODE_BUTTON_5...
```

该事件是由一个Key(ACTION_DOWN)和一个Key(ACTION_UP)组成的，点击的按键就是其他的一些系统按键，如字母按键、数字按键等。因为现在手机很少带字母按键或数字按键，所以这个事件一般使用得比较少。

## 1.4 Monkey启动

Monkey启动方式很简单:先连接被测手机到PC上，然后打开CMD命令行窗口输入对应的adb命令行即可。通过命令行启动Monkey有两种方式:

- 直接PC启动

```
> adb shell monkey [options] <count>
```



- Shell端启动

```
>adb shell
>monkey [options] <count>
```

这两者的区别是，通过PC端启动，Monkey运行日志可以保存在PC上;通过Shell端启动，Monkey运行日志可以保存在手机里

>Monkey启动后会不断地向被测对象发送随机事件流，直到事件执行完毕或者发生异常时才停止。在Monkey运行过程中，即便断开与PC的连接，Monkey依然可以在手机上继续运行。

停止Monkey的方法是:直接杀掉手机上的Monkey进程。具体方法如下:

```
>adb shell ps |grep monkey
```

获取到com.android.commands.monkey的进程ID

```
>adb shell kill pid
```

下面来看一个最简单的Monkey命令行示例:

```
> adb shell monkey -v 10
```

通过该命令启动Monkey后，Monkey向被测手机的Android系统发送10条随机事件流。当启动运行Monkey测试后，手机上会开始执行Monkey测试， 同时在命令行窗口输出日志，执行完成后，可以看到Monkey的日志信息

![image.png](https://i.loli.net/2021/05/19/pgHnoYwGPBF1xv4.png)

## 1.5 Monkey测试方法

### 1.5.1 Monkey测试实例

**1.常规的稳定性测试**

被测程序是Android应用(App)，希望通过Monkey来模拟用户长时间的随机操作，检查被测应用是否会出现异常(应用崩溃或者无响应)。

测试脚本:

```
adb shell monkey -p com.xxx.xxx --pct-touch 40 --pct-motion 25 --pct-appswitch 10 --pct-rotation 5 -s 12358 --throttle 400 --ignore-crashes --ignore-timeouts -v 500000
```

显而易见，这个Monkey测试的命令相比上面提到的要复杂得多，主要是对一些操作事件做了限制，从而减少了Monkey伪随机化的无效操作。这体现在以下几个方面。

1)使用-p参数来制定测试应用的包名(Package)

因为被测程序是一个特定的Android应用程序，需要指定被测程序的包名。指定包名后，Monkey会根据包名找到对应的应用，并启动其main activity，然后执行Monkey测试。

2)使用--pct-xxx参数限制Monkey执行的事件类型和占比

前面已经说了，这个测试的目的是希望模拟用户操作，因此需要让Monkey执行的事件尽可能地接近用户的常规操作，这样才可以 最大限度地发现用户使用过程中可能出现的问题。因此需要对Monkey执行的事件百分比做一些调整。

触摸事件和手势事件是用户最常见的操作，所以通过--pct-touch和--pct-motion将这两个事件的占比调整到40%与25%;目标应用包 含了多个Activity，为了能覆盖大部分的Activity，所以通过--pct-appswitch将Activity切换的事件占比调整到10%;被测应用之前在测试中 出现过不少横竖屏之间切换的问题，这个场景也必须关注，因此通过--pct-rotation把横竖屏切换事件调整到10%。

3)使用-s参数来指定命令执行的seed值

Monkey会根据seed值来生成对应事件流，同一个seed生成的事件流是完全相同的。这里指定了seed值，是为了测试发现问题时，便于进行问题复现。

4)使用--throttle参数来控制Monkey每个操作之间的时间间隔

指定操作之间的时间间隔，一方面是希望能更接近用户的操作场景，正常用户操作都会有一定的时间间隔;另一方面也是不希望 因为过于频繁的操作而导致系统崩溃，尤其是在比较低端的手机上执行测试时。因此通过--throttle设置Monkey每个操作固定延迟0.4 秒。

5)使用--ignore-crash和--ignore-timeouts参数使Monkey遇到意外时能继续执行

在执行Monkey测试时，会因为应用的崩溃或没有响应而意外终止，所以需要在命令中增加限制参数--ignore-crash和--ignore-timeouts，让Monkey在遇到崩溃或没有响应的时候，能在日志中记录相关信息，并继续执行后续的测试。

6)使用-v指定log的详细级别

Monkey的日志输出有3个级别:默认的是level 0，-v日志级别为level 1，-v-v日志级别为level 2。日志的级别越高，其详细程度也越高。为了方便问题的定位，将日志级别设置为level2。

在常规的稳定性测试中，虽然可以自定义各种事件的操作占比，但毕竟是随机事件流。在实际测试过程中，难免会遇到Monkey点了我们不希望它点击的地方，比如误点了工具栏导致网络断开的情况等。后续会介绍如何解决wifi重连的问题。

**2.自定义脚本的稳定性测试**

常规Monkey测试执行的是随机的事件流，但如果只是想让Monkey测试某个特定场景(执行固定的事件流)呢?这时候就需要用 到自定义脚本了，Monkey支持执行用户自定义脚本的测试，用户只需要按照Monkey脚本的规范编写好脚本，存放到手机上，启动 Monkey通过-f scriptfile参数调用脚本即可。

```
#头文件，控制Monkey发送消息的参数，固定写即可
#脚本类型，一般不用更改
type=raw events 
#脚本执行次数，但是由于Monkey命令本身可以指定执行次数，所以这里的设置是不生效的 
count=10
#命令执行速率，速率也可以通过 Monkey命令设置，这里的设置是不生效的
speed=1.0 
#以下为Monkey命令 
start data>>
LaunchActivity( pkg_name,cl_name)
DispatchPress(KEYCODE_HOME)...
```

| API                                                          | 说明                                                         |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| LaunchActivity(Pkg_name,cl_name)                             | 启动被测应用的某个Activity<br/>Pkg_ name:包名<br/>cl name: Activity 名 |
| Tap(x,y,tapDuration)                                         | 模拟一次手指单击事件<br/>X:<br/>点击的横坐标<br/>y:点击的纵坐标 |
| DispatchPress(KeyName)                                       | 模拟按键点击<br/>keyName:按键的名称                          |
| RotateScreen(rotationDegree,peresist)                        | 模拟旋转屏幕<br/>rotationDegree:用0 ~ 3分别表示顺时针旋转的四个方向<br/>peresist:是否存留 |
| DispatchFlip(true/false)                                     | 打开或关闭软键盘                                             |
| LongPress()                                                  | 长按两秒                                                     |
| PressAndHold(x,y,pressDuration)                              | 模拟长按事件:即单指按下一段时间， 再抬起<br/>X:点击的横坐标<br/>y:点击的纵坐标<br/>pressDuration:点击时长，单位是ms |
| DispatchString(input)                                        | 输入字符串<br/>input:输入内容                                |
| Drag(xStrat,yStart,xEnd,yEnd,stepCount)                      | 模拟拖动操作:即单指按下、拖动、放开<br/>xStart、yStart:起始的横坐标和纵坐标<br/>xEnd、yEnd:结束的横坐标和纵坐标<br/>stepCount:移动的事件数(可理解为移动速度) |
| PinchZoom(pt1xStart,pt1yStart,<br/>pt1xEnd,pt1yEnd,pt2xStart,pt2yStart,<br/>pt2xEnd,pt2yEnd,stepCount) | 模拟缩放手势:即两个手指同时按下并移动，再同时放开<br/>pt1xStart、ptlyStart: 手指1起始的横坐标和纵坐标<br/>pt1xEnd、pt1yEnd: 手指1结束的横坐标和纵坐标<br/>pt2xStart、pt2yStart: 手指2起始的横坐标和纵坐标<br/>pt2xEnd、pt2yEnd:手指2结束的横坐标和纵坐标<br/>stepCount:移动的事件数(可理解为移动速度,stepCount越大移动速度越慢) |
| UserWait(sleepTime)                                          | 设置等待时间<br/>sleepTime:等待的时间，单位是ms              |
| DeviceWakeUp()                                               | 唤醒屏幕                                                     |



> Monkey脚本只能通过坐标的方式来定位点击和移动事件的屏幕位置，这里就需要提前获取坐标信息。获取坐标信息的方法很多，最简单的方法就是打开手机中的开发人员选项，打开“显示指针位置”。随后，在屏幕上的每次操作，在导航栏上都会显示坐标信息。

下面来看一个简单的例子:

这里要测试的是“设置”，测试的操作是打开“设置”，点击输入框，输入“”，点击搜索。搜索完成后，点击返回键返回应用宝首页。

首先，将在本地编写需要的测试脚本命名为monkey.script(文件格式无要求)，脚本内容如下

```shell
#启动测试
type = user 
count = 49 
speed = 1.0 
start data >> 


#启动设置
LaunchActivity(com.android.settings,com.android.settings.Settings) 
UserWait(2000)


#点击搜索按钮
Tap(840,820,500) 
UserWait(2000) 


#点击搜索栏
Tap(200,150,1000)


#输入字母“theo”
DispatchString(theo) 
UserWait(2000) 


#点击搜索
Tap(975,2060,1000) 
UserWait(2000) 


#点击返回键返回首页
DispatchPress(KEYCODE_BACK)
```

将文件push到手机或模拟器中，最后，执行脚本

```shell
adb shell monkey -f /sdcard/monkey.script –v 1
```



在手机上我们可以看到，Monkey脚本按照我们的设计的脚本执行了相应的动作。

> 如果需要重复执行某个脚本，只要在Monkey启动命令中修改执行次数即可。例如:
>
> ```shell
> adb shell monkey -f /sdcard/monkey.script –v 10
> ```
>
> 此时Monkey会重复执行monkey.script脚本10次。

**3.结合辅助命令，获取更多信息**

常规测试只要记录下Monkey日志，再分析Monkey日志检查是否有异常即可。但是，很多时候，测试除了想知道执行过程是否有异常，还需要能获取执行过程中的一些详细信息或性能数据，比如想知道Monkey执行过程中是否存在内存泄漏，需要获取内存信息。 这时候就需要借助一些辅助的命令来获取更多信息了。下面列举了几种Monkey测试中常用的辅助命令，使用方法也非常简单，只要在 执行Monkey的同时，另起一个命令行窗口输入对应命令执行即可

- 获取logcat日志信息:

```
adb shell logcat -v time>log.txt
```

- 获取内存信息:

```
adb shell dumpsys meminfo <进程名>
```

- 获取CPU消耗信息:

```
adb shell dumpsys cpuinfo <进程名>
```

- 获取电量信息:

```
adb shell dumpsys battery
```

- 获取GPU信息:

```
adb shell dumpsys gfxinfo <进程名>
```

- 获取流量信息:

```
#某些厂商的设备上无法使用
adb shell cat/proc/uid_stat/<被测应用的uid>/tcp_rcv
```

>如何获取被测应用的UID
>
>步骤1:查看被测应用的进程ID(PID)
>
>```
>adb shell ps | grep <被测应用包名>
>```
>
>步骤2:查看被测应用的用户ID(UID)
>
>```
>adb shell cat /proc/$pid/status
>```

## 1.6 Monkey日志分析

Monkey日志分析是Monkey测试中非常重要的一个环节，通过日志分析，可以获取当前测试对象在测试过程中是否会发生异常，以及发生的概率，同时还可以获取对应的错误信息，帮助定位和解决问题。介绍日志分析方法之前，先来看一下日志的保存方法。

### 1.6.1 Monkey日志的保存方法

Monkey运行日志常见的保存方法有三种:

- 保存在PC中，代码如下:

```
adb shell monkey [option] <count> >/home/monkey.txt
```

- 保存在手机中，代码如下

```
adb shell monkey [option] <count> > /mnt/sdcard/monkey.txt
```

- 标注流与错误流分开保存，代码如下

```
adb shell monkey [option] <count> 1>/sdcard/monkey.txt 2>/sdcard/error.txt
```

> 日志：
>
> ![image.png](https://i.loli.net/2021/06/02/SUMz4BNtmeKsJhR.png)

### 1.6.2 Monkey日志内容分析

Monkey运行时输出的日志一般包含四类信息，分别是测试命令信息、伪随机事件流信息、异常信息、Monkey执行结果信息

1)测试命令信息

Monkey启动后会输出当前所执行命令的各种参数信息，其中包括种子(Seed)信息、事件数量、可运行的应用列表以及各事件百分比等。这些信息都是通过Monkey命令参数所指定的，这部分日志信息的解析，如下所示。

```
//测试命令信息

//随机种子值，执行事件数量
:Monkey: seed=1454215444564 count=10

//可运行的应用列表
:AllowPackage: "com.sec.android.app.popupcalculator"

//Category包含 LAUNCHER和MONKEY
:IncludeCategory: android.intent.category.LAUNCHER 
:IncludeCategory: android.intent.category.MONKEY 

//各事件的百分比
// Event percentages: 

// 0: 15.0% 事件0: --pct-touch

// 1: 10.0% 事件1: --pct-motion

// 2: 2.0% 事件2: --pct-pinchzoom

// 3: 15.0% 事件3: --pct-trackball

// 4: 0.0% 事件4: --pct-rotation

// 5: 0.0% 事件5: --pct-permission

// 5: 25.0% 事件6: --pct-nav

// 6: 15.0% 事件7: --pct-majornav

// 7: 2.0% 事件8: --pct-syskeys

// 8: 2.0% 事件9: --pct-appswitch

// 9: 1.0% 事件10: --pct-flip

// 10: 13.0% 事件11: --pct-anyevent
```

[源代码在这里](https://android.googlesource.com/platform/development/+/refs/heads/master/cmds/monkey/src/com/android/commands/monkey/MonkeySourceRandom.java)

2)伪随机事件流信息 

当Monkey开始执行测试后，会顺序输出执行的事件流信息，主要是前面提到的11大事件。这部分日志信息的解析，如下所示。

```shell
//执行的事件流信息 

//启动App事件
:Switch: #Intent;action=android.intent.action.MAIN;category=android.intent.category.LAUNCHER;launchFlags=0x10200000;component=com.sec.android.app.popupcalculator/.Calculator;end
    // Allowing start of Intent { act=android.intent.action.MAIN cat=[android.intent.category.LAUNCHER] cmp=com.sec.android.app.popupcalculator/.Calculator } in package com.sec.android.app.popupcalculator
:Sending Touch (ACTION_DOWN): 0:(576.0,470.0)
:Sending Touch (ACTION_UP): 0:(576.10016,449.71652)
:Sending Trackball (ACTION_MOVE): 0:(-5.0,-3.0)
:Sending Touch (ACTION_DOWN): 0:(996.0,2189.0)
:Sending Touch (ACTION_UP): 0:(987.718,2137.2007)
:Sending Touch (ACTION_DOWN): 0:(695.0,888.0)
:Sending Touch (ACTION_UP): 0:(697.2413,877.8434)
    // Injection Failed
:Sending Trackball (ACTION_MOVE): 0:(4.0,-4.0)
:Sending Trackball (ACTION_UP): 0:(0.0,0.0)
:Sending Touch (ACTION_DOWN): 0:(1061.0,1427.0)
:Sending Touch (ACTION_UP): 0:(1053.2065,1428.9296)
:Sending Trackball (ACTION_MOVE): 0:(3.0,-1.0)
:Sending Touch (ACTION_DOWN): 0:(83.0,739.0)
:Sending Touch (ACTION_UP): 0:(170.69313,728.6698)
:Sending Flip keyboardOpen=false
Got IOException performing flipjava.io.IOException: write failed: EINVAL (Invalid argument)
    // Injection Failed
:Sending Trackball (ACTION_MOVE): 0:(-3.0,0.0)
:Sending Touch (ACTION_DOWN): 0:(221.0,1004.0)
:Sending Touch (ACTION_UP): 0:(220.8598,1007.04846)
:Sending Trackball (ACTION_MOVE): 0:(-3.0,1.0)
```

3)异常信息

当Monkey执行过程中遇到错误时，会输出对应异常信息，如下所示。

```
//发送 Crash的应用包名和pid
// CRASH: com.tencent.android.qqdownloader (pid 912) 

//Crash的简要信息
// Short Msg: java.lang.ClassNotFoundException 

//Crash的详细信息
// Long Msg: java.lang.ClassNotFoundException: Didn't find class "com. qq.AppService.AstApp" on path DexPathList[[zip file "/data/app/com.tencent. android.qqdownloader-2.apk"],nativeLibraryDirectories[/data/app-lib/com. tencent.android.qqdownloader-2, /vendor/lib, /system/lib]]
//机型和系统信息
// Build Label: Xiaomi/pisces/pisces:4.4.4/KTU84P/5.12.24:user/release-keys 
// Build Changelist: 5.12.24
// Build Time: 1450958964000

//Crash的详细日志
// java.lang.RuntimeException: Unable to instantiate application com. qq.AppService.AstApp: java.lan.ClassNotFoundException: Didn't find class "com. qq.AppService.AstApp" on path: DexPathList[[zip fil "/data/app/com.tencent. android.qqdownloader-2.apk"],nativeLibraryDirectories=[/data/app-lib/com. tecent.android.qqdownloader-2, /vendor/lib, /system/lib]]
// at android.app.LoadedApk.makeApplication(LoadedApk.java:509)
// at android.app.ActivityThread.access$1500(ActivityThread.java:138)
// at dalvik.system.NativeStart.main(Native Method)
// ... 11 more //
```

4)Monkey执行结果信息

当Monkey执行完所有事件后，会输出执行结果信息，其中包括执行的事件数量、旋转的角度、丢失的事件数量、网络状态以及Monkey最终的执行结果，如下所示。

```
//执行的事件数量
Events injected: 100

//旋转的角度为0
:Sending rotation degree=0, persist=false

//丢失的事件数量
:Dropped: keys=0 pointers=1 trackballs=0 flips=1 rotations=0

//网络状态
## Network stats: elapsed time=262ms (0ms mobile, 0ms wifi, 262ms not connected)
// Monkey finished
```

如果Monkey执行过程中出现了异常导致执行失败，会输出对应的执行失败的原因，第几个事件执行失败以及所使用的随机种子数，如下所示。

```
//显示Monkey执行失败
** Monkey aborted due to error.

//执行的事件数量
Events injected: 8

//旋转的角度为0
:Sending rotation degree=0, persist=false /

/丢失的事件数量
:Dropped: keys=0 pointers=0 trackballs=0 flips=0 rotations=0 

//网络状态
## Network stats: elapsed time=405ms (0ms mobile, 0ms wifi, 405ms not connected) 

//提示在执行到第8个事件时出现 Crash，以及所使用的随机种子的值
** System appears to have crashed at event 8 of 100 using seed 1454216848235

```

### 1.6.3 Monkey日志异常信息查找

Monkey执行过程中常见的错误类型主要有两类:应用程序无响应(ANR)和崩溃(Crash)。

 ANR是指当Android系统监测到应用程序在5秒内没有响应输入的事件或广播在10秒内没有执行完毕时抛出无响应提示。

rash是指当应用程序出现错误时导致程序异常停止或退出的情况.

要统计Monkey日志中错误出现的次数也非常简单，只要搜索关键字“ANR”和“CRASH”出现的次数即可。

通过详细日志信息，测试可以定位到引起Crash的原因，以及出现Crash的代码行信息。这里给出常见的一些Crash错误信息。

|               Crash关键字                |  Crash原因   |
| :--------------------------------------: | :----------: |
|      java.lang.NullPointerException      |  空指针异常  |
| java.lang.ArrayIndexOutOfBoundsException |   数组溢出   |
|     java.lang.ClassNotFoundException     |   类不存在   |
|      java.lang.ArithmeticException       | 数学运算异常 |
|   java.lang.IlleagalArgumentException    | 方法参数异常 |
|      java.io.FileNotFoundException       |  文件未找到  |
|     java.lang.NumberFormatException      | 数值转化异常 |
|     java.lang.StackOverflowException     | 堆栈异常错误 |
|      java.lang.OutOfMemoryException      | 内存溢出错误 |

