---
layout: post
title: nRF24L01+ 数据手册
date: 2018-4-2
categories: [blog, Datasheet]
description: 单片 2.4G 无线射频收发芯片 nRF24L01+ 数据手册。
keywords: GFSK, nRF24L01+, wireless
---
 
> nRF24L01+ 是由 NORDIC 生产的工作在 2.4GHz ~ 2.5GHz 的 ISM 频段的单片无线收发器芯片。无线收发器包括：频率发生器、增强型「[SchockBurst](http://www.elecbench.com/%e5%af%b9shockburst%e4%b8%8eenhance-shockburst%e7%9a%84%e7%90%86%e8%a7%a3/)」模式控制器、功率放大器、晶体振荡器、调制器和解调器。  
输出功率频道选择和协议的设置可以通过 SPI 接口进行设置。几乎可以连接到各种单片机芯片，并完成无线数据传送工作。


**目录**

* TOC
{:toc}  

[英文原文](http://www.nordicsemi.com/chi_simple/node_176/2.4GHz-RF/nRF24L01P)

# 主要特性

- 可在全球通用 2.4 GHz ISM 频段运行
- 无线数据速率为 250 kbps，1 Mbps 和 2 Mbps
- 超低功耗运行
- 发射功率为 0 dBm 时，发送电流消耗 11.3 mA
- 无线数据速率为 2 Mbps 时，接收电流消耗 13.5 mA
- 掉电模式电流 900 nA
- 待机模式 I 电流 26 μA
- 带片上稳压器
- 输入电压范围 1.9 ~ 3.6 V
- 增强型 ShockBurst™
- 自动数据包处理
- 自动数据包事务处理
- 6 路数据通道 MultiCeiver™
- 与 nRF24L01 直接兼容
- 在 250 kbps 和 1 Mbps 下 与 nRF2401A，nRF2402，nRF24E1 和 nRF24E2 的无线兼容
- 低成本
- ±60 ppm 16 MH 晶振
- 兼容 5 V 输入
- 紧凑型 20 引脚 4x4 mm QFN 封装



# 主要应用场合

- PC 无线外设
- 鼠标，键盘和遥控器
- 3 合 1 桌面软件包
- 高级媒体中心遥控器
- VoIP 耳机
- 游戏控制器
- 运动手表和传感器
- 用于消费电子产品的 RF 遥控器
- 智能家居和商业自动化
- 超低功耗传感器网络
- 有源 RFID
- 资产跟踪系统
- 玩具

# 免责声明

Nordic Semiconductor ASA 保留对产品进行更改的权利，恕不另行通知，以提高可靠性，功能或设计。 Nordic Semiconductor ASA不承担因应用或使用本文所述任何产品或电路而引起的任何责任。
所有应用信息都是咨询性的，不构成规范的一部分。

# 限值

如果器件工作条件超过一个或多个极限值，可能会对器件造成永久性损坏。限值仅为运行条件极大值，不建议器件在规定的范围以外运行。长时间工作在极值条件下，其可靠性可能会受到影响。

# 生命支持应用
这些产品并非设计用于生命支持设备，设备或系统，因为这些产品的故障可合理预期会导致人身伤害。使用或销售这些产品用于此类应用的 Nordic Semiconductor ASA 客户自行承担风险，并同意全面赔偿 Nordic Semiconductor ASA 因使用或销售不当而造成的任何损害。

数据手册状态  

- 目标产品规格
本产品规格包含产品开发的目标规格。
- 初步产品规格
本产品规格包含初步数据; 稍后可能会从Nordic Semiconductor ASA发布补充数据。
- 产品规格
本产品规格包含最终产品规格。 Nordic Semiconductor ASA保留随时进行更改的权利，恕不另行通知，以改进设计并提供最佳产品。

# 联系方式

北欧半导体销售办事处和全球分销商请访问 www.nordicsemi.no  

# 书写规则

- 命令，位状态和寄存器名称用等宽字符表示
- 引脚名称和引脚信号条件用粗体表示
- 交叉引用以下划线表示

# 1. 介绍

nRF24L01+ 是一款带有嵌入式基带协议引擎（增强型 ShockBurst™）的单芯片 2.4 GHz 收发器，适用于超低功耗无线应用。nRF24L01+ 设计用 2.400 - 2.4835 GHz 的全球 ISM 频段。

要使用 nRF24L01+ 设计一套无线电系统，只需要一个 MCU（微控制器）和一些外部无源组件。

可以通过串行外设接口（SPI）来操作和配置 nRF24L01+。寄存器映射可通过 SPI 访问，其中包含 nRF24L01+ 中的所有配置寄存器，并且可在芯片的所有操作模式下访问。

嵌入式基带协议引擎（增强型 ShockBurst™）基于数据包通信，支持从手动操作到高级自主协议操作等各种模式。内部 FIFO 确保无线电前端和系统 MCU 之间的数据流畅通。增强型 ShockBurst™ 通过处理所有高速链路层操作来降低系统成本。

无线电前端使用 GFSK 调制。它具有用户可配置的参数，如频道，输出功率和无线数据速率。nRF24L01+ 支持 250 kbps，1 Mbps 和 2 Mbps 的无线数据速率。 高无线数据速率与两种省电模式相结合，使 nRF24L01+ 非常适合超低功耗设计。

nRF24L01+ 与 nRF24L01 直接兼容，并与 nRF2401A，nRF2402，nRF24E1和nRF24E2 无线兼容。nRF24L01+ 中的 intermodulation 和 wideband blocking values 与 nRF24L01 相比有了很大的改进，并且 nRF24L01+ 的内部滤波功能增加了满足 RF 监管标准的余量。

内部稳压器确保高电源抑制比（PSRR）和宽电源范围。

## 1.1. 特性

nRF24L01+ 的特性主要有：

- 无线电
    - 全球 2.4 GHz ISM 频段运行
    - 126 个 RF 频道
    - 常规的 RX 和 TX 接口
    - GFSK 调制
    - 250 kbps，1 和 2 Mbps 的无线数据速率
    - 1 Mbps 的 1 MHz 非重叠信道间隔
    - 2 Mbps 的 2 MHz 非重叠信道间隔
- 发射器
    - 可编程输出功率：0，-6，-12 或 -18 dBm
    - 输出功率为 0 dBm 时为 11.3 mA
- 接收器
    - 快速 AGC 改善动态范围
    - 集成通道过滤器
    - 2 Mbps 时为 13.5 mA
    - 在 2 Mbps 时灵敏度为 -82 dBm
    - 在 1 Mb​​ps 时为灵敏度 -85 dBm
    - 在 250 kbps 时灵敏度为 -94 dBm
- RF 合成器
    - 完全集成的合成器
    - 无需外部环路滤波器、VCO、可变电容、二极管或谐振器
    - 可用低成本 ±60 ppm 16 MHz 晶振
- 增强型 ShockBurst™
    - 1 到 32 个字节的动态有效长度
    - 自动数据包处理
    - 自动数据包事务处理（transaction handling）
    - 6 数据通道 MultiCeiver™ 用于 1 : 6 星形网络
- 电源管理
    - 集成稳压器
    - 1.9 至 3.6 V 的电源电压范围
    - 具有快速启动时间的空闲模式可用于高级电源管理
    - 26μA 待机 I 模式，900 nA 掉电模式
    - 最大 1.5 ms 从掉电模式启动
    - 最大 130 us 从待机 I 模式启动
- 主机接口
    - 4 针硬件 SPI
    - 最大 10 Mbps
    - 3 个独立的 32 字节 TX 和 RX FIFO
    - 兼容 5 V 输入
- 紧凑型 20 引脚 4x4 mm QFN 封装

## 1.2. 结构框图

图 1. 结构框图   
![][P1]

# 2. 引脚

## 2.1. 引脚排列

图 2. QFN20 4x4 封装引脚排列（顶视）  
![][P2]

## 2.2. 引脚功能

表 1. 引脚功能

| 引脚 | 名称   | 引脚功能 | 描述                                         |
| ---- | ------ | -------- | -------------------------------------------- |
| 1    | CE     | 数字输入 | 芯片使能 RX 或 TX 模式选择                   |
| 2    | CSN    | 数字输入 | SPI 片选信号                                 |
| 3    | SCK    | 数字输入 | SPI 时钟                                     |
| 4    | MOSI   | 数字输入 | 从 SPI 数据输入脚                            |
| 5    | MISO   | 数字输出 | 从 SPI 数据输出脚，三态选择                  |
| 6    | IRQ    | 数字输出 | 可屏蔽中断脚，低有效                         |
| 7    | VDD    | 电源     | 电源 +3V（+1.9 ~ +3.6V DC）                  |
| 8    | VSS    | 电源     | 接地 0V                                      |
| 9    | XC2    | 模拟输出 | 晶振 2 脚                                    |
| 10   | XC1    | 模拟输入 | 晶振 1 脚／外部时钟输入                       |
| 11   | VDD_PA | 电源输出 | 给 RF 的功率放大器提供的 +1.8V 电源，*图 32* |
| 12   | ANT1   | 天线     | 天线接口 1                                   |
| 13   | ANT2   | 天线     | 天线接口 2                                   |
| 14   | VSS    | 电源     | 接地 0V                                      |
| 15   | VDD    | 电源     | 电源 +3V（+1.9 ~ +3.6V DC）                  |
| 16   | IREF   | 模拟输入 | 参考电流，经 22kΩ 电阻接地，见*图 32*        |
| 17   | VSS    | 电源     | 接地 0V                                      |
| 18   | VDD    | 电源     | 电源 +3V（+1.9 ~ +3.6V DC）                  |
| 19   | DVDD   | 电源输出 | 去耦电路电源正极端，*见图 32*                |
| 20   | VSS    | 电源     | 接地 0V                                      |


# 3. 绝对极限范围

    如果工作条件超过一个或多个极限值，可能会对器件造成永久性损坏。

表 2. 极限范围  

| 操作条件                          | 最小值    | 最大值    | 单位 |
| --------------------------------- | --------- | --------- | ---- |
| **供电电压**                      |           |           |      |
| VDD                               | -0.3      | 3.6       | V    |
| VSS                               |           | 0         | V    |
| **输入电压**                      |           |           |      |
| V<sub>I</sub>                     | -0.3      | 5.25      | V    |
| **输出电压**                      |           |           |      |
| V<sub>O</sub>                     | VSS ~ VDD | VSS ~ VDD |      |
| **总功耗**                        |           |           |      |
| P<sub>D</sub>(T<sub>A</sub>=85°C) |           | 60        | mW   |
| **温度**                          |           |           |      |
| 运行温度                          | -40       | +85       | °C   |
| 存储温度                          | -40       | +125      | °C   |

# 4. 运行条件

表 3. 运行条件

| 符号 | 参数（条件）                | 注  | 最小 | 典型 | 最大 | 单位 |
| ---- | --------------------------- | --- | ---- | ---- | ---- | ---- |
| VDD  | 供电电压                    |     | 1.9  | 3.0  | 3.6  | V    |
| VDD  | 供电电压，若输入信号 > 3.6V |     | 2.7  | 3.0  | 3.3  | V    |
| TEMP | 运行温度                    |     | -40  | +27  | +85  | ºC   |

# 5. 电气特性

条件：VDD = +3V, VSS = 0V, T<sub>A</sub> = -40℃ ~ +85℃

## 5.1. 能量消耗

表 4. 能量消耗

| 符号                 | 参数（条件）                              | 注  | 最小 | 典型 | 最大 | 单位 |
| -------------------- | ----------------------------------------- | --- | ---- | ---- | ---- | ---- |
|                      | **待机模式**                              |     |      |      |      |      |
| I<sub>VDD_PD</sub>   | 掉电模式下供电电流                        |     |      | 900  |      | nA   |
| I<sub>VDD_ST1</sub>  | 待机模式-I 供电电流                       | a   |      | 26   |      | µA   |
| I<sub>VDD_ST2</sub>  | 待机模式-II 供电电流                      |     |      | 320  |      | µA   |
| I<sub>VDD_SU</sub>   | 晶振起振 1.5ms 期间平均电流               |     |      | 400  |      | μA   |
|                      | **发送模式**                              |     |      |      |      |      |
| I<sub>VDD_TX0</sub>  | 供电电流 @ 0dBm 输出功率                  | b   |      | 11.3 |      | mA   |
| I<sub>VDD_TX6</sub>  | 供电电流 @ -6dBm 输出功率                 | b   |      | 9.0  |      | mA   |
| I<sub>VDD_TX12</sub> | 供电电流 @ -12dBm 输出功率                | b   |      | 7.5  |      | mA   |
| I<sub>VDD_TX18</sub> | 供电电流 @ -18dBm 输出功率                | b   |      | 7.0  |      | mA   |
| I<sub>VDD_AVG</sub>  | 平均供电电流 @ -6dBm ShockBurst™ 输出功率 | c   |      | 0.12 |      | mA   |
| I<sub>VDD_TXS</sub>  | TX 设置期间平均电流                       | d   |      | 8.0  |      | mA   |
|                      | **接收模式**                              |     |      |      |      |      |
| I<sub>VDD_2M</sub>   | 供电电流 2Mbps                            |     |      | 13.5 |      | mA   |
| I<sub>VDD_1M</sub>   | 供电电流 1Mbps                            |     |      | 13.1 |      | mA   |
| I<sub>VDD_250</sub>  | 供电电流 250kbps                          |     |      | 12.6 |      | mA   |
| I<sub>VDD_RXS</sub>  | RX 设置期间平均电流                       | e   |      | 8.9  |      | mA   |

    a. 该电流用于 12pF 晶体。 使用外部时钟时的电流取决于信号摆幅。
    b. 天线负载阻抗 = 15Ω+j88Ω.
    c. 天线负载阻抗 = 15Ω+j88Ω. 平均数据速率 10kbps 和最大有效长度数据包。
    d. 在TX启动期间（130μs）以及从RX切换到TX（130μs）时的平均电流消耗。
    e. 在RX启动期间（130μs）以及从TX切换到RX（130μs）时的平均电流消耗。


## 5.2. 常用射频条件

表 5. 常用射频条件

| 符号                   | 参数（条件）                     | 注  | 最小 | 典型 | 最大 | 单位 |
| ---------------------- | -------------------------------- | --- | ---- | ---- | ---- | ---- |
| f<sub>OP</sub>         | 运行频率                         | a   | 2400 |      | 2525 | MHz  |
| PLL<sub>res</sub>      | PLL Programming resolution       |     |      | 1    |      | MHz  |
| f<sub>XTAL</sub>       | 晶振频率                         |     |      | 16   |      | MHz  |
| Δf<sub>250</sub>       | 频率偏差 @ 250kbps               |     |      | ±160 |      | kHz  |
| Δf<sub>1M</sub>        | 频率偏差 @ 1Mbps                 |     |      | ±160 |      | kHz  |
| Δf<sub>2M</sub>        | 频率偏差 @ 2Mbps                 |     |      | ±320 |      | kHz  |
| R<sub>GFSK</sub>       | 无线数据速率                     | b   | 250  |      | 2000 | kbps |
| F<sub>CHANNEL 1M</sub> | 非重叠的信道间隔 @ 250kbps/1Mbps | c   |      | 1    |      | MHz  |
| F<sub>CHANNEL 2M</sub> | 非重叠的信道间隔 @ 2Mbps         | c   |      | 2    |      | MHz  |

> a. 监管标准决定您可以使用的频段。  
> b. 在每个 burst on-air 中的数据速率  
> c. 最小信道间隔 1MHz


## 5.3. 发射操作

表 6. 发射操作

| 符号                | 参数（条件）                         | 注  | 最小 | 典型 | 最大 | 单位 |
| ------------------- | ------------------------------------ | --- | ---- | ---- | ---- | ---- |
| P<sub>RF</sub>      | 最大输出功率                         | a   |      | 0    | +4   | dBm  |
| P<sub>RFC</sub>     | 射频功率控制范围                     |     | 16   | 18   | 20   | dB   |
| P<sub>RFCR</sub>    | 射频功率精度                         |     |      |      | ±4   | dB   |
| P<sub>BW2</sub>     | 载波调制的 20dB 带宽(2Mbps)          |     |      | 1800 | 2000 | kHz  |
| P<sub>BW1</sub>     | 载波调制的 20dB 带宽(1Mbps)          |     |      | 900  | 1000 | kHz  |
| P<sub>BW250</sub>   | 载波调制的 20dB 带宽(250kbps)        |     |      | 700  | 800  | kHz  |
| P<sub>RF1.2</sub>   | 第一相邻信道发射功率 2MHz(2Mbps)     |     |      |      | -20  | dBc  |
| P<sub>RF2.2</sub>   | 第二相邻信道发射功率 4MHz(2Mbps)     |     |      |      | -50  | dBc  |
| P<sub>RF1.1</sub>   | 第一相邻信道发射功率 1MHz(1Mbps)     |     |      |      | -20  | dBc  |
| P<sub>RF2.1</sub>   | 第二相邻信道发射功率 2MHz(1Mbps)     |     |      |      | -45  | dBc  |
| P<sub>RF1.250</sub> | 第一相邻信道发射功率 1MHz(250kbps  ) |     |      |      | -30  | dBc  |
| P<sub>RF2.250</sub> | 第二相邻信道发射功率 2MHz(250kbps)   |     |      |      | -45  | dBc  |

> a. 天线负载阻抗  = 15Ω+j88Ω


## 5.4. 接收操作

表 7. 接收灵敏度

| 速率    | 符号              | 参数（条件）                         | 注  | 最小 | 典型 | 最大 | 单位 |
| ------- | ----------------- | ------------------------------------ | --- | ---- | ---- | ---- | ---- |
|         | RX<sub>max</sub>  | Maximum received signal at <0.1% BER |     |      | 0    |      | dBm  |
| 2Mbps   | RX<sub>SENS</sub> | 灵敏度(0.1%BER) @2Mbps               |     |      | -82  |      | dBm  |
| 1Mbps   | RX<sub>SENS</sub> | 灵敏度(0.1%BER) @1Mbps               |     |      | -85  |      | dBm  |
| 250kbps | RX<sub>SENS</sub> | 灵敏度(0.1%BER) @250kbps             |     |      | -94  |      | dBm  |

表 8. RX 选择性，根据 ETSI EN 300 440-1 V1.3.1（2001-09）第27页

| 速率    | 符号              | 参数（条件）                                               | 注  | 最小 | 典型 | 最大 | 单位 |
| ------- | ----------------- | ---------------------------------------------------------- | --- | ---- | ---- | ---- | ---- |
| 2Mbps   |                   |                                                            |     |      |      |      |      |
|         | C/I<sub>CO </sub> | C/I Co-channel                                             |     |      | 7    |      | dBc  |
|         | C/I<sub>1ST</sub> | 1<sup>st</sup> ACS (Adjacent Channel Selectivity) C/I 2MHz |     |      | 3    |      | dBc  |
|         | C/I<sub>2ND</sub> | 2<sup>nd</sup> ACS C/I 4MHz                                |     |      | -17  |      | dBc  |
|         | C/I<sub>3RD</sub> | 3<sup>rd</sup> ACS C/I 6MHz                                |     |      | -21  |      | dBc  |
|         | C/I<sub>Nth</sub> | N<sup>th</sup> ACS C/I,f<sub>i</sub> > 12MHz               |     |      | -40  |      | dBc  |
|         | C/I<sub>Nth</sub> | N<sup>th</sup> ACS C/I,f<sub>i</sub> > 36MHz               | a   |      | -48  |      | dBc  |
| 1Mbps   |                   |                                                            |     |      |      |      |      |
|         | C/I<sub>CO </sub> | C/I Co-channel                                             |     |      | 9    |      | dBc  |
|         | C/I<sub>1ST</sub> | 1<sup>st</sup> ACS C/I 1MHz                                |     |      | 8    |      | dBc  |
|         | C/I<sub>2ND</sub> | 2<sup>nd</sup> ACS C/I 2MHz                                |     |      | -20  |      | dBc  |
|         | C/I<sub>3RD</sub> | 3<sup>rd</sup> ACS C/I 3MHz                                |     |      | -30  |      | dBc  |
|         | C/I<sub>Nth</sub> | N<sup>th</sup> ACS C/I,f<sub>i</sub> > 6MHz                |     |      | -40  |      | dBc  |
|         | C/I<sub>Nth</sub> | N<sup>th</sup> ACS C/I,f<sub>i</sub> > 25MHz               | a   |      | -47  |      | dBc  |
| 250kbps |                   |                                                            |     |      |      |      |      |
|         | C/I<sub>CO </sub> | C/I Co-channel                                             |     |      | 12   |      | dBc  |
|         | C/I<sub>1ST</sub> | 1<sup>st</sup> ACS C/I 1MHz                                |     |      | -12  |      | dBc  |
|         | C/I<sub>2ND</sub> | 2<sup>nd</sup> ACS C/I 2MHz                                |     |      | -33  |      | dBc  |
|         | C/I<sub>3RD</sub> | 3<sup>rd</sup> ACS C/I 3MHz                                |     |      | -38  |      | dBc  |
|         | C/I<sub>Nth</sub> | N<sup>th</sup> ACS C/I,f<sub>i</sub> > 6MHz                |     |      | -50  |      | dBc  |
|         | C/I<sub>Nth</sub> | N<sup>th</sup> ACS C/I,f<sub>i</sub> > 25MHz               | a   |      | -60  |      | dBc  |


> a. Narrow Band (In Band) Blocking measurements:  
>    0 to ±40MHz; 1MHz step size  
>    For Interferer frequency offsets n*2*fxtal, blocking performance is degraded by approximately 5dB compared to adjacent figures.


表 9. RX selectivity with nRF24L01+ equal modulation on interfering signal. Measured using Pin = -67dBm for wanted signal.

![][T9]

表 10. RX intermodulation test performed according to Bluetooth Specification version 2.0

![][T10]

## 5.5. 晶振规格

表 11. 晶振规格

| 符号          | 参数（条件）   | 注  | 最小 | 典型 | 最大 | 单位 |
| ------------- | -------------- | --- | ---- | ---- | ---- | ---- |
| Fxo           | 晶振频率       |     |      | 16   |      | MHz  |
| ΔF            | 容限 Tolerance | a b |      |      | ±60  | ppm  |
| C<sub>0</sub> | 等效并联电容   |     |      | 1.5  | 7.0  | pF   |
| Ls            | 等效串联电感   | c   |      | 30   |      | mH   |
| C<sub>L</sub> | 负载电容       |     | 8    | 12   | 16   | pF   |
| ESR           | 等效串联电阻   |     |      |      | 100  | Ω    |

    a. 包括频率精度; 25°C时的容差，温度漂移，老化和晶体负载。
    b. 某些地区的频率规定对频率容限提出了更严格的要求（例如，日本和韩国规定最高 +/-50ppm）。
    c. 从掉电到待机模式的启动时间取决于Ls参数。 有关详细信息，请参见表16。

晶振启动时间与晶振等效电感成正比。晶振设计的趋势是减小物理轮廓。小轮廓的影响是等效串联电感 Ls 的增加，这使得启动时间更长。最大晶振启动时间 Tpd2stby = 1.5 ms，使用最大等效串联电感为 30mH 的晶振设置。

应用程序特定的最坏情况启动时间可以计算为：

如果 Ls 超过 30mH，Tpd2stby = Ls / 30mH * 1.5ms。

**注意**：在一些晶振数据手册中，Ls 称为 L1 或 Lm，Cs 称为 C1 或 Cm。

图 3. 晶振等效电路

![][P3]

## 5.6. 直流特性

表 12. 数字量输入引脚

| 符号           | 参数（条件） | 注  | 最小   | 典型 | 最大             | 单位 |
| -------------- | ------------ | --- | ------ | ---- | ---------------- | ---- |
| V<sub>IH</sub> | 输入高电平   |     | 0.7VDD |      | 5.25<sup>a</sup> | V    |
| V<sub>IL</sub> | 输入低电平   |     | VSS    |      | 0.3VDD           | V    |

> a: 如果输入信号 > 3.6V，则 nRF24L01+ 的 VDD 必须介于 2.7V 和 3.3V 之间（3.0V±10％）

表 13. 数字量输出引脚

| 符号           | 参数（条件）                        | 注  | 最小    | 典型 | 最大 | 单位 |
| -------------- | ----------------------------------- | --- | ------- | ---- | ---- | ---- |
| V<sub>OH</sub> | 输出高电平 (I<sub>OH</sub>=-0.25mA) |     | VDD-0.3 |      | VDD  | V    |
| V<sub>OL</sub> | 输出低电平 (I<sub>OL</sub>=0.25mA)  |     |         |      | 0.3  | V    |


## 5.7. 上电复位

表 14. 上电复位

| 符号            | 参数（条件） | 注  | 最小 | 典型 | 最大 | 单位 |
| --------------- | ------------ | --- | ---- | ---- | ---- | ---- |
| T<sub>PUP</sub> | 电源上升时间 | a   |      |      | 100  | ms   |
| T<sub>POR</sub> | 上电复位     | b   | 1    |      | 100  | ms   |

> a. 从 0V 到 1.9V.  
> b. 测量从 VDD 达到 1.9V 到复位结束时.


# 6. 无线电控制

本部分介绍 nRF24L01+ 无线电收发器的工作模式和用于控制无线电的参数。

nRF24L01+ 具有内置状态机，可控制芯片工作模式之间的转换。状态机从用户定义的寄存器值和内部信号中获取输入。

## 6.1. 运行模式

可以在掉电，待机，RX 或 TX 模式下配置 nRF24L01+。本节详细介绍这些模式。

### 6.1.1. 状态转换图

图 4 中的状态图显示了操作模式及其功能。状态图中突出显示了三种不同的状态：

- **推荐的操作模式（Recommended operating mode）**：是正常操作期间使用的推荐状态。
- **可能的操作模式（Possible operating mode）**：可能的操作状态，但在正常操作期间不使用。
- **过渡状态（Transition state）**：在振荡器启动和 PLL 建立期间使用的时间限制状态。

当 **VDD** 达到 1.9V 或更高时，nRF24L01+ 进入上电复位状态，保持复位状态直到进入掉电模式。

![][P4]

### 6.1.2. 掉电模式

在掉电模式下，nRF24L01+ 失能，使用最小电流消耗。所有可用的寄存器值都保持不变，并且 SPI 保持活动状态，从而可以更改配置以及上传／下载数据寄存器。关于启动时间，请参见<u>表 16</u>.通过将 `CONFIG` 寄存器中的 `PWR_UP` 位设置为低电平来进入掉电模式。

### 6.1.3. 待机模式

#### 6.1.3.1. 待机模式-I

通过将 `CONFIG` 寄存器中的 `PWR_UP` 位设置为 1，器件进入待机模式-I。 待机模式-I 用于在保持较短的启动时间的同时将平均电流消耗降至最低。在这种模式下，只有部分晶体振荡器处于活动状态。只有特定情况下才会切换到活动模式，即在 **CE** 为高电平且将 **CE** 置为低电平时。nRF24L01 将从 TX 和 RX 模式返回到待机模式-I。

#### 6.1.3.2. 待机模式-II

在待机模式-II 下，与待机模式-I 相比，额外的时钟缓冲器处于活动状态并使用更多电流。如果 **CE** 在具有空 TX FIFO 的 PTX 设备上保持高电平，nRF24L01+ 进入待机模式-II。如果一个新的数据包上传到 TX FIFO，PLL 立即启动并在正常的 PLL 建立延迟（130μs）后发送数据包。

寄存器值保持并在两种待机模式下均可激活 SPI。 有关启动时间，请参阅<u>表 16</u>。

### 6.1.4. RX 模式

RX 模式是将 nRF24L01+ 用作接收器的主动模式。要进入该模式，nRF24L01+ 必须将 `PWR_UP` 位，`PRIM_RX` 位和 **CE** 引脚设置为高电平。

在接收模式下，接收器解调来自射频通道的信号，不断向基带协议引擎提供解调数据。基带协议引擎持续搜索有效的数据包。如果找到了有效的数据包（通过匹配的地址和有效的 CRC），数据包的 payload 将存于 RX FIFO 的空缺位置。如果 RX FIFO 已满，则丢弃接收的数据包。

nRF24L01+ 保持RX模式，直到 MCU 将其配置为待机模式-I 或掉电模式。但是，如果基带协议引擎中的自动协议功能（增强型 ShockBurst™）已启用，nRF24L01+ 可以进入其他模式以执行协议。

在接收模式下，接收功率检测器（RPD, Received Power Detector）信号可用。RPD 是一个信号，它当接收频道内检测到高于-64 dBm的 RF 信号时被设置为高电平。内部 RPD 信号在提供给 RPD 寄存器之前被过滤。在 RPD 设置为高电平之前，RF信号必须至少存在 40 μs。<u>第 6.4 节</u>描述了如何使用 RPD。

### 6.1.5. TX 模式

TX 模式是发送数据包的主动模式。要进入该模式，nRF24L01+ 必须为下面的状态， `PWR_UP` 位置高，将 `PRIM_RX` 位置低，TX FIFO 中有 payload 且 **CE** 上的高电平脉冲超过 10 μs。

nRF24L01+ 一直处于 TX 模式，直到完成数据包发送。如果 **CE** = 0，则 nRF24L01+ 返回待机模式-I。 如果 **CE** = 1，则 TX FIFO 的状态决定下一个操作。 如果 TX FIFO 不为空，则 nRF24L01+ 将保持在 TX 模式并发送下一个数据包。如果 TX FIFO 为空，则 nRF24L01+ 进入待机模式-II。 处于 TX 模式时，nRF24L01+ 发送 PLL 工作在开环状态。重要的是，不要一次将 nRF24L01+ 在 TX 模式下保持超过 4 ms。如果启用增强 ShockBurst™ 功能，则 nRF24L01+ 绝不会在 TX 模式下大于 4 ms。

### 6.1.6. 工作模式配置

下面的表格描述了怎样配置工作模式。

表 15. 主要工作模式

| 模式        | `PWR_UP` | `PRIM_RX` | **CE** | FIFO 状态                                                    |
| ----------- | -------- | --------- | ------ | ------------------------------------------------------------ |
| RX 模式     | 1        | 1         | 1      | -                                                            |
| TX 模式     | 1        | 0         | 1      | 数据在 TX FIFO 中。将清空 TX FIFO 的所有 level。<sup>a</sup> |
| TX 模式     | 1        | 0         | 1→0    | 数据在 TX FIFO 中。将清空 TX FIFO 的一个 level。<sup>b</sup> |
| 待机模式-II | 1        | 0         | 1      | TX FIFO 为空                                                 |
| 待机模式-I  | 1        | -         | 0      | 无数据传输                                                   |
| 掉电模式    | 0        | -         | -      | -                                                            |

> a. 如果 **CE** 保持高电平，则所有 TX FIFO 都清空，并执行所有必要的 ACK 和可能的重发。只要 TX FIFO 被重新填充，发送就会继续。如果 **CE** 仍为高电平时 TX FIFO 为空，则 nRF24L01+ 进入待机模式-II。 在这种模式下，只要在将数据包上载（UL）到 TX FIFO 之后将 **CSN** 设置为高电平，就立即开始发送数据包。  
> b. 该工作模式将 **CE** 脉冲至少持续 10 μs，允许发送一个数据包。这是正常的工作模式。数据包发送完成后，nRF24L01+ 进入待机模式-I。

### 6.1.7. 时序信息

本节中的时序信息涉及模式之间的转换和 **CE** 引脚的时序。 从 TX 模式到 RX 模式（或反过来）的转换与从待机模式到 TX 模式或 RX 模式（最大 130 μs）的转换相同，如下表。

表 16. 操作时间  

![][T16]

> a. 有关晶振规格，请参见<u>表 11</u>。

nRF24L01+ 从掉电模式进入 TX 或 RX 模式必须首先通过待机模式。在nRF24L01+ 离开掉电模式之后， **CE** 设置为高电平之前，必须有一个延迟 Tpd2stby（见<u>表 16</u>）。

> 注：如果 VDD 关闭，寄存器值将丢失，必须在进入 TX 或 RX 模式之前配置 nRF24L01+。

## 6.2. 无线传输速率

无线传输速率（air data rate）是 nRF24L01+ 在发送和接收数据时使用的调制信号速率。它可以是 250 kbps，1 Mbps 或 2 Mbps。使用较低的无线传输速率比较高的无线传输速率提供更好的接收器灵敏度。然而，较高的无线传输速率可使平均电流消耗较低，并且可减少空中碰撞的可能性。

无线传输速率由 `RF_SETUP` 寄存器中的 `RF_DR` 位设置。发射端和接收端必须以相同的无线传输速率进行编程才能相互通信。
nRF24L01+ 与 nRF24L01 完全兼容。若与 nRF2401A，nRF2402，nRF24E1 和 nRF24E2 兼容，无线传输速率必须设置为 250 kbps 或 1 Mbps。

## 6.3. RF 信道频率

RF 信道频率决定了 nRF24L01+ 使用的信道的中心。该信道在 250 kbps 和 1M bps 下占用带宽小于 1 MHz，在 2 Mbps 下带宽小于 2 MHz。nRF24L01+ 可以在 2.400 GHz 至 2.525 GHz 的频率下工作。RF 信道频率设置的编程分辨率为 1 MHz。

在 2 Mbps 时，信道占用的带宽比 RF 信道频率设置的分辨率更宽。为确保 2 Mbps 模式下信道不重叠，信道间隔必须为 2 MHz 或更高。在 1 Mbps 和 250 kbps 时，信道带宽与 RF 频率的分辨率相同或更低。

RF 信道频率由 `RF_CH` 寄存器根据以下公式设置：

*F<sub>0</sub> = 2400 + `RF_CH` [MHz]*

发射端和接收端必须以相同的 RF 信道频率进行编程才能相互通信。


## 6.4. 接收功率检测器

功率等级位于接收的 RF 信道中，当接收的功率等级超过 -64 dBm 时，位于寄存器 09，位 0 中的接收功率检测器（RPD）触发。如果接收功率小于 -64 dBm，则 RDP = 0。

当 nRF24L01+ 处于接收模式时，可随时读出 RPD。它提供了该频道当前接收功率等级的快照（snapshot）。接收到有效数据包后，RPD 状态被锁存，然后指示来自己方的发射端的信号强度。如果没有收到数据包，RPD 在接收周期结束时锁存，主 MCU 设置 CE 为低电平或 RX 超时由增强型 ShockBurst™ 控制。

当接收模式启用且等待时间 Tstby2a + Tdelay_AGC = 130 us + 40 us 后，RPD 状态是正确的。RX 增益随温度变化，这意味着 RPD 阈值也随温度而变化。在 T = -40 °C 时，RPD 阈值降低了 -5 dB，并在 85 °C 时增加了 +5dB。

## 6.5. 功率放大器控制

功率放大器（PA，Power Amplifier）控制用于设置 nRF24L01+ 功率放大器的输出功率。在 TX 模式下，PA 控制有四个可编程步骤，参见<u>表17</u>。

功率放大器控制由 `RF_SETUP` 寄存器中的 `RF_PWR` 位设置。

| SPI RF-SETUP (`RF_PWR`) | RF 输出功率 | 直流电流损耗 |
| :---------------------: | ----------: | -----------: |
| 11                      | 0dBm        | 11.3mA       |
| 10                      | -6dBm       | 9.0mA        |
| 01                      | -12dBm      | 7.5mA        |
| 00                      | -18dBm      | 7.0mA        |

> 条件： **VDD** = 3.0 V, **VSS** = 0 V, T<sub>A</sub> = 27 ℃， 负载阻抗 = 15Ω+j88Ω。

## 6.6. RX／TX 控制

RX／TX 控制由 `CONFIG` 寄存器中 `PRIM_RX` 位配置，可以设置 nRF24L01+ 的发送／接收模式。

# 7. 增强型 ShockBurst™ 

增强型 ShockBurst™ 是基于数据包的数据链路层，具有自动数据包组装和定时，自动应答和数据包重发功能。增强型 ShockBurst™ 可实现与低成本微控制器主机之间的超低功耗和高性能通信。增强型 ShockBurst™ 功能可显著提高双向和单向系统的电源效率，而不会增加主机控制器端的复杂性。

## 7.1. 特性

增强型 ShockBurst™ 的主要特点是：
- 1 到 32 字节的动态 payload 长度
- 自动数据包处理
- 自动数据包事务处理
    - 带 payload 的自动应答
    - 自动重发
- 6 个数据通道 MultiCeiver™，用于 1：6 星形网络

## 7.2. 增强型 ShockBurst™ 概述

增强型 ShockBurst™ 使用 ShockBurst™ 进行自动数据包处理和定时。在传输过程中，ShockBurst™ 组装数据包并对数据包中的位进行时钟传输。在接收期间，ShockBurst™ 不断在解调信号中搜索有效地址。当 ShockBurst™ 发现一个有效的地址时，处理数据包的其余部分并通过 CRC 验证它。如果数据包有效，则将 payload 移入 RX FIFO 中的空闲位置。所有高速位处理和时序均由 ShockBurst™ 控制。

增强型 ShockBurst™ 具有自动数据包事务处理（automatic packet transaction handling）功能，可轻松实现可靠的双向数据链接。增强型 ShockBurst™ 数据包事务是两个收发器之间的数据包交换，一个收发器充当主接收器（PRX，Primary Receiver），另一个收发器充当主发送器（PTX，Primary Transmitter）。增强型 ShockBurst™ 数据包事务始​​终由 PTX 的数据包发送来启动，当 PTX 收到来自 PRX 的应答数据包（ACK 数据包）时，事务完成。PRX 可以将用户数据附加到 ACK 数据包，从而启用双向数据链接。

自动数据包事务处理的工作原理如下：

1. 通过从 PTX 向 PRX 发送数据包开始事务。增强型 ShockBurst™ 自动将 PTX 设置为接收模式，以等待ACK 数据包。
2. 如果 PRX 接收到数据包，增强型 ShockBurst™ 会在返回到接收模式之前自动组合并向 PTX 发送应答数据包（ACK 数据包）。
3. 如果 PTX 没有立即收到 ACK 数据包，增强型 ShockBurst™ 会在可编程的延迟后自动重发原始数据包，并将 PTX 设置为接收模式以等待 ACK 数据包。

在增强型 ShockBurst™ 中，可以配置参数，例如最大重传发数和从一次传输到下一次重传的延迟。所有的自动处理都是在没有 MCU 参与的情况下完成的。

## 7.3. 增强型 Shockburst™ 数据包格式

本节介绍增强型 ShockBurst™ 数据包的格式。 增强型 ShockBurst™ 数据包包含前导码，地址，数据包控制，payload 和 CRC 字段。图 5. 显示了左侧 MSB 的数据包格式。

图 5. 带 payload（0-32 字节）的数据包

| 前导码 | 地址     | 包控制字段 | Payload   | CRC      |
| ------ | -------- | ---------- | --------- | -------- |
| 1 字节 | 3-5 字节 | 9 位       | 0-32 字节 | 1-2 字节 |
        
### 7.3.1. 前导码

前导码是用于将接收解调器与输入比特流同步的比特序列。前导码是一个字节长，可以是 `01010101` 或 `10101010`。如果地址中的第一位是 `1`，则前导码自动设置为 `10101010`，如果第一位为 `0`，则前导码自动设置为 `01010101`。这样做是为了确保在在前导码中有足够的过渡来稳定接收器。

### 7.3.2. 地址

这是接收器的地址。地址确保数据包被正确的接收器检测并接收，防止多个 nRF24L01+ 系统之间发生意外串扰。可以将 `AW` 寄存器中的地址字段宽度配置为 3,4 或 5 个字节，请参阅<u>表 28</u>。

> 注意：电平只变化一次的地址（像，`000FFFFFFF`）通常可以在噪声中检测到，并且可能导致错误检测，这可能会导致包错误率（Packet Error Rate）升高。作为前导延续（高低切换）的地址也会增加包错误率。

### 7.3.3. 包控制字段

图 6 显示了 9 位数据包控制字段的格式，MSB 在左边。

| Payload 长度 | PID  | NO_ACK |
| ------------ | ---- | ------ |
| 6 位         | 2 位 | 1 位   |

包控制字段包含一个 6 位的 payload 长度字段，一个 2 位的 PID（数据包识别）和一个 1 位的 `NO_ACK` 标志。

#### 7.3.3.1. Payload 长度

这 6 位字段指示以字节为单位的 Payload 长度。Payload 的长度可以从 0 到 32 个字节。

编码：000000 = 0 字节（仅用于空的 ACK 包） 100000 = 32 字节，100001 = 不需关心。

只有在启用动态 Payload 长度（Dynamic Payload Length）功能的情况下才会使用此字段。

#### 7.3.3.2. PID（包识别，Packet identification）

2 位 PID 字段用于检测接收到的数据包是新的还是重新发送的。PID 防止 PRX 设备向接收主机 MCU 多次提供相同的 Payload。对于通过 SPI 接收到的每个新数据包，PID 字段在 TX 端递增。PRX 设备用 PID 和 CRC 字段（请参阅<u>第7.3.5节</u>）确定数据包是重发的还是新的。当链路上有多个数据包丢失时，PID 字段可能会等于上一次接收到的 PID。如果数据包与前一个数据包具有相同的 PID，则 nRF24L01+ 将比较两个数据包的 CRC 总和。如果 CRC 总和也相等，则最后收到的数据包被认为是先前收到的数据包的副本并丢弃。

#### 7.3.3.3. 无应答标志（`NO_ACK`）

可选择的自动应答功能控制 `NO_ACK` 标志。

该标志仅在使用自动应答功能时使用。将标志设置为高，告诉接收器该数据包不用自动应答。

在 PTX 上，可以使用以下命令在包控制字段中设置 `NO_ACK` 标志位：

`W_TX_PAYLOAD_NOACK`

但是，必须先设置 `FEATURE` 寄存器中的 `EN_DYN_ACK` 位以启用该功能。当您使用此选项时，PTX 在传输数据包后直接进入待机模式-I。PRX 在收到数据包时不发送 ACK 数据包。


### 7.3.4. Payload

Payload 是用户定义的数据包内容。它可以是 0 到 32 个字节宽，并在上传到 nRF24L01+ 时无线传输。

增强型 ShockBurst™ 提供两种处理 Payload 长度的方案：静态和动态。

默认值是静态 Payload 长度。 对于静态 Payload 长度，发射端和接收端之间的所有数据包具有相同的长度。静态 Payload 长度由接收端的 `RX_PW_Px` 寄存器设置。发送端的 Payload 长度由时钟输入到 TX_FIFO 的字节数设置，并且必须等于接收器端 `RX_PW_Px` 寄存器中的值。

动态 Payload 长度（DPL，Dynamic Payload Length）是静态 Payload 长度的替代。DPL 使发送端能够向接收端发送具有可变 Payload 长度的包。这意味着对于具有不同 Payload 长度的系统，不需要将数据包长度扩展到最长的 Payload。

借助 DP L功能，nRF24L01+ 可以自动解码接收数据包的 Payload 长度，而不是使用 `RX_PW_Px` 寄存器。MCU 可以使用 `R_RX_PL_WID` 命令读取接收到的有效负载的长度。

> 注意：使用 `R_RX_PL_WID` 命令时，请始终检查报告的数据包宽度是否为 32 字节或更短。如果它的宽度大于 32 字节，则数据包有错误且必须丢弃。使用 `Flush_RX` 命令丢弃数据包。

为了启用 DPL，必须使能 `FEATURE` 寄存器中的 `EN_DPL` 位。在 RX 模式下，必须设置 `DYNPD` 寄存器。要发送到一个启用 DPL 的 PRX，PTX 必须设置 `DYNPD` 中的 `DPL_P0` 位。

### 7.3.5. CRC (循环冗余校验) 

CRC 是数据包中的强制错误检测机制。它具有 1 或 2 个字节，通过地址、包控制字段和 Payload 进行计算。

1 字节 CRC 的多项式为 X<sup>8</sup> + X<sup>2</sup> + X + 1。初始值 0xFF。

2 字节 CRC 的多项式为 X<sup>16</sup> + X<sup>12</sup> + X<sup>5</sup> + 1。初始值 0xFFFF。

CRC 中的字节数由 `CONFIG` 寄存器中的 `CRCO` 位设置。 如果 CRC 失败，增强型 ShockBurst™ 不接受数据包。

### 7.3.6. 自动组包

自动组包主要组合前导码、地址、数据包控制字段、Payload 和 CRC，在发送之前创建一个完整的数据包。

图 7. 自动组包流程图

![][P7]



### 7.3.7. 自动拆包

数据包通过验证后，增强型 ShockBurst™ 拆解数据包，并将 Payload 加载到 RX FIFO 中，然后产生 `RX_DR` IRQ。

图 8. 自动拆包流程图

![][P8]

## 7.4. 自动包事务处理

增强型 ShockBurst™ 具有两种自动数据包事务处理功能：自动应答和自动重发。

### 7.4.1. 自动应答

自动应答功能可在收到并确认数据包后自动将 ACK 数据包发送至 PTX。自动应答功能降低了系统 MCU 的负载，并且可以消除对专用 SPI 硬件的需求。这也降低了成本和平均电流消耗。自动应答功能通过设置 `EN_AA` 寄存器来启用。

> 注意：如果收到的数据包设置了 `NO_ACK` 标志，则不执行自动应答。

ACK 数据包可以包含从 PRX 到 PTX 的可选 Payload。为了使用此功能，必须启用动态 Payload 长度（DPL）功能。PRX 端的 MCU 必须通过使用 `W_ACK_PAYLOAD` 命令将 Payload 通过时钟发送到 TX FIFO 来上传。Payload 在 TX FIFO（PRX）中等待处理，直到从 PTX 收到新数据包。nRF24L01+ 可以同时在 TX FIFO（PRX）中挂起三个 ACK 数据包 Payload。

图 9. 带 Payload 挂起的 TX FIFO (PRX)

![][P9]

图 9 显示了在处理挂起的 ACK 数据包 Payload 时 TX FIFO（PRX）如何操作。使用 `W_ACK_PAYLOAD` 命令在 MCU 上时钟输入 Payload。地址解码器和缓冲控制器确保 Payload 存储在 TX FIFO（PRX）中的空闲位置。当收到一个数据包时，将通过 PTX 地址通知地址解码器和缓冲器控制器。这样可确保将正确的 Payload 呈现给 ACK 生成器。

如果 TX FIFO（PRX）包含多个到 PTX 的 Payload，则使用先进先出（first in -first out）原则处理 Payload。如果所有挂起的 Payload 都指向（addressed to）一个链路丢失的 PTX，则 TX FIFO（PRX）将被阻塞（blocked）。在这种情况下，MCU 可以使用 `FLUSH_TX` 命令刷新 TX FIFO（PRX）。

为了启用带 Payload 的自动应答功能，必须设置 `FEATURE` 寄存器中的 `EN_ACK_PAY` 位。

### 7.4.2. 自动重发（ART）

自动重发是一种在未收到 ACK 数据包的情况下重发数据包的功能。它用于 PTX 上的自动确认系统。当数据包未被确认时，可以通过设置 `SETUP_RETR` 寄存器中的 `ARC` 位来设置允许重传的次数。每次发送数据包时，PTX 进入接收模式，并等待一个短时间的 ACK 数据包。PTX 处于 RX 模式的时间段取决于以下条件：

- 自动重发延迟（ARD）已过。
- 250 μs 内无地址匹配（或 250 kbps模式下 500 μs）。
- 收到数据包后（CRC 正确与否）。

当收到 ACK 数据包时，nRF24L01+ 断言 `TX_DS` IRQ。

如果 TX FIFO 中没有未发送的数据且 **CE** 引脚为低电平，nRF24L01+ 进入待机模式-I。如果未收到 ACK 数据包，nRF24L01+ 将在 ARD 定义的延迟后返回到 TX 模式并重新发送数据。一直持续到收到应答，或达到最大重发数为止。

每丢失一个数据包，`OBSERVE_TX` 寄存器中的两个数据包丢失计数器 `ARC_CNT` 和 `PLOS_CNT` 就会自增。`ARC_CNT` 计算当前事务的重发次数。可通过启动新的事务来重置 `ARC_CNT`。`PLOS_CNT` 计算自上次频道更改以来的重传总数。通过写入 `RF_CH` 寄存器来重置 `PLOS_CNT`。可以使用 `OBSERVE_TX` 寄存器中的信息对信道质量进行总体评估。

ARD 定义了从传输数据包的末端到 PTX 重新开始传输的时间。ARD 设置在 `SETUP_RETR` 寄存器中，步长为 250 μs。如果 PTX 未收到 ACK 数据包，则重新发送。

使用带有 Payload 的 ACK 数据包时，ARD 的长度有限制。ARD 时间不得少于 ACK 数据包的启动时间和空中传播时间的总和：

- 对于 2 Mbps 数据速率和 5 字节地址; 15 字节是最大的 ACK 包 Payload 长度 ARD = 250 μs（复位值）。
- 对于 1 Mbps 数据速率和 5 字节地址; 5 字节是最大 ACK 包 Payload 长度 ARD = 250 μs（复位值）。

ARD = 500 μs 对于 1 或 2 Mbps模式下的任何 ACK Payload 长度都足够长。

- 对于 250 kbps 数据速率和 5 字节地址，以下值适用：

表 18. 250 kbps 下不同重传延迟时间的最大 ACK Payload 大小

| ARD    | ACK 包大小 (字节)     |
| ------ | --------------------- |
| 1500µs | 所有 ACK payload 大小 |
| 1250µs | ≤ 24                  |
| 1000µs | ≤ 16                  |
| 750µs  | ≤ 8                   |
| 500µs  | 空 ACK 无 Payload     |

作为自动重发的替代方案，可以手动设置 nRF24L01+ 多次重发数据包。这由 `REUSE_TX_PL` 命令完成。当使用此命令时，每次 MCU 发起数据包发送时必须在 **CE** 引脚上附带脉冲。


## 7.5. 增强型 ShockBurst 流程图

 本节包含增强型 ShockBurst™ 中 PTX 和 PRX 操作流程图概述。

### 7.5.1. PTX 操作

<u>图 10</u> 中的流程图描述了配置为 PTX 的 nRF24L01+ 在进入待机模式-I 后的操作。

图 10. 增强型 ShockBurst™ 下 PTX 操作流程

![][P10]

> 注：ShockBust™ 操作用虚线框标出。

通过将 **CE** 引脚设置为高电平来激活 PTX 模式。如果 TX FIFO 中存在数据包，则 nRF24L01+ 进入 TX 模式并发送数据包。如果启用了自动重发功能，状态机检查是否设置了 `NO_ACK` 标志。如果未设置，则 nRF24L01+ 进入 RX 模式以接收 ACK 数据包。如果收到的 ACK 数据包为空，则只有 `TX_DS` IRQ 被置位。如果 ACK 数据包包含 Payload，则在 nRF24L01+ 返回待机模式-I 之前，`TX_DS` IRQ 和 `RX_DR` IRQ 同时被置位。

如果在超时发生之前未收到 ACK 数据包，则 nRF24L01+ 将返回到待机模式-II。在 ARD 到达之前，它一直处于待机模式-II。如果重发次数还没有达到 ARC，则nRF24L01+ 进入 TX 模式并再次发送最后一个数据包。

在执行自动重发功能时，重发次数可以达到 ARC 中定义的最大数量。如果发生这种情况，nRF24L01+ 会置位 `MAX_RT` IRQ并返回待机模式-I。

如果 **CE** 为高电平且 TX FIFO 为空，则 nRF24L01+ 进入待机模式-II。

### 7.5.2. PRX 操作 

<u>图 11</u> 中的流程图描述了配置为 PRX 的 nRF24L01+ 在进入待机模式-I 后的操作。

图 11. 增强型 ShockBurst™ 下 PRX 操作流程

![][P11]

> 注：ShockBust™ 操作用虚线框标出。

通过将 **CE** 引脚设置为高电平来激活 PRX 模式。nRF24L01+ 进入 RX 模式并开始搜索数据包。如果接收到数据包并启用了自动应答功能，nRF24L01+ 将决定数据包是新数据包还是先前接收数据包的副本。如果数据包是新的，则 Payload 在 RX FIFO 中可用，并且 `RX_DR` IRQ被置位。如果从发送器接收到的最后一个数据包被带有 Payload 的 ACK 数据包应答，则 `TX_DS` IRQ指示 PTX 收到带有 Payload 的 ACK 数据包。如果接收数据包中没有设置 `No_ACK` 标志，则 PRX 进入 TX 模式。如果 TX FIFO 中有一个挂起的 Payload，它将附加到 ACK 数据包。ACK 数据包传输完成后，nRF24L01+ 返回 RX 模式。

如果 ACK 数据包丢失，可能会收到先前收到的数据包的副本。在这种情况下，PRX 在返回 RX 模式之前丢弃接收的数据包并发送 ACK 数据包。

## 7.6. MultiCeiver™

MultiCeiver™ 是一种用于 RX 模式的功能，它包含一组具有唯一地址的六个并行数据通道（data pipe）。数据通道是物理 RF 信道中的逻辑信道。每个数据通道在 nRF24L01+ 中都有自己的物理地址（数据通道地址）解码。

图 12. 使用 MultiCeiver™ 方式的 PRX

![][P12]

配置为 PRX（主接收器）的 nRF24L01+ 可以在一个频道中接收发往六个不同数据通道的数据，如<u>图 12</u> 所示。每个数据通道都有其自己的唯一地址，并可针对独立行为进行配置。

配置为 PTX 的最多六个 nRF24L01+ 可以与一个配置为 PRX 的 nRF24L01+ 进行通信。同时搜索所有数据通道地址。一次只有一个数据通道可以接收数据包。所有数据通道都可以执行增强型 ShockBurst™ 功能。

以下设置对所有数据通道通用：

- 启用／禁用 CRC（增强型 ShockBurst™ 启用时，CRC 始终启用）
- CRC 编码方案
- 接收地址宽度
- 频道
- 无线数据速率
- LNA 增益
数据通道使用 `EN_RXADDR` 寄存器中的位启用。默认情况下只启用数据通道 0 和 1。每个数据通道地址都配置在 `RX_ADDR_PX` 寄存器中。

> 注意：始终确保所有数据通道具有不同的地址。

每个通道最多可以有一个 5 字节的可配置地址。数据通道 0 有一个唯一的 5 字节地址。数据通道 1 - 5 共享四个最重要的地址字节。LSByte 对于所有六个通道必须是唯一的。<u>图 13</u> 是数据通道 0 - 5 地址配置的例子。

图 13. 数据通道 0 - 5 地址

![][P13]


使用 MultiCeiver™ 和增强型 ShockBurst™ 的 PRX 从多个 PTX 接收数据包。为了确保来自 PRX 的 ACK 数据包被发送到正确的 PTX，PRX 将收到数据包的数据通道地址作为发送 ACK 数据包时的 TX 地址。<u>图 14</u> 是 PRX 和 PTX 的地址配置示例。在 PRX 上，定义为通道地址的 `RX_ADDR_Pn` 必须是唯一的。在 PTX 上，`TX_ADDR` 必须与 `RX_ADDR_P0` 相同，并作为指定通道的地址。

图 14. MultiCeiver™ 下数据通道地址示例

![][P14]

只有当数据通道接收到完整的数据包时，其他数据通道才能开始接收数据。当多个 PTX 传输到 PRX 时，可以使用 ARD 来歪曲（skrew）自动重发，以便它们仅阻塞对方一次。


## 7.7. 增强型 ShockBurst™ 时序

本节介绍增强型 ShockBurst™ 的时序以及所有模式如何启动和操作。增强型 ShockBurst™ 时序通过数据和控制接口（Data and Control interface）进行控制。

nRF24L01+ 可以设置为静态模式或自主模式（内部状态机控制事件）。每个自主模式／序列以 **IRQ** 引脚上的中断结束。所有中断都在时序图中表示为 IRQ 事件。

图 15. 无应答时发送一个数据包

![][P15]

以下等式计算各种时间测量值：

表 19. 时间公式

![][T19]

图 16. 上传一个数据包的增强型 ShockBurst™ 时序（2 Mbps）

![][P16]

<u>图 16</u> 显示了一个数据包的发送和应答。PRX 器件激活 RX 模式（**CE** = 1），PTX 器件在 TX 模式下激活（**CE** = 1，最小 10 μs）。 经过 130 μs 后，传输开始并在经过 T<sub>OA</sub> 后结束。

当传输结束时，PTX 设备自动切换到 RX 模式，等待来自 PRX 设备的 ACK 数据包。当 PRX 设备接收到数据包时，它为主 MCU 设置中断，并切换到 TX 模式发送 ACK。 在 PTX 设备收到 ACK 包后，向 MCU 设置中断，并从 TX FIFO 中清除该包。

在<u>图 17</u> 中，显示 PTX 在第一个 ACK 包丢失时的包发送时序。当 ACK 包失败时的完整发送情况参见<u>图 20</u>。

图 17. 第一个 ACK 包丢失时的增强型 ShockBust™ 时序（2 Mbps）

![][P17]












    2018.4.8. 搁






-----------------------


## 7.8 增强型 ShockBurst™ transaction diagram 
7.8.1 Single transaction with ACK packet and interrupts
7.8.2 Single transaction with a lost packet 
7.8.3 Single transaction with a lost ACK packet
7.8.4 Single transaction with ACK payload packet
7.8.5 Single transaction with ACK payload packet and lost packet
7.8.6 Two transactions with ACK payload packet and the first
ACK packet lost 
7.8.7 Two transactions where max retransmissions is reached
## 7.9 Compatibility with ShockBurst™ 
7.9.1 ShockBurst™ packet format
# 8 Data and Control Interface 
## 8.1 Features
## 8.2 Functional description 
## 8.3 SPI operation 
8.3.1 SPI commands 
8.3.2 SPI timing 
## 8.4 Data FIFO 
## 8.5 Interrupt
# 9 Register Map
9.1 Register map table
# 10 Peripheral RF Information
## 10.1 Antenna output
## 10.2 Crystal oscillator
## 10.3 nRF24L01+ crystal sharing with an MCU
10.3.1 Crystal parameters 
10.3.2 Input crystal amplitude and current consumption
## 10.4 PCB layout and decoupling guidelines
# 11 Application example 
## 11.1 PCB layout examples
# 12 Mechanical specifications
# 13 Ordering information 
## 13.1 Package marking 
## 13.2 Abbreviations 
## 13.3 Product options 
13.3.1 RF silicon
13.3.2 Development tools
# 14 Glossary of Terms
## Appendix A - Enhanced ShockBurst™ - Configuration and communication example 
Enhanced ShockBurst™ transmitting payload
Revision 1.0 Page 6 of 78
nRF24L01+ Product Specification
Enhanced ShockBurst™ receive payload
## Appendix B - Configuration for compatibility with nRF24XX
## Appendix C - Constant carrier wave output for testing
Configuration. 78


<!-- 缩略词 -->
*[ACK]:确认信号 应答信号 
*[ART]:自动重发  
*[CE]:芯片使能  
*[CLK]:时钟信号  
*[CRC]:循环冗余校验  
*[CSN]:片选非  
*[ESB]:增强型 ShockBrust TM 
*[GFSK]:高斯键控频移  
*[IRQ]:中断请求  
*[ISM]:工业 科学 医学  
*[LNA]:低噪声放大  
*[LSB]:最低有效位  
*[LSByte]:最低有效字节  
*[Mbps]:兆位／秒 
*[MCU]:微控制器  
*[MISO]:主机输入从机输出  
*[MOSI]:主机输出从机输入  
*[MSB]:最高有效位  
*[MSByte]:最高有效字节  
*[PCB]:印刷电路板  
*[PER]:数据包误码率  
*[PID]:数据包识别位  
*[PLD]:载波  
*[PRX]:接收源  
*[PSRR]: Power Supply Rejection Ratio 电压抑制比
*[PTX]:发射源  
*[PWR_DWN]:掉电  
*[PWR_UP]:上电  
*[RPD]:Received Power Detector
*[RX]:接收  
*[RX_DR]:接收数据准备就绪  
*[SPI]: Serial Peripheral Interface 串行外设接口
*[TX]:发送  
*[TX_DS]:已发送数据  
*[基带协议引擎]:baseband protocol engine
*[Payload]:有效数据

<!-- 图片链接 -->
[P1]:https://us1.myximage.com/2018/04/03/91f68a55e9f4b4ee578c4680ab23ee14.png "nRF24L01+ block diagram"

[P2]:https://us1.myximage.com/2018/04/03/5be39d85ff8d401e20e1b9ab2b995000.png "nRF24L01+ pin assignment (top view) for the QFN20 4x4 package"

[P3]:https://us1.myximage.com/2018/04/03/889a27fddc22d4fb0b178447a18dec20.png "Equivalent crystal components"

[P4]:https://us1.myximage.com/2018/04/03/115a3022c8bf7842380c6d1ced6ddd08.png "Radio control state diagram"

[P7]:https://us1.myximage.com/2018/04/04/3bf9cf646038fc10057d5e33da7857a5.png "Automatic packet assembly"

[P8]:https://us1.myximage.com/2018/04/04/8f5136ccdf0cd22731ef7cbd43807f6e.png "Automatic packet disassembly"

[P9]:https://us1.myximage.com/2018/04/04/20e5814f4f01c0500ca270c6f4a91438.png "TX FIFO (PRX) with pending payloads"

[P10]:https://us1.myximage.com/2018/04/08/3ecaf3befae3d05c67f6b68310d847ec.png "PTX operations in Enhanced ShockBurst™"

[P11]:https://us1.myximage.com/2018/04/08/024fc88c9e92f2e337a83af4832a30fa.png "PRX operations in Enhanced ShockBurst™"

[P12]:https://us1.myximage.com/2018/04/08/0dae118c95722c1a0cd559fa6f7db55f.png "PRX using MultiCeiver™"

[P13]:https://us1.myximage.com/2018/04/08/f81c078acb761ff155d2b460cbc96b0d.png "Addressing data pipes 0-5"

[P14]:https://us1.myximage.com/2018/04/08/33a6b70f749fb6a962349791d36a8176.png "Example of data pipe addressing in MultiCeiver™"

[P15]:https://us1.myximage.com/2018/04/08/5b8dcf2a08e10adfd151bc71319fda9f.png "Transmitting one packet with NO_ACK on"

[P16]:https://us1.myximage.com/2018/04/08/1f218dad53fc9521319de8f1e69511d9.png "Timing of Enhanced ShockBurst™ for one packet upload (2Mbps)"

[P17]:https://us1.myximage.com/2018/04/08/360977c60c12cf14512e7a89f00c9e38.png "Timing of Enhanced ShockBurst™ when the first ACK packet is lost (2Mbps)"








[T9]:https://us1.myximage.com/2018/04/04/a38f922805032f67676a0ad0bbc93609.png 

[T10]:https://us1.myximage.com/2018/04/04/7e7c858ea0f7bec611468f8137fc2218.png 

[T16]:https://us1.myximage.com/2018/04/04/351e0079a57d5beb631bb5a6aa954fa9.png 

[T19]:https://us1.myximage.com/2018/04/08/ee6c37fe9eb10a45b70dfd360c8a5228.png

<!--  
## 1. 介绍

### 1.1 结构框图

图 1 结构框图  
![][P1]

### 1.2 引脚及功能

图 2 引脚排列（顶视），4x4封装  
![][P2]

引脚功能


### 1.3 缩略词



## 2. 功能描述

### 2.1 工作模式

nRF24L01 可以设置为以下几种主要的模式


nRF24L01 在不同模式下的引脚功能
| 引脚名称 | 方向     | 发送模式                | 接收模式 | 待机模式 | 掉电模式 |
| -------- | -------- | ----------------------- | -------- | -------- | -------- |
| CE       | 输入     | 高电平>10us             | 高电平   | 低电平   | -        |
| CSN      | 输入     | SPI 片选使能 低电平使能 |
| SCK      | 输入     | SPI 时钟                |
| MOSI     | 输入     | SPI 串行输入            |
| MISO     | 三态输出 | SPI 串行输出            |
| IRQ      | 输出     | 中断 低电平使能         |


**待机模式**  

待机模式 I 在保证快速启动的同时减少系统平均电流消耗。在待机模式 I 下，晶振正常工作。在待机模式 II 下部分时钟缓冲器处在工作模式。当发送端 TX FIFO 寄存器为空并且 CE 为高电平时进入待机模式 II。在待机模式期间，寄存器配置字内容保持不变。

**掉电模式**

在掉电模式下，nRF24L01 各功能关闭，保持电流消耗最小。进入该模式后，nRF24L01 停止工作，但寄存器内容保持不变。启动时间见工作时序。掉电模式由寄存器中 PWR_UP 位控制。

**数据包处理方式**

有如下几种数据包处理方式：
- ShockBurst (TM) （与 nRF2401，nRF24E1，nRF2402，nRF24E2 数据传输率为 1 Mbps 时相同）
- 增强型 ShockBurst (TM) 模式

<u>ShockBust (TM) 模式</u>

ShockBust 模式下 nRF24L01 可与成本较低的 MCU 相连。高速信号处理是由芯片内部的射频协议处理的，nRF24L01 提供 SPI 接口，数据率取决于 MCU 本身接口速度。ShockBust 模式通过允许与 MCU 低速通信而无线部分高速通信，减小了通信的平均电流消耗。

在 *ShockBust 接收模式*下，当接收到有效的地址和数据时 IRQ 通知 MCU，随后 MCU 可将接收到的数据从 RX FIFO 寄存器中读出。

在 *ShockBust 发送模式*下，nRF24L01 自动生成前导码及 CRC 校验。数据发送完毕后 IRQ 通知 MCU。减少了 MCU 的查询时间，也就意味着减少了 MCU 的工作量，同时减少了软件的开发时间。nRF24L01 内部有三个不同的 RX FIFO 寄存器（6 个通道共享此寄存器）和三个不同的 TX FIFO 寄存器。在掉电模式下、待机模式下和数据传输的过程中 MCU 可以随时访问 FIFO 寄存器。这就允许 SPI 接口可以以低速进行数据传送，并且可以应用于 MCU 硬件上没有 SPI 接口的情况下。

<u>增强型的 ShockBust 模式</u>

该模式可使得双向链接协议执行起来更为容易、有效。典型的双向链接为：发送方要求终端设备在接收到数据后有应答信号，以便于发送方检测有无数据丢失。一旦数据丢失，则通过重新发送功能将丢失的数据恢复。该模式可以同时控制应答及重发功能而无需增加 MCU 工作量。

nRF24L01 在接收模式下可接收 6 路不同通道的数据，每一个数据通道使用不同的地址，但是共用相同的频道。也就是说 6 个不同的 nRF24L01 设置为发送模式后可以与同一个设置为接收模式的 nRF24L01 进行通讯，而设置为接收模式的 nRF24L01 可以对这 6 个发射端进行识别。数据通道 0 是唯一的一个可以配置为 40 位自身地址的数据通道。1 ~ 5 数据通道都为 8 位自身地址和 32 位共用地址。所有的数据通道都可以设置为增强型 ShockBust 模式。

nRF24L01 在确认收到数据后记录地址，并以此地址为目标地址发送应答信号。在发送端，数据通道 0 被用作接收应答信号，因此，数据通道 0 的接收地址要与发送端地址相等以确保接收到正确的应答信号。

    在发射器中，通道 0 要接收应答回来的信号，所以应该与发送通道地址相同。

nRF24L01 配置为增强型的 ShockBust 发送模式下时，只要 MCU 有数据要发送，nRF24L01 就会启动 ShockBust 模式来发送数据。在发送完数据后 nRF24L01 转到接收模式并等待终端的应答信号。如果没有收到应答，nRF24L01 将重发相同的数据包，直到收到应答信号或重发次数超过 `SETUP_RETR_ARC` 寄存器中设置的值为止，如果重发次数超过设定值，则产生 `MAX_RT` 中断。

只要收到确认信号，nRF24L01 就认为最后一包数据已经发送成功（接收方已经收到数据），把 TX_FIFO 中的数据清除掉并产生 `TX_DS` 中断（IRQ 引脚置高）。

在增强型 ShockBust 模式下，nRF24L01 有如下特征：
- 当工作在应答模式时，快速的空中传输及启动时间，极大地降低了电流消耗
- 低成本。集成了所有高速链路层操作，比如：重发丢失数据包和产生应答信号。无需单片机硬件上一定有 SPI 口与其相连。SPI 接口可以利用单片机通用 I/O 口进行模拟。
- 由于空中传输时间很短，极大的降低了无线传输中的碰撞现象。
- 由于链路层完全集成在芯片上，非常便于软硬件的开发。

*增强型 ShockBust 发送模式：*

1. 配置寄存器位 `PRIM_RX` 为低
2. 当 MCU 有数据要发送时，接收节点地址（`TX_ADDR`）和有效数据（`TX_PLD`）通过 SPI 接口写入 nRF24L01。发送数据的长度以字节计数从 MCU 写入 TX FIFO。当 CSN 引脚为低时数据被不断地写入。发送端发送完数据后，将通道 0 设置为接收模式来接收应答信号，其接收地址（`RX_ADDR_P0`）与~~接收端~~发送端地址（`TX_ADDR`）相同。
3. 设置 CE 为高，启动发射。CE 高电平持续时间最小为 10 μs。
4. nRF24L01 ShockBust 模式：
    - 无线系统上电
    - 启动内部 16 MHz 时钟
    - 无线发送数据打包（见数据包描述）
    - 高速发送数据（由 MCU 设定为 1 Mbps 或 2 Mbps）
5. 如果启动了自动应答模式（自动重发计数器不等于 0，ENAA_P0 = 1），无线芯片立即进入接收模式。如果在有效应答时间范围内收到应答信号，则认为数据成功发送到了接收端，此时状态寄存器的 `TX_DS` 位置高并把数据从 TX FIFO 中清除掉。如果在设定时间范围内没有接收到应答，则重新发送数据。如果自动重发计数器（`ARC_CNT`）溢出（超过了编程设定的值），则状态寄存器的 `MAX_RT` 位置高。不清楚 TX FIFO 中的数据。当 `MAX_TR` 或 `TX_DS` 为高电平时 IRQ 引脚产生中断。IRQ 中断通过写状态寄存器来复位。如果重发次数在达到设定的最大重发次数时还没有收到应答信号的话，在 `MAX_RX` 中断清除之前不会重发数据包。数据包丢失计数器（`PLOS_CNT`）在每次产生 `MAX_RT` 中断后加一。也就是说：重发计数器 `ARC_CNT` 计算重发数据包次数，`PLOS_CNT` 计算在达到最大允许重发次数时仍没有发送成功的数据包个数。
6. 如果 CE 拉低，则系统进入待机模式 I。如果不设置 CE 为低，则系统会发送 TX FIFO 寄存器中下一包数据。如果 TX FIFO 寄存器为空并且 CE 为高则系统进入待机模式 II。
7. 如果系统在待机模式 II，当 CE 拉低后系统立即进入待机模式 I。

*增强型 ShockBust 接收模式：*

1. ShockBust 接收模式是通过设置寄存器中 `PRIM_RX` 位为高来选择的。准备接收数据的通道必须被使能（`EN_RXADDR` 寄存器），所有工作在增强型 ShockBust 模式下的数据通道的自动应答功能是由（`EN_AA` 寄存器）来使能的，有效数据宽度是由 `RX_PW_Px` 寄存器来设置的。
2. 接收模式由设置 CE 为高来启动。
3. 130 μs 后 nRF24L01 开始检测空中信息。
4. 接收到有效的数据包后（地址匹配、CRC 检验正确），数据存储在 `RX_FIFO` 中，同时 `RX_DR` 位置高，并产生中断。状态寄存器中 `RX_P_NO` 位显示数据是由哪个通道接收到的。
5. 如果使能自动确认信号，则发送确认信号。
6. MCU 设置 CE 引脚为低，进入待机模式 I（低功耗模式）。
7. MCU 将数据以合适的速率通过 SPI 口将数据读出。
8. 芯片准备好进入发送模式、接收模式或掉电模式。

**两种数据双方向的通讯方式**

如果想要数据在双方向上通讯，`PRIM_RX` 寄存器必须紧随芯片工作模式的变化而变化。处理器必须保持 PTX 和 PRX 端的同步性。在 RX_FIFO 和 TX_FIFO 寄存器中可能同时存有数据。

**自动应答（RX）**

自动应答功能减少了外部 MCU 的工作量，并且在鼠标／键盘等应用中也可以不要求硬件一定有 SPI 接口，因此降低成本减少电流消耗。自动应答功能可以通过 SPI 口对不同的数据通道分别进行配置。

在自动应答模式使能的情况下，收到有效的数据包后，系统将进入发送模并发送确认信号。发送完确认信号后，系统进入正常工作模式（工作模式由 PRIM_RX 位和 CE 引脚决定）。

**自动重发功能（ART）（TX）：**

自动重发功能是针对自动应答系统的发送方。SETUP_PETR 寄存器设置：启动重发数据的时间长度。在每次发送结束后系统都会进入接收模式并在设定的时间范围内等待应答信号。接收到应答信号后，系统转入正常发送模式。如果 TX FIFO 中没有待发送的数据且 CE 脚电平为低，则系统将进入待机模式 I。如果没有收到确认信号，则系统返回到发送模式并重发数据直到收到确认信号或重发次数超过设定值（达到最大的发送次数）。有新的数据发送或 PRIM_TX 寄存器配置改变时丢包计数器复位。

**数据包识别和 CRC 校验应用于增强型 ShockBust 模式下：**

每一包数据都包括两位的 PID（数据包识别）来识别接收的数据是新数据包还是重发的数据包。PID 识别可以防止接收端同一数据包多次送入 MCU。在发送方每从 MCU 取得一包新数据后 PID 值加一。PID 和 CRC 校验应用在接收方识别接收的数据是重发的数据包还是新数据包。如果在链接中有一些数据丢失了，则 PID 值与上一包数据的 PID 值相同。如果一包数据拥有与上一包数据相同的 PID 值，nRF24L01 将对两包数据的 CRC 值进行比较。如果 CRC 值也是相同的话就认为后面一包是前一包的重发数据包而被舍弃。


【18/04/02 U】






-->



