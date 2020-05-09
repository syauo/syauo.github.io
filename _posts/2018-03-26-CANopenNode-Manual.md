---
layout: post
title: CANopenNode 用户手册
date: 2018-3-26
categories: [blog, Fieldbus]
description: CANopenNode 的介绍及使用方法。
keywords: CANopenNode, Embedded, Fieldbus, CANopen, PIC18F
---
 
> CANopenNode 是一款为单片机开发的开源程序。可以用在连接 CANbus 的多种设备（传感器，IO 模块，命令界面，控制器等）。程序根据 CANopen 标准编写，故设备可以与其他基于 CANopen 协议的设备通信。
>
> 原文件：    [CANopenNode Manual V1.1][R2]  
> 原作者：    [Janez Paternoster][R3]


**目录**

* TOC
{:toc}  

## 1. 介绍

**CANopenNode** 是一款为 ~~8 位~~单片机开发的开源程序。可以用在连接 CANbus 的多种设备（传感器，IO 模块，命令界面，控制器等）。程序根据 CANopen 标准编写，故设备可以与其他基于 CANopen 协议的设备通信。

### 1.1 CAN 与 CANopen 简述

CAN（Controller Area Network)最早是一款应用于汽车的串行总线系统。在工业自动化领域也有广泛应用。由于它具有成本效益（可以在多种8位控制器上实现），因此可大范围应用。


<u>CAN 的一些特性：</u>

- 成本效益
- 硬件实现
- 可靠（复杂的错误检测，重复的错误信息，对电磁干扰高度免疫）
- 灵活
- 消息长度：最高 8 字节
- 消息具有唯一的 CAN 标识符
- 仲裁不会浪费时间，例如高优先级消息在传输当前消息后立即发送
- 数据传输速率（电缆长度）：10 kbps(5 km)至 1 Mbps(25 m)
- 电缆连接无需 hub 与交换机。设备也可以与网络光电隔离

**CAN 可靠性（统计）**：如果基于 250 kbps 的网络每年运行 2000 小时，平均总线负载为 25％，则每 1000 年才会发生一次未检测到的错误。[CANopenBook]

**CAN** 是通信的较低层的实现。但是当我们需要设备之间的通信时，存在一些问题：使用哪个标识符，消息的内容是什么，如何处理错误，如何监视其他节点等。为避免重新发明轮子，于是有了 CANopen。

**CANopen** 是基于 CAN 的更高层协议之一。 它是开放的，这意味着人们可以使用它并根据需要进行定制。为了符合标准，基础实现是必须的。无论如何，CANopen 提供了许多有用的功能，可用于良好和可靠的通信。

<u>CANopen 的一些特性：</u>

- 具有 11 位标准 CAN 标识符，同一个网络可拥有高达 127 个节点。

- 不是典型的主从协议，故主节点是不必要的。但是某些功能只能在一个节点上使用，例如SDO
    客户端。该节点通常用于配置，我们可以将其称为主节点。

- **对象字典（OD）**：OD 内部是已排序的变量，由节点使用，可通过 CAN 总线访问。OD 具有 16 位宽的索引，并且对于每个索引有 8 位宽的子索引（例如，变量「设备类型」具有索引 0x1000 和子索引 0x00）。变量可以通过 CAN 以 **SDO（Service Data Objects）** 访问。变量可以是读/写，只读，等等。它们可以<u>保持</u>（XW-180326:掉电保持？）。变量的长度最多为 256 个字节（更长的变量通过分段传输进行传输）。使用在 PC 上运行的标准 CANopen 配置工具，OD 可作为树显示，因此可以轻松配置设备。使用 OD 可以访问很多变量，但这不是最快的方法。

- 在节点之间交换 **PDO（Process Data Objects）**以进行快速通信。PDO是高达8字节宽的数据对象，从一个节点传输到配置为接收它的节点。PDO 可以以不同的方式发送：周期性地按时间间隔，状态改变，与其他节点同步等。节点可以发送多达 512 个 PDO，并且可以从其他节点接收多达 512 个 PDO（预定义连接集提供 4 个 TPDO 和 4 个 RPDO）。

- **NMT（Network Management）**：网络管理对象包括：启动消息（Boot-up message），心跳协议(Heartbeat protocol)和 NMT 消息。每个节点可以处于四种状态之一：
    - 初始化 - initialization
    - 预操作 - preoperational
    - 运行 - operational
    - 停止 - stopped

    例如，PDO 只能在 Operational 状态下工作。

- **错误控制-心跳协议（Error control - Heartbeat protocol）**：它用于错误控制并标识节点及其状态的存在。心跳消息是节点到一个或多个其他节点的周期性消息。其他节点可以监视特定节点是否仍然正常工作。（除了心跳协议之外，还有一个陈旧过时的错误控制服务，称为节点和生命保护协议 - Node and Life Gurading protocol。）

- **Emergency message**:在节点发生错误或警告的情况下发送紧急消息。

- **电子数据表 EDS（Electronic Data Sheets）**：是 CANopen 中用于描述在特定节点中实现的对象字典的文本文件。使用它们可以在配置工具中配置 CANopen 网络。

**<u>为了理解 CANopenNode，需要进一步的知识。</u>必须了解微控制器，C 编程和 CANopen 协议。**见文献。

### 1.2 CANopenNode 功能 


- **配置宏（Macros for configuration）**：可以使用 *CO\_OD.h* 文件中的宏配置功能。如果功能裁剪或禁用，程序和内存占用小会减少。

- **CAN 比特率**：10，20，50，125，250，500，800，1000 kbps; 振荡器频率：4,8,16,20,24,32,40，... MHz; 8 位微控制器在 1000 kbps 比特率的 100％ 总线负载下也可以保持稳定。

