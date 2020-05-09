---
layout: post
title: CANopenNode学习（1）
date: 2018-3-21
categories: blog
tags: [CANopen,CANopenNode,学习,代码]
description: 学习使用CANopenNode开源库开发CANopen网络。
---
> 学习 CANopenNode 的最终目的是：结合其 API 在 PIC18F 单片机上开发 CANopen 通信节点，并采集、传递和处理信号。  
> 英语及专业水平有限，如有纰漏和表达不周，请及时与我联系，谢谢。

文中使用的 CANopenNode V1.1 源码和文件可以在 [这里下载](https://sourceforge.net/projects/canopennode/files/canopennode/CANopenNode-1.10/)   
新的工程已经转移到 [GitHub](https://github.com/canopennode) 可以支持 PIC32 等更多的 MCU。

由于 v1.1 版本涵盖了 PIC18F 的实例和工程文件，并有详细的手册和使用指南，所以选择 v1.1 版本作为入门学习。下面的章节主要依据 [教程][R1]。

CANopenNode V1.1 采用许可 `GNU Free Documentaion License`.


## 1. 介绍  
通过[教程][R1]，学习使用 CANopenNode 开源库开发 CANopen 网络。 它将显示如何：制作通用输入/输出设备，使用过程数据对象（传输和映射），在对象字典中使用保持性变量，为智能设备制作自己的程序，为NMT主设备使用自定义 CAN 消息，使用紧急消息 针对自定义错误等。

在后面的章节中将介绍如何使用 `panel_with_PIC+LCD+keypad` 或 `Web_interface_with_SC1x` 示例（都包含在 CANopenNode 中）或使用标准配置工具配置网络。

[教程][R1]介绍了 CANopenNode 的大部分功能。 这是相当多的，教程中有很多信息。 源代码已经过测试，所以用户不应该遇到问题。 如果有问题或疑问，请[联系教程原作者][R3]。



### 1.1 准备
1. [CANopenNode 源码](https://sourceforge.net/projects/canopennode/files/canopennode/CANopenNode-1.10/);
2. [MPLAB IDE](http://www.microchip.com);
3. [MPLAB C18 V3.00 +](http://www.microchip.com);
4. 三块带有微控制器 PIC18F458（或其他具有 CAN 的 PIC18F）和 CAN 收发器的电路板;
5. PIC 编程器（使用 [MPLAB ICD2](http://www.microchip.com) 工作正常）。
6. 熟悉使用 MPLAB IDE 和 MPLAB C18。
7. 有关 CANopen 协议的知识（在线培训 [can-cia](http：//www.can-cia.org/canopen/) 或 [esacademy](http://www.esacademy.com/myacademy/)，[书籍](www.canopenbook.com/))。
8. 建议：CANopen 配置工具，如 `panel_with_PIC+LCD+keypad`（CANopenNode 的一部分）或 `Web_interface_with_SC1x` （也是 CANopenNode 的一部分）或任何其他商用 CANopen 配置工具。

### 1.2 说明
网络例程是带有远程传感器和命令接口的空调机组。它由三个 CANopen 设备组成：

图1.1 - 简单的 CANopen 网络  
![][P1-1]

1. 采集单元——独立温度传感器：  
    - 基于通用输入/输出设备配置文件（CiADS401）。
    - 在每次状态改变时传输感测温度，并定期使用事件定时器（TPDO 1）。
    - 每秒产生一次心跳。
2. 动力装置——由加热器和冷却器制成的空调单元：
    - 从传感器接收温度（RPDO 0）。
    - TempLo 和TempHi 变量位于「对象字典」中。 可以通过 SDO 通信对象使用 CANopen 配置工具访问和更改它们。变量具有保持性（关机后不丢失）。 单位是[℃ 摄氏度]。
    - 打开或关闭冷却器或加热器。决定基于来自传感器，TempLo 和 TempHi 的温度。 如果设备未处于运行状态，则冷却器和加热器将关闭。
    - 在每次状态改变时传送冷却器和加热器的状态，并周期性地使用事件定时器（TPDO 0）。
    - 每秒产生一次心跳。
    - 监测来自传感器和命令接口的心跳。
3. 操作单元——一个按钮和两个 LED，可选 LCD 显示器：
    - 接收来自传感器（RPDO 0）的温度并将其显示在 LCD 上（可选）。
    - 从电源单元（RPDO 1）接收冷却器和加热器的状态，并将其显示在 LED 上。
    - 与按钮连接的 NMT 主设备。如果按下按钮，则所有网络将被设置为预操作`pre-operational`状态，并且如果再次按下按钮，则所有网络将被设置为可操作的 NMT 状态。如果更长时间（5 秒）按下按钮，则网络上的所有节点都将重置。
    - 每秒产生一次心跳。

除上述通信对象外，每个节点还使用 SDO，紧急和 NMT（从）通信对象。

## 2. 硬件介绍

例程系统的硬件由 Microchip PIC18F458 微控制器，CAN 收发器和一些状态 LED 二极管或按钮组成。可以使用 CAN 收发器 Microchip MCP 2551 或 Philips PCA82C250（图 2.1）。采用 PLLx4 模式的 8 MHz 振荡器将用于在 PIC 上获得 32 MHz。 如果使用其他振荡器频率或其他用于 CAN 二极管的引脚，则必须编辑 *CO_driver.h* 文件。

图 2.1 CAN 收发器与 PIC MCU 的连接  
![][P2-1]

<u>详细说明：</u>  
1. 采集单元 - 节点号 6
    - RB4 和 GND 之间接 LED - 绿色（CAN Run led），
    - RB5 和 GND 之间接 LED - 红色（CAN Error led），
    - +5V，GND 和 RA0 之间接 5 kΩ 电位器（模拟温度传感器）。
2. 动力装置 - 节点号 7：
    - RB4 和 GND 之间接 LED - 绿色（CAN Run led），
    - RB5 和 GND 之间接 LED - 红色（CAN Error led），
    - RC4 和 GND 之间接 LED - 黄色（冷却器开启）;
    - RC5 和 GND 之间接 LED - 黄色（加热器开启）。
3. 操作单元-节点号8：
    - RB4 和 GND 之间接 LED - 绿色（CAN Run led）;
    - RB5 和 GND 之间接 LED - 红色（CAN Error led）;
    - RC4 和 GND 之间接 LED - 黄色（冷却器状态）;
    - RC5 和 GND 之间接 LED - 黄色（加热器状态）;
    - RC6 和 VCC 之间接按钮带下拉电阻（NMT 主控）;
    - LCD 显示温度（这里没有实现，预留了可用的变量）。

## 3. MCU 编程

阅读该章节需要了解 MPLAB IDE 和 MPLAB C18 编译器的相关知识。这两个程序都必须正确安装和配置。

### 3.1 源代码

首先从 1.1 中的链接下载 *CANopenNode-V1.10.zip*。解压文件到 C 盘。

压缩包内已经为 Microchip MPLAB C18 编写了三个工程： *Tutorial_Sensor*，*Tutorial_Power* 和 *Tutorial_Command*。所有三个工程都使用相同的库文件。每个工程都可以用 MPLAB IDE 打开。每个工程的编译都必须不能包含错误或警告。

*Project>Build options>project>general* 中的文件夹设置如图 3.1 所示。

图 3.1 路径设置  
![][P3-1]

完整 Include 路径是：

    C：\MCC18\h; 
    C：\CANopenNode\_src\CANopen; 
    C：\CANopenNode\_src\CANopen\PIC18_with_Microchip_C18; 
    C：\CANopenNode\_src\Tutorial_Sensor


每个工程文件夹包括以下文件：

文件            |说明  
----------------|-----  
lesser.txt |许可文件   
CANopen\CANopen.h |main 头文件  
CANopen\CO_errors.h |错误定义   
CANopen\CO_stack.c |main 代码  
CANopen\CO_OD.txt |对象字典说明  
CANopen\PIC18_with_Microchip_C18\CO_driver.h |驱动头文件  
CANopen\PIC18_with_Microchip_C18\CO_driver.c |处理器特定代码  
CANopen\PIC18_with_Microchip_C18\main.c |main()与中断函数  
CANopen\PIC18_with_Microchip_C18\memcpyram2flash.h |写 flash  
CANopen\PIC18_with_Microchip_C18\memcpyram2flash.c |写 flash  
CANopen\PIC18_with_Microchip_C18\CONFIG18f458.c |芯片配置位 - 可选  
CANopen\PIC18_with_Microchip_C18\18f458i.lkr |默认链接脚本  
Tutorial_xxx\CO_OD.h |对象字典头文件  
Tutorial_xxx\CO_OD.c |对象字典  
Tutorial_xxx\user.c |用户代码  
Tutorial_xxx\Tutorial_xxx.eds |EDS 文件  


### 3.2 振荡器和 CAN LED 设置

默认情况下，所有 MCU 都运行在 32 MHz。这是通过 PIC18Fxxx 微控制器中的 8MHz 外部晶振和 PLLx4 模式实现的。如果使用 *CONFIG18f458.c* 文件，则利用 `#pragma config OSC = HSPLL` 设置 PLLx4 模式，另外也可以在 MPLAB IDE 中转到 *Configure>Configuration bits* 并设置 OSC ： `HS Oscillator, PLL Enable`。如果更改了 PLLx4 模式设置，则微控制器必须重新上电，更改方可生效。

若采用其它频率的振荡器则打开 *CO_driver.h* 并更改宏 `CO_OSCILATOR_FREQ`。另一种方法是在每个工程文件中添加一个宏定义： *Project>Build options>project>MPLAB C18>Macro Definitions>Add...* 并写入 `CO_OSCILATOR_FREQ xx`，其中 `xx` 是振荡器频率，对于每个工程可以不同。

默认情况下，绿色 LED （CAN Run led）位于 RB4 上，红色 LED （CAN Error Error led）位于 RB5 上。引脚在 *CO_driver.h* 文件中定义，与振荡器的配置在同一部分。引脚也可以在那里改变。如果需要禁用 LED，请禁用宏。例如可以将其注释掉：  

```c
#define PCB_RUN_LED_INIT()  //{TRISBbits.TRISB4 = 0; PORTBbits.RB4 = 0;}
#define PCB_RUN_LED(i)      //PORTBbits.RB4 = i
```

### 3.3 采集单元编程

**采集单元（Sensor）基于 Example\_generic\_IO 。** 所有的差异都会标出来。Example_generic_IO 基于 _blank_project 和 CiADS401 ——通用输入/输出模块的 CANopen 设备配置文件。采集单元将使用 ADC 转换器和 PDO 对象通过 CAN 发送模拟量值。

以下是 CiADS401 的简短摘录：

> OD，0x6000： 读 DI - 映射到 TPDO 0 的 64 位。  
> OD，0x6200： 写 DO - 映射到 RPDO 0 的 64 位。  
> OD，0x6401： 读 AI - 将 12x16 位值映射到 TPDO 1...3。  
> OD，0x6411： 写 AO - 将 12x16 位值映射到 RPDO 1...3。  

这里将只使用 TPDO 1 的两个字节，从索引 0x6401 子索引 0x01 映射。

打开 *Tutorial\_Sensor* 工程。

#### 3.3.1 配置工程 - CO_OD.h 文件

代码以等宽字体显示。与 Example_generic_IO 的差异已标出。为了更好地理解源代码可以阅读源文件中每行的注释。

*Setup CANopen* 部分代码：

```c
#define CO_NO_SYNC              0  //<<
#define CO_NO_EMERGENCY         1  
#define CO_NO_RPDO              0  //<<
#define CO_NO_TPDO              2  //<<
#define CO_NO_SDO_SERVER        1  
#define CO_NO_SDO_CLIENT        0  
#define CO_NO_CONS_HEARTBEAT    0  //<<
#define CO_NO_USR_CAN_RX        0  
#define CO_NO_USR_CAN_TX        0  
#define CO_MAX_OD_ENTRY_SIZE    20  
#define CO_SDO_TIMEOUT_TIME     10  
#define CO_NO_ERROR_FIELD       8  
#define CO_PDO_PARAM_IN_OD
#define CO_PDO_MAPPING_IN_OD
#define CO_TPDO_INH_EV_TIMER
#define CO_VERIFY_OD_WRITE
#define CO_OD_IS_ORDERED
#define CO_SAVE_EEPROM
#define CO_SAVE_ROM
```

在本节中可以定义 CANopenNode 中使用的特定对象的数量。相同的功能可能被禁用。

在我们的案例中，不使用 SYNC 对象，不接收 PDO，不使用 TPDO 0（数字输入），TPDO 1（两字节）用于传输温度传感器的模拟值，不监控其他节点。

*Device profile for Generic I/O* 部分配置代码：

```c
//#define CO_IO_DIGITAL_INPUTS      //4 * 8 位数字量输入  
//#define CO_IO_DIGITAL_OUTPUTS     //4 * 8 位数字量输出  
#define CO_IO_ANALOG_INPUTS         //8 * 16 位模拟量输入  
//#define CO_IO_ANALOG_OUTPUTS      //2 * 16 位模拟量输出
 ```

*Default values for object dictionary* 部分配置代码： 

````c
#define ODD_PROD_HEARTBEAT  1000  
//...
#define ODD_ERROR_BEH_COMM  0x01    //<<  
#define ODD_NMT_STARTUP     0x00000000L    
````

心跳对象将每秒发送一次。

如果错误寄存器中的通讯错误位（索引 1001）被置位并且设备将处于 Operational NMT 状态，则设备将维持 Operational 状态。 默认行为是通信错误强制设备处于 Pre-Operational NMT状态。

在启动设备时进入 NMT 操作状态。

*0x1800 Transmit PDO parameters* 部分配置代码：

```c
#define ODD_TPDO_PAR_COB_ID_0   0
#define ODD_TPDO_PAR_T_TYPE_0   255
#define ODD_TPDO_PAR_I_TIME_0   0
#define ODD_TPDO_PAR_E_TIME_0   0
#define ODD_TPDO_PAR_COB_ID_1   0
#define ODD_TPDO_PAR_T_TYPE_1   255
#define ODD_TPDO_PAR_I_TIME_1   1000    //<<
#define ODD_TPDO_PAR_E_TIME_1   60000   //<<
```

PDO 0 不会被发送（因为 CO_IO_DIGITAL_INPUTS 被禁用）。PDO 1 COB-ID（11 位 CAN 标识符）将为默认值 —— 0x280 + Node ID。PDO 1的抑制时间（Inhibit time）为1000*100 μs，因此 PDO 1 更新将不会比间隔 100 ms 更快。PDO 1 将在状态更改和每分钟（60000 ms）时发送 - 传输类型为 255 - 设备配置文件特定。

*0x1A00 Transmit PDO mapping for PDO 1* 部分代码:

```c
#define ODD_TPDO_MAP_1_1    0x64010110L
#define ODD_TPDO_MAP_1_2    0x64010200L //<<
#define ODD_TPDO_MAP_1_3    0x64010300L //<<
#define ODD_TPDO_MAP_1_4    0x64010400L //<<
#define ODD_TPDO_MAP_1_5    0x00000000L
#define ODD_TPDO_MAP_1_6    0x00000000L
#define ODD_TPDO_MAP_1_7    0x00000000L
#define ODD_TPDO_MAP_1_8    0x00000000L
```

只在 TPDO 1 中发送一个模拟量，所以在 CAN 总线上只有两个字节的数据。PDO 长度是根据 CANopenNode 中的映射自动计算的。在每次状态改变时，从变量 `ODE_Read_Analog_Input [0]`（位于 OD 中，索引 = 0x6401，子索引 = 1，长度 = 0x10 字节）复制数据。要了解它是如何工作的，看一下来自文件
*user.c* 的示例代码，*Write TPDOs* 部分：

```c
if(CO_TPDO_InhibitTimer[1] == 0 && (
   CO_TPDO(1).WORD[0] != ODE_Read_Analog_Input[0])){

    CO_TPDO(1).WORD[0] = ODE_Read_Analog_Input[0];
    if(ODE_TPDO_Parameter[1].Transmission_type >= 254)
        CO_TPDOsend(1);
}
```

*Default values for user Object Dictionary Entries* 部分:

```c
    #define ODD_CANnodeID   0x06
    #define ODD_CANbitRate  3
```

采集单元的节点 ID 是 6，CAN 比特率是 125 kbps。

#### 3.3.2 应用程序接口 - User.c 文件

代码以等宽字体显示。与 Example_generic_IO 的差异已标出。

头文件：

```c
#include <adc.h>    //<<
```

*User_Init* 函数的更改：

```c
void User_Init(void){
    // ...
    #ifdef CO_IO_ANALOG_INPUTS
    OpenADC(ADC_FOSC_RC & ADC_RIGHT_JUST & ADC_1ANA_0REF,
            ADC_CH0 & ADC_INT_OFF); //<<
    ConvertADC();
    ODE_Read_Analog_Input[0] = 0;
    ODE_Read_Analog_Input[1] = 0;
    ODE_Read_Analog_Input[2] = 0;
    ODE_Read_Analog_Input[3] = 0;
    ODE_Read_Analog_Input[4] = 0;
    ODE_Read_Analog_Input[5] = 0;
    ODE_Read_Analog_Input[6] = 0;
    ODE_Read_Analog_Input[7] = 0;
    #endif
    // ...
}
```

*User_Process1msIsr function, section Read from Hardware* 更改：

```c
//CHANGE THIS LINE -> ODE_Read_Digital_Input.BYTE[0] = port_xxx 
//CHANGE THIS LINE -> ODE_Read_Digital_Input.BYTE[1] = port_xxx  
//CHANGE THIS LINE -> ODE_Read_Digital_Input.BYTE[2] = port_xxx  
//CHANGE THIS LINE -> ODE_Read_Digital_Input.BYTE[3] = port_xxx  
/* 添加 */  
if(BusyADC() == 0){
    ODE_Read_Analog_Input[0] = ReadADC() >> 2; //8 bit value
    ConvertADC();
}
```

函数每 ms 执行一次。

Eds 文件是一个文本文件，可以用于 CANopen 监视器。它现在有一些开销，因为我们没有使用数字输入和输出。

到这里采集单元部分已经结束了。你可以读取 *user.c* 文件，因为它是用户代码和 CANopenNode 协议栈之间的连接点。下一回，我们将从 `blank` 文件开始。您还可以检查 Example_generic_IO 和 _blank_project 文件夹中文件之间的差异。[winmerge][R4] 是一个非常合适的工具。

#### 3.3.3 编译，下载和测试

编译工程并下载到 PIC MCU。现在将 MCU 断电并重新上电，以确保 PLLx4 模式启用。先不要连接到网络。上电后绿色的 *Green CAN run led* 应该点亮。这意味着 NMT 的运行状态是Operational。红色的 *Red CAN error led* 应该闪烁一次，这意味着，CAN 总线是被动的。如果您在 CAN_LO 和 CAN_HI 信号上短路，则红色 *Red CAN error led* 灯将点亮，这表明 CAN 总线关闭。由于通讯错误，传感器并不会离开 Operational 状态。在我们的方案中，这是正常的。

---
2018/3/22更新，下面的部分见[CANopenNode学习（2)][L2]

[L1]:https://syauo.github.io/2018/03/21/CANopenNode-learn-1/
[L2]:https://syauo.github.io/2018/03/22/CANopenNode-learn-2/
[L3]:https://syauo.github.io/2018/03/23/CANopenNode-learn-3/

[R1]:https://sourceforge.net/projects/canopennode/files/canopennode/CANopenNode-1.10/ "《CANopenNode Turorial》  V1.10"  
[R2]:https://sourceforge.net/projects/canopennode/files/canopennode/CANopenNode-1.10/ "《CANopenNode Manual》 V1.10"  
[R3]:mailto:janez.paternoster@siol.net "作者 Janez Paternoster"
[R4]:http://www.winmerge.org/ "文件比较工具"

[P1-1]:https://us1.myximage.com/2018/03/22/1184ad33502524a63969c282b9040d7f.png "简单的CANopen网络"
[P2-1]:https://us1.myximage.com/2018/03/22/505b807b391ee5b3b38ae4a5c772b07a.png "CAN收发器与PIC MCU的连接"
[P3-1]:https://us1.myximage.com/2018/03/22/b34e867948786dfb5afd0e0824a6152c.png "路径设置"