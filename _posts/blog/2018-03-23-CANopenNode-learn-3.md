---
layout: post
title: CANopenNode学习（3）
date: 2018-3-23
categories: blog
tags: [CANopen，CANopenNode，学习，代码]
description: 学习使用CANopenNode开源库开发CANopen网络。
---
> 学习 CANopenNode 的目的是：了解其工作流程和配置方法，结合 API 在 PIC18F 单片机上开发 CANopen 通信节点，并采集、传递和处理信号。  
> 英语及专业水平有限，如有纰漏和表达不周，请及时与我联系，谢谢。

文中使用的 CANopenNode V1.1 源码和文件可以在 [这里下载](https://sourceforge.net/projects/canopennode/files/canopennode/CANopenNode-1.10/)   
新的工程已经转移到 [GitHub](https://github.com/canopennode) 可以支持 PIC32 等更多的 MCU。

由于 v1.1 版本涵盖了 PIC18F 的实例和工程文件，并有详细的手册和使用指南，所以选择 v1.1 版本作为入门学习。下面的章节主要依据 [教程][R1]。

CANopenNode V1.1 采用许可 `GNU Free Documentaion License`.

接上文：   
- [CANopenNode学习（1）][L1]  
- [CANopenNode学习（2）][L2]

前面两部分主要介绍了 CANopenNode 的基本配置方法，并选择了具有三个节点的典型案例进行说明。并对采集单元和动力装置部分的程序进行了解析，该页面主要进行剩下的工作，即第三个节点——操作单元的编程。

### 3.5 操作单元的编程

操作单元基于空白工程 *\_blank\_project*。所有的差异已经标出。它的作用是显示加热器和冷却器的状态及当前温度值。它也是 **NMT Master**，可以改变其它节点的运行状态。

打开工程文件 *Tutorial\_Command*

#### 3.5.1 配置工程 - CO_OD.h 文件

代码以等宽字符显示，与 *\_blank\_project* 不同的地方已标出。我们将尽可能地简化这个装置，所占存储空间也会减小。

*Setup CANopen* 部分代码：

```c
#define CO_NO_SYNC              0   //<<
#define CO_NO_EMERGENCY         1
#define CO_NO_RPDO              2   //<<
#define CO_NO_TPDO              0   //<<
#define CO_NO_SDO_SERVER        1
#define CO_NO_SDO_CLIENT        0
#define CO_NO_CONS_HEARTBEAT    0   //<<
#define CO_NO_USR_CAN_RX        0
#define CO_NO_USR_CAN_TX        1   //<<
#define CO_MAX_OD_ENTRY_SIZE    4   //<<
#define CO_SDO_TIMEOUT_TIME     10
#define CO_NO_ERROR_FIELD       8
//#define CO_PDO_PARAM_IN_OD         //<<
//#define CO_PDO_MAPPING_IN_OD       //<<
//#define CO_TPDO_INH_EV_TIMER       //<< 
//#define CO_VERIFY_OD_WRITE         //<<
#define CO_OD_IS_ORDERED
#define CO_SAVE_EEPROM
//#define CO_SAVE_ROM                //<<
```

未使用 SYNC 对象，使用 2 个RPDO，一个用来接收采集单元发送的温度值，另一个用来接收动力装置发送的状态。未使用 TPDO 和监控。

创建 NMT 主机时将用到一个自定义的 CANtx 消息。

对象字典中的变量最大限制为 4 个字节，因此不用使用SDO的分段传输。

PDO 参数不在对象字典中存在。它们仍将作为变量出现，但不会被访问。闪存占用将会减小，因为变量不会被添加到 `CO_OD[]` 数组中。

未使用映射，变量也不存在。在这种情况下，如果我们有 TPDO，它的数据长度将固定为 8 个字节。由于我们有 RPDO，所以任何数据长度都可以被接受（accepted）。

宏 `CO_SAVE_ROM` 被禁用，所以 SDO 客户端将无法写入位于闪存中的变量。例如，位于索引 0x1017 处的 `Producer_Heartbeat_Time` 将不可写。
出于这个原因，我们也可以从项目中删除文件 *memcpyram2flash.h* 和 *memcpyram2flash.c* 。

*Default values for object dictionary*   
「对象字典默认值」部分的代码保持缺省。

*0x1400 Receive PDO parameters*  
「接收 PDO 参数」部分代码：

```c
#define ODD_RPDO_PAR_COB_ID_0   0x40000286L //<<
#define ODD_RPDO_PAR_T_TYPE_0   255     
#define ODD_RPDO_PAR_COB_ID_1   0x40000187L //<<
#define ODD_RPDO_PAR_T_TYPE_1   255
```

该节点将直接从采集单元（COB-ID = 0x286）和动力装置（COB-ID = 0x187）接收 PDO。

*Default values for user Object Dictionary Entries*  
「用户对象字典入口默认值」部分代码：

```c
#define ODD_CANnodeID   0x08    //<<
#define ODD_CANbitRate  3
```

操作单元的 Node-ID 为 8，CAN 比特率为 125 kbps。

#### 3.5.2 应用程序接口 - User.c 文件

代码以等宽字符显示，与 *Example\_generic* 不同的地方已标出。

头文件中的一些定义：

```c
#include "CANopen.h"
#define RemoteTemperature   CO_RPDO(0).WORD[0]  //<<
#define STATUS_COOLER       CO_RPDO(1).BYTEbits[0].bit0  //<<
#define STATUS_HEATER       CO_RPDO(1).BYTEbits[0].bit1  //<<
#define DigOut_COOLER_IND   PORTCbits.RC4  //<<
#define DigOut_HEATER_IND   PORTCbits.RC5  //<<
#define DigInp_Button       PORTCbits.RC6  //<<
#define NMT_MASTER          CO_TXCAN_USER
```

更改 *User\_Init* 函数：

```c
void User_Init(void){
    ODE_EEPROM.PowerOnCounter++;

    TRISCbits.TRISC4 = 0; PORTCbits.RC4 = 0;    //<<
    TRISCbits.TRISC5 = 0; PORTCbits.RC5 = 0;    //<<
}
```

更改 *User\_ResetComm* 函数：

```c
void User_ResetComm(void){
    // 为定制的 NMT 主消息准备变量。
    // CO_IDENT_WRITE(CAN_ID_11bit, RTRbit/*usually 0*/) 的参数
    CO_TXCAN[NMT_MASTER].Ident.WORD[0] = CO_IDENT_WRITE(0, 0);  //<<
    CO_TXCAN[NMT_MASTER].NoOfBytes = 2; //<<
    CO_TXCAN[NMT_MASTER].NewMsg = 0;    //<<
    CO_TXCAN[NMT_MASTER].Inhibit = 0;   //<<
}
```

更改 *User\_Process1msIsr* 函数：

```c
/* 所有都已更改 */
unsigned int Temperature = 0;

void User_Process1msIsr(void){
    static unsigned int DeboucigTimer = 0;
    if(DigInp_Button) DeboucigTimer++;
    else DeboucigTimer = 0;
    switch(DeboucigTimer){
        case 1000: // 按键按住 1 秒
            if(CO_NMToperatingState == NMT_OPERATIONAL){
                CO_TXCAN[NMT_MASTER].Data.BYTE[0] = NMT_ENTER_PRE_OPERATIONAL;
                CO_NMToperatingState = NMT_PRE_OPERATIONAL;
            }
            else{
                CO_TXCAN[NMT_MASTER].Data.BYTE[0] = NMT_ENTER_OPERATIONAL;
                CO_NMToperatingState = NMT_OPERATIONAL;
            }
            CO_TXCAN[NMT_MASTER].Data.BYTE[1] = 0; // 所有节点
            CO_TXCANsend(NMT_MASTER);
            break;
        case 5000: // 按键按住 5 秒
            //reset all remote nodes
            CO_TXCAN[NMT_MASTER].Data.BYTE[0] = NMT_RESET_NODE;
            CO_TXCAN[NMT_MASTER].Data.BYTE[1] = 0; // 所有节点
            CO_TXCANsend(NMT_MASTER);
            break;
        case 5010: // 按键按住 5 秒 + 10 毫秒
            CO_Reset(); // 复位该节点
    }
    // 显示变量
    DigOut_COOLER_IND = STATUS_COOLER;
    DigOut_HEATER_IND = STATUS_HEATER;
    Temperature = RemoteTemperature;
}
```

函数每毫秒执行一次。

#### 3.3.3 编译、下载和测试

编译工程文件并下载到 PIC MCU。给MCU断电并重新上电，用以确保 PLLx4 使能。先不要连接到网络。在上电以后绿色的 *Green CAN run led* 应以 2.5 Hz 的频率闪烁。这意味着当前操作状态是 *Pre-Operational*。红色 *Red CAN error led* 应该闪烁一次。意味着 CAN 总线是被动的。

## 4 连接并测试网络

现在可以将所有的三个节点连接到 CANopen 网络了，如图 4.1 所示。电源是可选的。线材采用双绞线。

图 4.1：三个节点的CANopen网络连接  
![][P4-1]

上电以后，所有节点上的 *CAN run led* 都必须是亮的。*CAN error led* 必须是灭的。

网络现在已经运行。当左右调整采集单元上的电位器时，动力装置和操作单元上的加热器和冷却器的 LED 指示应点亮或熄灭。如果此时按住按键 1 秒，整个网络将在 Operational（绿色 LED 点亮）和 Pre-Operational（绿色 LED 闪烁）之间切换。如果按住按键 5 秒，网络上所有的节点都会复位。

只有三个节点都在 Operational 状态时，动力装置上的加热器和冷却器才会工作。

你可能会发现，操作单元上的加热器和冷却器指示灯可以点亮，而动力装置并没有，网络处于 Pre-Operational 状态。在操作单元中没有做任何额外的检查。你可能会看到，在使用心跳消费者的动力装置中如何解决这个问题。

现在尝试切断采集单元的电源。动力装置将进入 Pre-Operational 状态，并切断冷却器和加热器。红色 LED 闪烁两次——心跳消费者错误。发送紧急消息 8130。了解更多关于紧急消息可阅读 *CO_errors.h* 文件。错误历史可以从对象字典读取（索引 1003）。

示例运行跟踪如下。第一列是时间戳（秒），第二列是十六进制格式的11位CAN-ID，第三列是数据长度，接下来的是十六进制格式的CAN数据字节。

```
195.878 0x708 0x01 0x00 //操作单元启动
195.879 0x707 0x01 0x00 //动力装置启动
195.880 0x706 0x01 0x00 //采集单元启动
195.881 0x286 0x02 0xFF 0x00 //来自采集单元的温度
196.879 0x708 0x01 0x05 //操作单元心跳(operational)
196.880 0x187 0x01 0x00 //动力装置状态
196.881 0x707 0x01 0x05
196.881 0x706 0x01 0x05
196.891 0x187 0x01 0x01 //动力装置状态(状态改变)
197.879 0x708 0x01 0x05
197.881 0x187 0x01 0x01 //动力装置状态(定时器事件)
197.881 0x707 0x01 0x05
197.882 0x706 0x01 0x05
198.880 0x708 0x01 0x05
198.882 0x187 0x01 0x01
...
201.270 0x286 0x02 0x54 0x00 //来自采集单元的温度(状态改变)
201.371 0x286 0x02 0x46 0x00 //来自采集单元的温度(Inhibit 时间)
201.471 0x286 0x02 0x37 0x00
201.571 0x286 0x02 0x26 0x00
201.671 0x286 0x02 0x14 0x00
201.673 0x187 0x01 0x00
201.771 0x286 0x02 0x01 0x00
201.772 0x087 0x08 0x05 0x10 0x00 0x14 0x01 0x00 0x00 0x00 //紧急 - Low temp.
201.773 0x187 0x01 0x02
201.871 0x286 0x02 0x00 0x00
201.883 0x708 0x01 0x05
201.884 0x187 0x01 0x02
201.884 0x707 0x01 0x05
201.885 0x706 0x01 0x05
202.645 0x286 0x02 0x01 0x00
202.745 0x286 0x02 0x07 0x00
202.746 0x087 0x08 0x00 0x00 0x00 0x14 0x07 0x00 0x00 0x00 //紧急 – Error Reset
202.845 0x286 0x02 0x0F 0x00
202.883 0x708 0x01 0x05
202.885 0x187 0x01 0x02
202.885 0x707 0x01 0x05
202.886 0x706 0x01 0x05
202.945 0x286 0x02 0x17 0x00
202.947 0x187 0x01 0x00
203.045 0x286 0x02 0x1D 0x00
...
208.888 0x708 0x01 0x05
208.889 0x187 0x01 0x01
208.890 0x707 0x01 0x05
208.890 0x706 0x01 0x05
209.060 0x000 0x02 0x80 0x00 //NMT 主机——所有节点进入 Pre-Operational
209.889 0x708 0x01 0x7F
209.891 0x707 0x01 0x7F
209.891 0x706 0x01 0x7F
...
216.834 0x000 0x02 0x01 0x00 //NMT 主机——所有节点进入 Operational
216.894 0x708 0x01 0x05
216.895 0x187 0x01 0x01
216.896 0x707 0x01 0x05
216.896 0x706 0x01 0x05
...
221.900 0x706 0x01 0x05
222.613 0x706 0x01 0x00 //采集单元复位
222.614 0x286 0x02 0xFF 0x00
222.899 0x708 0x01 0x05
222.900 0x187 0x01 0x01
222.900 0x707 0x01 0x05
223.403 0x087 0x08 0x30 0x81 0x10 0x0E 0x00 0x00 0x00 0x40 //来自动力装置的紧急消息
223.614 0x706 0x01 0x05 //unit, Heartbeat Consumer
223.900 0x708 0x01 0x05
223.901 0x707 0x01 0x7F
224.615 0x706 0x01 0x05
224.901 0x708 0x01 0x05
224.902 0x707 0x01 0x7F
...
231.998 0x000 0x02 0x81 0x00 //NMT 主机——所有节点复位
231.999 0x706 0x01 0x00
232.000 0x707 0x01 0x00
232.000 0x286 0x02 0xFF 0x00
232.009 0x708 0x01 0x00
233.000 0x706 0x01 0x05
233.001 0x187 0x01 0x00
233.001 0x707 0x01 0x05
233.010 0x708 0x01 0x05
233.011 0x187 0x01 0x01
234.001 0x706 0x01 0x05
```

使用CANopen配置工具可以配置网络。例如，可以更改变量TempHi。图 4.2 显示了来自 Port 的 CANopen Device Monitor。下面显示的是用这样的工具读写TempHi。网络处于运行状态。关机后更改仍然存在。 协议描述见标准。

```
//Configuration tool requests reading from node-id=7, index 3001, sub0
---.--- 0x607 0x08 0x40 0x01 0x30 0x00 0x00 0x00 0x00 0x00
// Power unit responses: node-id=7, index 3001, sub0, data 2 bytes long = 0x19
---.--- 0x587 0x08 0x4b 0x01 0x30 0x00 0x19 0x00 0x00 0x00
//Configuration tool request write to node-id=7, index 3001, sub0, value 1E, 2 bytes
---.--- 0x607 0x08 0x2b 0x01 0x30 0x00 0x1e 0x00 0x00 0x00
// Power unit responses: write successful
---.--- 0x587 0x08 0x60 0x01 0x30 0x00 0x00 0x00 0x00 0x00
```

图 4.2：CANopen Device Monitor  
![][P4-2]

## 5 用 PIC + LCD + 键盘的用户界面 

你可以尝试示例 *Example\_panel\_with\_PIC+LCD+keypad*。它就像一个拥有 LCD 显示和数字键盘的命令界面。它集成了 SDO client。它是为这个教程准备的，具有更高 `TempLo` 和 `TempHi` 的菜单，带密码保护，一些系统菜单和一个用于访问所有节点对象字典的菜单。

hex 文件可以用于 PIC18F458 带 8 MHz 晶振（PLLx4 模式启用）。构建源代码时，必须使用完全优化。详细说明在[手册][R2]中。

## 6 以太网用户界面

Example_web_interface_with_SC1x 与 18F 系列无关，略。

## 7 结论

现在您可以自行探索 CANopenNode 了。教程中几乎显示了所有功能。也可以阅读手册，在手册中您可以找到 CANopenNode 源代码的详细说明。您可以使用最少的编程编写代码，如教程中所述。可以更改 CANopenNode 以适应您的需求，因为它足够灵活。

希望 CANopenNode 可以用于很多项目。项目的质量主要取决于程序员的经验。高质量项目还需具有可靠的硬件，包含文档和查找错误的系统，这些错误可能发生在设备或网络上。（错误总是发生，通常有一些不合理的原因）。CANopenNode 不应该是错误的来源，但它可以作为跟踪它们的基础。如有任何问题，您可以随时联系[原作者][R4]。

CANopenNode ~~目前~~很久以前是针对具有 Microchip C18 编译器的 8 位 PIC18F458 以及具有 Borland C5.02 编译器的 16 位 SC1x 而编写的。将代码移植到其他系统并不困难。编写了许多示例，包括教程，通用 I/O 的设备配置文件，用户界面等。欢迎其他系统上的用户为其微控制器移植此代码。

[完]



    2018/3/26 更新


[L1]:https://syauo.github.io/2018/03/21/CANopenNode-learn-1/  
[L2]:https://syauo.github.io/2018/03/22/CANopenNode-learn-2/  
[L3]:https://syauo.github.io/2018/03/23/CANopenNode-learn-3/  

[R1]:https://sourceforge.net/projects/canopennode/files/canopennode/CANopenNode-1.10/ "《CANopenNode Turorial》  V1.10"  
[R2]:https://sourceforge.net/projects/canopennode/files/canopennode/CANopenNode-1.10/ "《CANopenNode Manual》 V1.10"  
[R3]:mailto:janez.paternoster@siol.net "作者 Janez Paternoster"
[R4]:http://www.winmerge.org/ "文件比较工具"
[P1-1]:https://us1.myximage.com/2018/03/22/1184ad33502524a63969c282b9040d7f.png "简单的CANopen网络"
[P2-1]:https://us1.myximage.com/2018/03/22/505b807b391ee5b3b38ae4a5c772b07a.png "CAN收发器与PIC MCU的连接"
[P4-1]:https://us1.myximage.com/2018/03/23/c20cbfc03670fbc8a0361795ba7f9e09.png "三个节点的CANopen网络连接"

[P3-1]:https://us1.myximage.com/2018/03/22/b34e867948786dfb5afd0e0824a6152c.png "路径设置"

[P4-2]:https://us1.myximage.com/2018/03/26/ca394b0172994644aa633bd97d866447.png "CANopen Device Monitor"