- **CANopen 一致性**：符合 *[CiADS301]*，*[CiADR303-3]*。使用标准帧格式（11 位标识符）。RTR 位可用;

- **Service Data Objects（SDO）**：实现 SDO 服务端和 SDO 客户端，加速和分段传输。变量可长达 256 字节;

- **对象字典（OD）**分两个方面：

    1. <u>CANopen 方面</u>：通过 SDO（只读，读/写等）通过索引，子索引和长度来访问变量。在写入内存之前，可以验证它们的正确性。 

    2. <u>程序方面</u>：变量具有普通名称（无索引和子索引）。它们可以位于 RAM 存储器空间，EEPROM 或 Flash 闪存程序空间中。EEPROM 和 Flash 变量具有掉电保持性（断电后保持数值）并可通过 SDO 进行更改。实现起来简单，快速和灵活;

- **Process Data Objects（PDO）**：实现 TPDO 和 RPDO。自动同步传输，其他方法也是可以的。参数是 COB-ID，传输类型，Inhibit time（抑制时间）和事件定时器。Mapping （映射）是静态的，必须是「手工制作的」。PDO 的长度根据映射计算;

- **SYNC对象**：精度为 1 ms 的生产者或消费者;

- **网络管理（NMT）**：已实现，可以使用或不使用 NMT 主站;

- **心跳监控**：心跳生产者和消费者。（监控多个节点的存在和 NMT 状态）;

- **用户 CAN 消息**：可自由使用的 RX 或 TX CAN 消息。

- **错误控制**：使用紧急对象。已实现捕捉不同错误的机制。对于发生的每个错误，设置不同的标志位并发送紧急消息。根据标志位计算错误寄存器，并且可以将设备置于 Pre-Operational 状态。通过对象字典条目（索引 0x2100）可以轻松地跟踪所有错误。这种机制也可以用于用户定义的错误;

- **状态 LED**：绿色和红色 LED，根据 *[CiADR303-3]*;

- **通用 IO 设备示例 Generic Input/Output**：数字 IO，模拟 IO，根据事件和 Inhibit time 改变传输状态。根据设备配置文件 *CiADS401*。

- **操作面板示例 Command Panel**：带 PIC，数字小键盘和字母数字 2x16 LCD 显示器。SDO 客户端。易于配置的参数用于访问网络上任何节点的对象字典变量。密码保护。自定义屏幕。可以使用。

- **以太网到 CANopen 接口的示例 Ethernet to CANopen interface**：带有 Beck IPC@chip SC1X Web 控制器和 SJA1000 CAN 控制器。

    + <u>HTML 界面 1：</u>状态，心跳消费者，NMT 主设备，接收 PDO，传输消息，SDO 客户端。

    + <u>HTML 界面 2：</u>易于配置的参数，用于访问网络上任何节点的对象字典变量。
    
    + <u>HTML 界面 3：</u>具有 1 ms 时间戳和 64 kb 内存转储的 CAN 跟踪。可以使用。

- **EDS**：包含每个示例的电子数据表。


### 1.3 V1.10 版本更新

版本 1.10 是 CANopenNode 的最终和广泛测试版本。它包含了所有文档：本手册，教程和 EDS。许多示例都可用。

该版本的结构与以前的版本不同。但基本上，文件中的代码保持不变。同样也有与用户应用程序的接口 - *User.c* 文件。因此，如果您想将旧的 *CANopenNode* 项目移植到这个新版本，最好的解决方案是使用该版本的结构和文件，并将旧项目的代码复制到新的版本中。对于 *CO_OD.c* 和 *CO_OD.h* 文件尤其如此。

如果没有必要，用户不应更改 *CANopen* 文件夹内的文件。对于更高版本的 CANopenNode，只需要升级这些文件。


## 2. 设计

CANopenNode ~~目前~~(很久很久以前 :smile:)为以下设计：  
- 8 位 Microchip PIC18FXXX 和 MPLAB C18 编译器（>= V3.00）。
- 带 Philips SJA1000 和 Borland C ++ 5.02 编译器的 16 位 BECK SC1X。
- 可以翻译成与其他编译器和微控制器一起使用。

实现 CANopen 和用于用户程序的框架。目标是简单，强大并且适用于扩展。

CANopenNode 是开源的。使用 `LGPL许可证`，这意味着许可证只能在库上执行，而不能在完整的用户程序上执行。

### 2.1 文件

    
+ **\_doc文件夹** —— 说明文件
    - Manual.pdf —— 该手册的 pdf 版
    - Manual.odt —— 该手册的 OpenOffice 格式
    - Tutorial.pdf —— 使用指南的 pdf 版
    - Tutorial.odt —— 使用指南的 OpenOffice 格式
    - fdl.txt —— GNU 免费文档许可
+ **\_src文件夹** —— CANopenNode 源文件。分为 CANopen 文件夹和多个示例文件夹。
    - lesser.txt —— GNU 较低通用公共许可证
    - readme.txt —— 说明，如何打开示例项目
    - *.mcp,*.ide 文件 —— 示例工程文件
    - **CANopen文件夹** —— 除对象字典外，所有 CANopenNode 特定文件。在一个具有多个 CANopen 设备的项目中，该文件夹对于所有设备（如库）可以是通用的：
        - CANopen.h —— CANopenNode 的主头文件。 包括一般定义和其他头文件;
        - CO_stack.c —— 大部分 CANopenNode 代码;
        - CO_errors.h —— 所有错误的定义，可能发生在程序中。还为紧急错误代码和用于计算错误寄存器的定义赋值。对于用户程序，可以添加用户错误的定义;
        - CO_OD.txt —— 对象字典条目和 SDO 中止代码的简短描述;
        - **子文件夹** —— 微控制器特定代码：
            - main.c —— 中断，timer1ms 和主函数的初始化和定义;
            - CO_driver.h —— 处理器/编译器特定的宏;
            - CO_driver.c —— 处理器/编译器特定函数;
            - 其他特殊文件
    - **\_blank_project** —— 示例项目，它没有用户特定的代码。 适用于所有微控制器：
        - CO_OD.h —— 对象字典的头文件和 CANopenNode 的主设置;
        - CO_OD.c —— CANopenNode 的变量，验证函数和对象字典;
        - user.c —— 示例函数，可以在用户程序中使用，并且必须定义。
        - _blank_project.eds —— EDS 使用默认配置;
    - **其他示例项目文件夹** —— 请参阅手册和教程。


### 2.2 程序流程图 （主流程和定时器程序）


CANopenNode 程序执行分为三（四）个任务：

- 启动后，基本任务是主线程序。它在无限循环内执行。在 *CO\_ProcessMain* 函数中处理非时间关键的程序代码。所有代码都是非阻塞（non-blocking）的。

- 第二项任务是由中断触发的定时器程序，每 1 ms 执行一次。这是处理时间关键的代码。代码必须很快。如果执行时间大于 1 ms，则会发生溢出，设置错误位并发送紧急消息。

- 第三和第四个任务是 CAN 发送／接收中断。CAN 接收中断必须优先于定时器程序。

蓝色背景的地方是可以写入用户代码的函数。除此之外，用户还可以定义其他中断（当然也可以在多任务系统中执行其他任务）。

图2.1 主线和定时器程序流程图
![][P2-1]

### 2.3 复位节点与复位通信程序

在 CANopen 中定义了两种不同的复位：『复位节点』（`Reset Node`）和『复位通信』(`Reset Communication`)。使用来自 NMT 主节点的 NMT 命令可以触发两者。第一次复位是完成处理器复位并在处理器启动后执行，第二次是部分复位并用于通信复位。在 `CO_ResetComm()` 中设置了所有 CANopenNode 特定的变量。因此，例如，如果 PDO 通信参数已更改，则必须执行「复位通信」以使更改生效。

蓝色背景的地方是可以写入用户代码的函数。

图2.2 复位节点与复位通信程序
![][P2-2]


### 2.4 CAN 消息，接收／发送

CAN 消息的接收和发送由中断功能完成。这些功能是硬件专用的。它们已经集成了一些CANopen 特定的代码。

#### 2.4.1 CAN 消息的变量

CAN 消息的主要变量是数组 `CO_RXCAN[]` 和 `CO_TXCAN[]`（RX = receive，TX = transmit）。每个数组中的成员数量等于不同 CANopen 通信对象的数量（COB = communication object 通信对象）。CANopenNode 中的所有东西都会「转向」这两个数组。有关说明，每个数组中使用哪些 COB，请参阅 *CANopen.h* 文件。

每个数组中的一个元素的类型如下所述：

```c
typedef struct{
    tData2bytes Ident;
    unsigned int NoOfBytes  :4;
    unsigned int NewMsg     :1;
    unsigned int Inhibit    :1;
    tData8bytes Data;
}CO_CanMessage;
```

- `Ident` 是 CAN 消息标准标识符与硬件寄存器对齐。它包括 11 位 COB-ID 和远程传输请求位（RTR）。为了对齐，使用 *CO_driver.h* 中的宏 `CO_IDENT_WRITE（CobId，RTR）`。如果 CAN-ID 和 RTR 匹配，则接收消息。
- `NoOfBytes` 是消息中的数据长度（CAN 使用 0 到 8 个字节）。对于接收，有一个特殊的规则：如果值大于 8，则不检查消息长度，因此接受任何长度的消息。
- `NewMsg` 是一个标志：对于接收，这个位在接收到新消息时置位；对于发送，该位『通知』TX中断程序，该消息已准备好发送。
- `Inhibit`  标志有不同的含义。对于接收，`if（Inhibit == 1 AND NewMsg == 1）`，
    从总线收到的新消息的数据不会被复制。例如，如果旧消息还没有被读取，则新消息丢失。对于发送，标志用于同步 TPDO 消息。如果 `Inhibit == 1` 且时间在 SYNC 窗口外（OD，索引 1007h），则消息被销毁并且不通过 CAN 发送。
- `Data` 8 个字节的 CAN 数据。

在通信复位时，首先整个数组清零，然后除了 Data 之外的所有值都被初始化（在`CO_ResetComm()`函数内部）。这样可以实现最少的数据复制，并且不需要其他缓冲区。

#### 2.4.2 接收 CAN 消息

CANopen 使用许多不同的 CAN 标识符，因此不能总是使用消息的硬件过滤。相反，过滤是用软件来实现的。原则如下：在当前配置中使用的所有 COB 的标识符在启动时写入 `CO_RXCAN[]` 数组中。当新的消息从 CAN 总线到达时，触发 `CO_CANrxIsr()` 中断，并从第一个元素到最后一个元素搜索数组。如果来自消息和数组的两个 COB-ID 都匹配，则消息将进一步处理。如果不是，中断退出。

该中断必须具有高优先级，并且必须具有低延迟，因为必须过滤掉许多消息。在 CANopenNode 中，它使用快速汇编代码实现，所以即使在高 CAN 比特率和高总线负载情况下也没有问题。

接收到的消息在不同的功能中处理。细节将在下面的章节中介绍。

图 2.3 接收 CAN 消息  
![][P2-3]

#### 2.4.3 发送 CAN 消息

要发送 CAN 消息的函数首先准备足够的 `CO_TXCAN[]` 数组元素：写入 `Data`，并且必要时还可以修改其他参数。然后它调用 `CO_TXCANsend()` 函数。如果 CAN TX 缓冲区空闲，消息将立即发送，否则将被标记，中断程序将发送它。索引值 index 较低的消息（`CO_TXCAN [index]`）将首先发送。

图 2.4 发送 CAN 消息  
![][P2-4]

### 2.5 主线程序


如第 2.2 章所述，有两个处理 CANopen 消息的『任务』。定时器程序较短，并在该章中描述，此处描述主线程序。

`CO_ProcessMain()` 在无限循环内执行。处理的程序代码是非时间关键的。所有代码都是非阻塞的。有时需要处理大量的代码，因此可能导致更长的延迟和更长的执行周期。例如，SDO 通信非常耗时。

在同一个循环中，用户函数 `User_ProcessMain()` 也正在处理中。也可能会耗费时间并且是非时间关键的代码。无论如何，不可使用阻塞函数（blocking functions）。

图 2.5 CANopenNode 主线处理流程图  
![][P2-5]


### 2.6 COB - 通信对象


CANopen 中的预定义连接集将通信对象与其标识符连接起来。它适用于带有 11 位标识符的标准 CAN 帧。特别是对于 PDO，使用预定义值不是强制规定。

表 2.1 预定义连接集

| Object      | COB-ID         |
| ----------- | -------------- |
| NMT SERVICE | 000h           |
| SYNC        | 080h           |
| EMERGENCY   | 080h + NODE ID |
| TIME STAMP  | 100h           |
| TPDO1       | 180h + NODE ID |
| RPDO1       | 200h + NODE ID |
| TPDO2       | 280h + NODE ID |
| RPDO2       | 300h + NODE ID |
| TPDO3       | 380h + NODE ID |
| RPDO3       | 400h + NODE ID |
| TPDO4       | 480h + NODE ID |
| RPDO4       | 500h + NODE ID |
| TSDO        | 580h + NODE ID |
| RSDO        | 600h + NODE ID |
| HEARTBEAT   | 700h + NODE ID |

        
#### 2.6.1 NMT 和网络管理

CANopen 标准详细介绍了网络管理。通常每个节点有 4 种可能的状态：初始化，预操作，运行和停止。在运行状态下，所有通信都是允许的。在预操作状态下，除PDO外，所有通信都是允许的。在停止状态下，只能接收和处理 NMT 消息。处理这些状态非常棘手，尤其是在需要可靠通信的情况下。

默认情况下，节点在启动后进入操作状态。这是在索引为 0x1F80 的对象字典中的变量 `NMT startup`中设置的。如果2号位置位，则节点将在启动后开始预操作。

还有另一个问题。如果节点中有错误，节点有时不应该进入操作。设置错误寄存器（OD，索引 0x1001）时会发生这种情况。在 *EMERGENCY* 章节有更多介绍。

CANopenNode 中收到的 NMT 消息在 `CO_ProcessMain()` 函数中处理。根据命令，动作触发。NMT 主站可以用用户定义的 CANTX 消息轻松实现。NMT 主站可以将特定的 NMT 命令发送到单个节点或所有节点（广播）。

#### 2.6.2 SYNC

SYNC 消息对同步很有用。网络上的一个节点以固定的时间间隔周期性地发送它。它可以用于不同的目的，其中一个使用同步 PDO。

SYNC 消息集成到 CANopenNode 中。它在 Timer 程序内处理。基本时间单位是 1 ms。CANopenNode可以是生产者或消费者。

有两个与 SYNC 相关的有用变量：`CO_SYNCtime` 和 `CO_SYNCcounter`（类型为`unsigned int`）。每次接收 / 发送 SYNC 时，`CO_SYNCtime` 每毫秒增加一次并复位为零。`SYNCcounter` 每次递增，SYNC 被接收/发送。

#### 2.6.3 紧急和错误处理

错误处理在网络的实际实施中是非常有用的。在测试阶段，软件中可能存在一些错误，甚至在网络的电子或机械部分存在一些错误。通过错误处理，可以跟踪许多这些错误。

在 CANopenNode 中，错误处理变得简单。 一般来说，当节点发生错误时，节点不应该崩溃，它应该进一步操作。

原则如下：如果程序中某处出现错误（满足某些条件），则调用 `ErrorReport()` 函数，该函数仅设置一些变量。反过来，错误处理在 `CO_ProcessMain()` 函数内处理。处理特定定义的所有错误都收集在文件 *CO\_errors.h* 中。
以下函数接受两个参数：

```c
void ErrorReport(unsigned char ErrorBit, unsigned int Code);
```
    
`ErrorBit` 对于每种不同的错误情况都是唯一的，`Code` 是客户特定的错误附加信息。如前所述，该函数只设置了一些变量，除此之外，它在 `CO_ErrorStatusBits` 数组中设置适当的位。如果之前已经设置了该位，则不会发生任何事情。这种方式只会在第一次报告具体的错误，稍后的重复会被忽略。

`ErrorReport()` 的相反函数是 `ErrorReset()`，如果错误以某种方式解决，那么特定位将被清除。

如果发生新错误，`CO_ProcessMain()` 函数中的过程确认新情况。首先，从`CO_ErrorStatusBits` 数组计算错误寄存器（OD，索引 0x1001）。然后发送 EMERGENCY 消息。紧急情况也写入历史（OD，索引 1003）。

紧急消息长度为 8 个字节。前 3 个字节是标准的：前两个字节是紧急错误代码，第三个字节是错误寄存器。第 4 个字节是 `ErrorBit`，第 5 - 6 个字节是来自 `ErrorReport()` 函数的代码。第 7 和第 8 字节可以是用户特定的。

1 个字节长的错误寄存器中的每一位都是从 `CO_ErrorStatusBits` 数组的状态计算出来的。请参阅 *CO_errors.h* 文件中的宏 `ERROR_REGISTER_BITn_CONDITION`。当特定位的条件满足时，该位置 1，反之亦然。在某些情况下，这些条件可能更具限制性，在其他情况下则不是。

<u>如果错误寄存器不等于零，则禁止节点处于操作状态，并且不能发送或接收PDO。</u>

如果 *CO_OD.h* 文件中的宏 `ODD_ERROR_BEH_COMM` 设置为 1，则错误寄存器中的通讯错误位不会阻止节点处于运行状态。在这种情况下，即使与网络断开连接，节点也将始终保持运行。

#### 2.6.4 时间戳
未在 CANopenNode 中实现。

#### 2.6.5 PDO - Process Data Objects

PDO 针对数据的频繁传输进行了优化。与服务数据对象（SDO）相比，它们更快，更
短，无需协议开销并且无需应答。整个 8 位宽的 CAN 数据字段用于过程数据。

CANopen 使用两个参数设置 PDO：PDO 通讯参数（Communication Parameters）和 PDO 映射参数（Mapping Parameters）。为了正确使用 PDO，需要了解它们。它们在其他文献中有描述。

**Mapping Parameters** 映射参数描述了对象字典中的哪些数据用于特定的 PDO。在
 CANopenNode 中，所有的映射都是静态的，这意味着在程序编译后映射不能被改变。在 CANopen 节点中，映射参数用于计算 PDO 的长度。当收到 RPDO 时，长度必须与映射中指定的长度相匹配，否则 RPDO 将不会被处理，紧急情况将首次发送。如果通过禁用宏 `CO_PDO_MAPPING_IN_OD` 来禁用映射参数，则 TPDO 的长度将固定为8个字节，并且任何长度的 RPDO 都将被接受。映射参数也可以在 *CO_OD.h* 文件中禁用。在这种情况下，TPDO 长度将固定为 8 个字节，并且任何 RPDO 长度都将被接受。

**Communication Parameters** 通信参数描述 PDO 通信对象（COB-ID）的 CAN ID，传输类型，Inhibit time 和事件定时器。

**COB-ID** 发送节点上的 COB-ID 必须与所有接收节点上的 COB-ID 相匹配。在 CANopen 上，只有一个规则：在一个网络上不得有两个相同的 COB-ID 用于传输消息。无论如何，CANopen 具有预定义的连接集，其中定义了 4 个 TPDO 和 4 个 RPDO COB-ID。在CANopenNode 中可以使用标准或自定义值。<u>如果第 31 位 == 1，则不使用 PDO。</u>

**Transmission Type** 传输类型描述何时传输 PDO。有更多的可能性：时间间隔，状态改变，它们的组合，在远程传输请求（RTR - CAN 特性）之后，或者在 SYNC 消息之后同步。

**<u>CANopenNode 中 PDO 的发送</u>**

可以采用多种方式。

*Synchronous transmission 同步传输* 已在 CANopenNode 中实现。消息在每第 n 个SYNC 消息后自动发送（n = 传输类型变量 1 ... 240 的值）。这是可预测的。

*Event timer 事件定时器* 按时间间隔发送消息，并在 CANopenNode 中自动发送。

一种可能性是 *Change-of-state 状态改变* 和（更长）时间间隔的组合。这种方式很好，在这种情况下，变化并不经常。优势是响应速度快，流量低。问题来自模拟传感器的值，其中最不重要的位经常变化。这导致许多不必要的消息。另一个问题是，总线负载不能总是预测。该示例在通用 IO 示例中使用。

Data,与 TPDO 一起发送的数据必须在发送 PDO 之前手动写入。它们必须写入数组 `CO_TPDO(i)`，其中索引是 PDO 编号（0 是第一个 PDO）。数组元素的类型是`tData8bytes`。因为宏语法，所以使用括号 `()` 代替 `[]`。

要手动发送 PDO，只需调用以下函数：

```c
char CO_TPDOsend(unsigned int index);
```

其中 `index` 是 PDO 编号（0 是第一个 PDO）。如果 return == 0，则传输成功。PDO 会（可能）只在节点处于操作状态时被发送。

**<u>CANopenNode 中 PDO 的接收</u>**

当 PDO 从 CAN 总线到达时，它被存储。无论如何，只有在节点处于运行状态时才能使用 PDO。

可以从 `CO_RPDO(i)` 数组读取 RPDO 数据，其中索引是 PDO 编号（0 是第一个 PDO）。数组元素的类型是 `tData8bytes`。因为宏语法，所以使用括号 `()` 代替 `[]`。

`CO_RPDO_New(i)` 数组元素每次被设置为 1，PDO 被接收。用户可以手动删除它。

用户必须注意一个问题：PDO 被高优先级中断接收，所以如果用户在读取数据期间发生中断，则数据可能无法预测。因此在读取期间，应禁用 CANrx 中断（请参阅 *CO_driver.h*）。

另一个问题是 CANopen 标准中的规则，其中在下一个 SYNC 消息之后必须处理同步 PDO。但是接下来的 SYNC 消息也会在下一个 RPDO 到达并覆盖旧的 PDO。解决方法是：在 Timer1ms 程序中，除 `CO_SYNCtime == 0` 外，将所有新的 RPDO 保存到不同的位置。如果是这样，则处理先前复制的 RPDO。

如果节点处于运行状态，则收到 PDO 总是被没有控制地保存。

为了确保 PDO 接收工作正常，在读取 RPDO 数据之前必须满足以下条件：该节点和发送节点必须都处于运行状态。因此检查 `CO_RPDO_New(i)` 是不必要的。若扫描其他节点，使用心跳消费者（Heartbeat consumer）。

#### 2.6.6 SDO - Service Data Objects

使用 SDO 可以访问 CANopen 网络上任何节点的全部对象字典。SDO 客户端（主）可访问其他节点上的 SDO 服务器。

SDO 服务器集成在 CANopenNode 中。最大可变长度可以从 4 … 256 字节设置。如果最大长度为 4 个字节，则只使用加速传输（expedited transfer）。否则，使用分段传输（segmented transfer）并消耗更多的闪存。可以使用多于 1 个 SDO 通道。

还有 SDO 客户端无阻塞功能，因此 CANopenNode 也可以用作主站。如何使用它们，请参阅函数 `CO_SDOclientDownload_wait()` 和 `CO_SDOclientUpload_wait()`。

SDO 通信可以访问许多变量，但速度不是很快。这是相当耗时的，尤其是在 PIC 单片机中。但它对于设置节点非常强大。

**CANopenNode 中的设计**：SDO 服务器是在主线功能内实现的状态机。当新的 SDO 对象从客户端到达时，会进行处理并设置一些静态变量。根据命令和状态：使用正确的索引和子索引搜索对象字典（OD），进行验证，必要时发送中止，收集数据段，从 OD 读取或写入变量等。对于 CANopenNode 用户，无需了解 SDO 对象的细节知识。

#### 2.6.7 心跳 Heartbeat

心跳协议用于监视远程节点的正常运行。在 CANopenNode 中实现了 Heartbeat 生产者和消费者。过时的「Node Guarding 节点保护」未实现。

使用心跳不需要主站，每个节点都可以监控对其非常重要的节点。

要设置生产者，必须编辑对象字典中相应的条目。节点将产生周期性的心跳消息。

心跳消费者监视对象字典中相应条目中定义的节点的存在和状态。接收到指定节点的第一个心跳后开始监测。如果心跳没有在特定时间到达，则生成紧急消息。心跳消费者时间值应设置为 `1.5 * 远程节点上的心跳生产者时间值`。远程节点的状态可以从 `CO_HBcons_NMTstate(i)` 数组读取。数组元素的类型是 `unsigned char`。值在 *CANopen.h* 中定义。因为宏语法，所以使用括号 `()` 代替 `[]`。

#### 2.6.8 用户定义的通信对象

除了标准的 COB，用户可以定义他自己的，例如 NMT 主消息。

rx（tx）消息的用法：  
1. 将 `CO_NO_USR_CAN_RX（CO_NO_USR_CAN_TX）` 定义设置为大于 0。
2. 初始化 `CO_RXCAN[CO_RXCAN_USER + i]`（`CO_TXCAN[CO_TXCAN_USER+i]`）数组。0 < i < `CO_NO_USR_CAN_RX`（0 < i < `CO_NO_USR_CAN_TX`）。不要设置 `NewMsg` 标志！
3. 当数组的 `NewMsg` 位为 1 时，从 `CO_RXCAN[CO_RXCAN_USER+i]` 读取接收到的数据。（将数据写入 `CO_TXCAN [CO_TXCAN_USER + i]` 并调用 `CO_TXCANsend`（`CO_TXCAN_USER + i`）发送消息）。

NMT 主消息示例（不要忘记第 1 点）：

```c
CO_TXCAN[CO_TXCAN_USER].Ident.WORD[0] = CO_IDENT_WRITE(0/*COB-ID*/, 0/*RTR*/);
CO_TXCAN[CO_TXCAN_USER].NoOfBytes = 2;
CO_TXCAN[CO_TXCAN_USER].Inhibit = 0;
CO_TXCAN[CO_TXCAN_USER].Data.BYTE[0] = NMT_RESET_COMMUNICATION;
CO_TXCAN[CO_TXCAN_USER].Data.BYTE[1] = 0;
CO_TXCANsend(CO_TXCAN_USER);
```

### 2.7 对象字典

对象字典是 CANopen 最强大的功能之一。它就像一个表格，收集所有网络可访问的数据。每个条目都有 16 位索引和 8 位子索引。每个条目的数据类型可以不同——从 8 位无符号到字符串。

在 CANopenNode 中对象字典是一个条目数组 `CO_OD[]`。每个条目都有关于索引，子索引，属性（只读，读/写等），长度和指向数据变量的指针的信息。

对象词典有「两个方面」：
1. 程序方面：变量具有普通名称（无索引，子索引）和类型;
2. CANopen 方面：普通变量被收集在 `CO_OD` 数组中，因此 SDO 服务器可以通过索引，子索引和长度（只读，读/写等）访问它们。

#### 2.7.1 变量的存储类型

在 CANopenNode 中，在对象字典中使用了三种类型的变量：
- ROM 变量，程序存储器 flash 空间中分配的变量;
- RAM 变量，RAM 空间中分配的变量;
- 在 RAM 空间中分配并从 EEPROM 中保存／加载的变量。
    
1. ROM 变量的特点：

    - 掉电不丢失;
    - 芯片编程时写入初始化值;
    - 像正常变量一样可读取;
    - 运行时可用 SDO 对象写入;
    - 用户程序不能直接写入变量;
    - 可能的问题：如果写入数据期间 PIC18Fxxx 复位可能会损坏;
    - 在 PIC18Fxxx 中，不使用 RAM;
    - 在对象字典中，它用属性 `ATTR_ROM` 标记。

2. RAM 变量的特点：

    - 经典读／写;
    - 关机后数值丢失。
    
3. EEPROM 变量的特点：
    - 从 RAM 中读取（写入）变量（在 ODE_EEPROM 结构体中分配）;
    - 芯片初始化时值从 EEPROM 读取;
    - 在运行时，后台例程比较 RAM 和 EEPROM，如果不同（PIC18Fxxx）则逐字节保存或数据在电源故障信号（SC12）后保存;
    - 用户程序经典写入变量;
    - 可能的问题：如果在写入期间微控制器复位：多字节变量可能会损坏（PIC18Fxxx）。

读取和写入不同类型的变量是特定于处理器的，因此这两种功能对于不同的微控制器是不同的。它们被放置在 *CO_driver.c* 文件中。

#### 2.7.2 变量和对象字典之间的连接

- 为每个变量分配一个 `CO_OD` 数组的条目;
- 条目保存关于索引，子索引，属性，长度和指向变量的指针的信息;
- 属性：

    + ATTR_RW - 读写;
    + ATTR_WO - 只写;
    + ATTR_RO - 只读（TPDO 可以从该条目读取）;
    + ATTR_CO - 只读，常量;
    + ATTR_RWR - 读取／写入过程输入（TPDO 可以从该条目读取）;
    + ATTR_RWW - 读取／写入进程输出（RPDO 可以写入该条目）;
    + ATTR_ROM - 变量保存在保持性存储器（ROM 变量）中;
    + ATTR_ADD_ID - 将 NODE-ID 添加到变量值（sizeof（variable）<= 4）;

- 当SDO服务器想要在对象字典中查找条目（使用特定的索引和子索引）时，它会调用 `CO_FindEntryInOD()` 函数。函数返回指向该条目的指针。

#### 2.7.3 验证函数

当正在写入对象字典条目时，从 SDO 服务器调用函数 `VerifyODwrite()`。当来自网络的数据是已知的时，函数被调用。功能验证数据值是否正确，然后返回 SDO 中止代码。如果返回 0，则数据是正确的并写入对象字典。函数不验证长度——它由 SDO 服务器验证。


## 3. 硬件和编译器特定的文件

主要的硬件特定文件是：

- main.c - 中断，timer1ms 和主函数的初始化和定义;
- CO_driver.h - 处理器／编译器特定的宏;
- CO_driver.c - 处理器／编译器特定功能。

这些文件包含：

- 基本的主函数和中断服务函数;
- 硬件宏：用于绿色和红色 LED 的引脚等。
- 不同的软件和中断启用／禁用宏;
- 用于从硬件读取节点 ID 和比特率的功能;
- CAN 控制器初始化功能;
- 向 CAN 控制器写入消息的功能;
- 接收来自 CAN 控制器的消息并识别它们的中断。必须足够快速;
- 处理来自 CAN 控制器的错误;
- 从／向对象字典读取／写入数据。数据可以在 RAM 或 ROM 存储器中;
- 处理 EEprom 变量。

### 3.1 PIC18 和 Microchip C18

PIC18Fxxx 是 Microchip(R) 提供的 8 位微控制器系列。CANopenNode 专为具有集成 CAN模块的用户设计。它使用 PIC18F458 进行了测试。使用的编译器是 Microchip MPLAB C18 >= V3.00。

其他文件包括：
- memcpyram2flash.h - memcpyram2flash.c 的头文件;
- memcpyram2flash.c - PIC18Fxxx 特定功能，将 RAM 变量的内容复制到 Flash 程序存储器空间中。完成擦除和写入循环需要 20 - 30 毫秒。函数还使用 64 字节的堆栈进行数据备份。在此期间，微控制器被冻结，不得失去电源。当写入对象字典 ROM 变量时，使用函数;
- 18f458i.lkr - 编译器的默认链接器脚本。
- CONFIG18f458.c - 微控制器的配置位。

使用的存储器类型是：RAM，ROM 和 EEprom。完全支持通过 SDO 读／写。

ROM 变量使用程序闪存并利用自编程功能。

EEprom 变量在启动时从内部 EEprom 读取到 RAM 中。如果 RAM 改变，Eeprom 自动更新。

#### 3.1.1 硬件设计

具有集成 CAN 模块和 CAN 收发器的 PIC18Fxxx 器件。
绿色 LED 连接到 RB4，红色 LED 连接到 RB5（可更改）。

#### 3.1.2 PIC18F458 中的资源

| 资源                 | 描述                                             |
| -------------------- | ------------------------------------------------ |
| CAN 模块             |
| PortB:RB2,RB3        | CANTX 和 CANRX 接线                              |
| INT2                 | 外部中断 2 – 与 RB2 复用                         |
| PortB:RB4,RB5 (默认) | LED 指示，引脚可以替换或不用                     |
| TIMER2 模块          | 用于 1ms 定时中断                                |
| PWM 模块             | 可以自由使用，除非 PWM 频率由 TIMER2 初始化设置  |
| Interrupts           | 高低优先级中断均使用，用户可以添加自己的中断资源 |
| Program memory       | 17 kB (52%) 默认情况\*                           |
| RAM memory           | 662 bytes (43%) 默认情况\*                       |

> \* 默认情况是基于 \_blank\_project：4 个 RPDO，4 个 TPDO，4 个心跳消费者，8 个错误字段，最大 OD 条目大小 = 20 个字节，闪存和 EEPROM 写入启用，PDO 参数和映射在对象字典中，map size= 8。计算中包含堆栈和 SFR 寄存器。 MPLAB C18 编译器。

#### 3.1.3 PIC18F458 的特性

进行了一些测量以检查驱动的表现。  

**<u>设置：</u>**

- PIC 的石英晶振：10 MHz，PLL 使能，产生 40MHz 时钟频率和 10 MIPS;
- CAN 波特率：1 Mbps;
- 4 个发送 PDO，4 个接收 PDO，4 个心跳消费者;
- 使用不同标识符接收 11 个不同的 CAN 消息，不使用硬件过滤。
    
|                                     | 值        | 描述                                                                                                                                                    |
| ------------------------------------- | --------- | ------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Low Interrupt Latency                 | 19 μs     | 中断上下文保存。<br/>在中断结束时，上下文被恢复并且必须考虑附加的延迟。                                                                                     |
| Low Interrupt <br/>– 发送消息              | Max 30 μs |
| Low Interrupt <br/>– 定时器                | 6 μs      |
| Low Interrupt  <br/>– 准备同步传输 PDO 4x. | 55 μs     |
| High Interrupt Latency                | 1.5 μs    | 中断上下文保存。<br/>在中断结束时，上下文被恢复并且必须考虑附加的延迟。高优先级中断用于接收 CAN 消息。<br/>如果不同 CAN ID 的数量小于或等于 6，则使用硬件过滤。 |
| High Interrupt <br/>– 处理消息             | Max 14 μs |
| main() <br/>- 平均时间                     | 40 μs     |
| Process SDO <br/>– 读／写 RAM               | 160 μs    | CO_OD数组具有有序索引和子索引，因此即使对于大型对象字典，搜索速度也很快                                                                               |
| Process SDO <br/>– 写 ROM.                 | 20 ms     | 在此期间，微控制器被冻结。如果这是个问题，请在ODE_EEPROM结构中使用RAM变量。                                                                             |

### 3.2 BECK SC1x + SJA1000 （略）

## 4. 示例和对象字典

示例中的主要文件有：

- **CO_OD.h** - CANopenNode 的定义，对象字典的默认值;
- **CO_OD.c** - 用于 CANopenNode 的变量，验证函数和对象字典;
- **user.c** - 示例函数，可以在用户程序中使用，并且必须定义。
- **\*.eds** - 为 CANopen 配置工具准备的电子数据表。

这些文件包含：

- 用于配置 CANopenNode 的宏;
- 对象字典的全局变量;
- 变量的默认值;
- 对象字典，其中存储了上述变量的地址，索引，子索引和属性;
- 验证函数 `CO_OD_VerifyWrite()`，它在写入对象字典之前验证变量的值;
- 可以执行用户代码的函数：`User_Init()`，`User_ResetComm()`，`User_ProcessMain()` 和 `User_Process1msIsr()`。

### 4.1 \_blank\_project

完整的 CANopenNode。用户功能是空的。 它与微控制器无关。

### 4.2 Example\_generic\_IO

用于实现标准 CANopen 通用输入／输出模块（*CiADS401*，数字量输入，输出，模拟量输入，输出）的示例。它基于 *_blank_project* 示例。它与微控制器无关。在 *User.c* 文件中必须添加硬件的定义。
参见教程。

### 4.3~4.5 略

## 5. 结语

标准化的 CANopen 协议是迈向高质量，可靠和标准化设备的一步。自动化任务现在更容易实现。使用 8 位微控制器的可靠和简单的网络可以使先进的分散控制系统具有智能，简单并因此更可靠的设备。

除网络外，CANopenNode 还为设备提供了必要的功能，这使得 CANopen 变得更加简单和实用。制造设备所需的一切是：设置 CANopen 通信；使用自定义变量（RAM 或 Flash 或 Eeprom）并将它们转换为对象字典；编写代码，用于处理这些变量，微控制器资源和 CANopen 通信对象。它也很灵活，所以自定义代码和自定义通信对象也不是问题。

CANopenNode 很轻量，所以微控制器中足够的内存和时间对于定制任务而言仍然是自由的。

CANopenNode 也可用于主设备，两个示例中显示了这一点，它显示了对网络的高级控制。

## 6. 文献

[CANopenBook]  
Olaf Pfeiffer, Andrew Ayre, Christian Keydel "Embendded Networking with CAN and CANopen", RTC Books, 2003, "http://www.canopenbook.com/"

[CiA]  
“http ://www.can-cia.org/canopen/ ”

[CiADS301]  
“CANopen Application Layer and Communication Profile”, CiA Draft Standard 301, Version 4.02,free access

[CiADR303-3]  
“CANopen Indicator Specification”, Draft Recommendation 303-3, Version 1.0

[CiADS401]  
“CANopen Device Profile for Generic I/O Modules”, Draft Standard 401, Version Version 2.1”

[esacademy]  
http://www.esacademy.com/myacademy/ - Online training.

[Microchip]  
http://www.microchip.com

[Beck-IPC]  
http://www.beck-ipc.com/ipc/index.asp?sp=en

[CANchkEDS]  
“http://www.vector-informatik.com/english/products/canchkeds.php” - EDS checker.

[sourceforge]  
“http://sourceforge.net/” - Many useful open source projects


[L1]:https://syauo.github.io/2018/03/21/CANopenNode-learn-1/
[L2]:https://syauo.github.io/2018/03/22/CANopenNode-learn-2/
[L3]:https://syauo.github.io/2018/03/23/CANopenNode-learn-3/

[R1]:https://sourceforge.net/projects/canopennode/files/canopennode/CANopenNode-1.10/ "《CANopenNode Turorial》  V1.10"  
[R2]:https://sourceforge.net/projects/canopennode/files/canopennode/CANopenNode-1.10/ "《CANopenNode Manual》 V1.10"  
[R3]:mailto:janez.paternoster@siol.net "作者 Janez Paternoster"
[R4]:http://www.winmerge.org/ "文件比较工具"

[P2-1]:https://us1.myximage.com/2018/03/26/90c4e2acd191f60b5c726889c295ea11.png "主线和定时器程序流程图"
[P2-2]:https://us1.myximage.com/2018/03/26/22c5c61437f6f2bc2b633c39b80fe273.png "重置节点与重置通信程序 "
[P2-3]:https://us1.myximage.com/2018/03/26/e58ad775a6dfc15ebf0439f7c42a9f4b.png "接收CAN消息"
[P2-4]:https://us1.myximage.com/2018/03/26/cf4a591766551da0f928634782e25284.png "发送CAN消息"
[P2-5]:https://us1.myximage.com/2018/03/26/1d35203fdfb71ea3ac94e3760ca8723e.png "CANopenNode主线处理流程图"

<!-- 缩略词 -->
*[CAN]: Controller Area Network

*[SDO]: Service Data Objects

*[OD]: Object Dictionary 对象字典

*[PDO]: Process Data Objects

*[COB]: Communication Object 通信对象

*[NMT]: Network Management

*[EDS]: Electronic Data Sheets

*[SYNC]: Synchronization 同步