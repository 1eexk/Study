### 2.2.1 计算机的基石：图灵机

![image-20230328154933104](https://cdn.jsdelivr.ren/gh/1eexk/Image@master/image-20230328154933104.png)



## 2.4 CPU性能提升：Cache机制

RAM可分为静态存储器（`Static Random Access Memory`,SRAM）和动态存储器（`Dynamic Random Access Memory`）。SRAM中的存储单元相当于一个锁存器，只有0，1两个稳态；DRAM则是利用电容存储电荷来保存0和1两种状态，因此需要定时对其进行刷新，否则随着时间的推移，电容其中存储的电荷将逐渐消失。

SRAM：读写速度快，生产成本高，多用于容量较小的高速缓冲存储器。

DRAM：读写速度较慢，集成度高，生产成本低，多用于容量较大的主存储器。|

|            | SRAM   | DRAM     |
| ---------- | ------ | -------- |
| 存储信息   | 触发器 | 电容     |
| 破坏性读出 | 非     | 是       |
| 需要刷新   | 不要   | 需要     |
| 送行列地址 | 同时送 | 分两次送 |
| 运行速度   | 快     | 慢       |
| 集成度     | 低     | 高       |
| 发热量     | 大     | 小       |
| 存储成本   | 高     | 低       |

动态存储器的定期刷新：在不进行读写操作时，DRAM 存储器的各单元处于断电状态，由于漏电的存在，保存在电容CS 上的电荷会慢慢地漏掉，为此必须定时予以补充，称为刷新操作.

`SRAM`一般用来做高速缓存`cach`，`DRAM`一般用来当内存。



**********拓展：

**cache既可以在core内部集成，又可以在core外面**，下面如图就是集成的L1,L2 Cache和core外共享的L3 Cache。

![image-20230407155433760](https://cdn.jsdelivr.ren/gh/1eexk/Image@master/image-20230407155433760.png)



### SDRAM

​	`SDRAM`(Synchronous Dynamic Random Access Memory)同步动态随机存取存储器，同步是指Memory工作需要步时钟，内部的命令的发送与数据的传输都以它为基准；动态是指存储阵列需要不断的刷新来保证数据不丢失；随机是指数据不是线性依次存储，而是由指定地址进行数据读写。目前的168线64bit带宽内存基本上都采用SDRAM芯片，工作电压3.3V电压，存取速度高达7.5ns，而EDO内存最快为15ns。并将RAM与CPU以相同时钟频率控制，使RAM与CPU外频同步，取消等待时间,所以其传输速率比EDO DRAM更快。

​	SDRAM从发展到现在已经经历了四代，分别是：第一代SDR SDRAM，第二代DDR SDRAM，第三代DDR2 SDRAM，第四代DDR3 SDRAM.

​	第一代与第二代SDRAM均采用单端（Single-Ended）时钟信号,第三代与第四代由于工作频率比较快，所以采用可降低干扰的差分时钟信号作为同步时钟。
​	SDR SDRAM的时钟频率就是数据存储的频率，第一代内存用时钟频率命名，如pc100，pc133则表明时钟信号为100或133MHz，数据读写速率也为100或133MHz。之后的第二，三，四代DDR（Double Data Rate）内存则采用数据读写速率作为命名标准，并且在前面加上表示其DDR代数的符号，PC-即DDR，PC2=DDR2，PC3=DDR3。如PC2700是DDR333，其工作频率是333/2=166MHz，2700表示带宽为2.7G。
​	DDR的读写频率从DDR200到DDR400，DDR2从DDR2-400到DDR2-800，DDR3从DDR3-800到DDR3-1666。
​	很多人将SDRAM错误的理解为第一代也就是 SDR SDRAM，并且作为名词解释，皆属误导,SDR不等于SDRAM。



## 2.5 CPU性能提升：流水线

　　流水线是工业社会化大生产背景下的产物。亚当·斯密在他的《国富论》中曾经描述这样一个场景：制作一枚回形针一般需要18个步骤，工厂里的工人平均每天也只能做100枚回形针。后来改进工艺，把制针流程分成18道工艺，然后让这10名工人平均每人负责1～2道工艺，最后这10名工人每天可以制造出48 000枚回形针，生产效率整整提高了几十倍！

　看到这里可能有人抬杠了：你这么算是不对的，每道工序所用的时间都变为原来的一半，怎么可能做得到？其实要做到不难的，只要工序拆解得合理，容易上手，再加上足够时间的机械重复，很多人都可以做得到。

### 2.5.1 流水线工作原理

​	一条指令的执行一般要经过取指令、翻译指令、执行指令3个基本流程。CPU内部的电路分为不同的单元：取指单元、译码单元、执行单元等，指令的执行也是按照流水线工序一步一步执行的。如图2-34所示，我们假设每一个步骤的执行时间都是一个时钟周期，那么一条指令执行完需要3个时钟周期。

​	CPU执行指令的3个时钟周期里，**取指单元只在第一个时钟周期里工作**，其余两个时钟周期都处于空闲状态，其他两个执行单元也是如此。这样做效率太低了，消费者无法接受，老板更无法接受。解决方法就是引入流水线，让流水线上的每一颗螺丝钉都马不停蹄地运转起来。

![image-20230328170534362](https://cdn.jsdelivr.ren/gh/1eexk/Image@master/image-20230328170534362.png)

　如图2-35所示，引入流水线后，除了刚开始的第一个时钟周期大家可以偷懒，其余的时间都不能闲着：从第二个时钟周期开始，当译码单元在翻译指令1时，取指单元也不能闲着，要接着去取指令2。从第三个时钟周期开始，当执行单元执行指令1时，译码单元也不能闲着，要接着去翻译指令2，而取指单元要去取指令3。从第四个时钟周期开始，每个电路单元都会进入满负荷工作状态，像富士康工厂里的流水线一样，源源不断地执行一条条指令。

![image-20230328170850415](https://cdn.jsdelivr.ren/gh/1eexk/Image@master/image-20230328170850415.png)

　　引入流水线后，虽然每一条指令的执行流程和时间不变，还是需要3个时钟周期，但是从整条流水线的输出来看，差不多平均每个时钟周期就能执行一条指令。原来执行一条指令需要3个时钟周期，引入流水线后平均只需要1个时钟周期，CPU性能提升了不少。

　　流水线的本质其实就是拿空间换时间。将每条指令分解为多步执行，指令的每一小步都有独立的电路单元来执行，并让不同指令的各小步操作重叠，通过多条指令的并行执行，加快程序的整体运行效率。

### 2.5.2 超流水线技术
​	超流水线技术就是对流水线技术的优化。流水线存在木桶短板效应，我们只需要找出CPU流水线中的**性能瓶颈**，即**耗时最长的那道工序**，对其**再进行细分，拆解为更多的工序**就可以了。每一道工序都称为流水线中的一级，流水线越深，每一道工序的执行时间就会变得越小，处理器的时钟周期就可以更短，CPU的工作频率就可以更高，进而可以提升CPU的性能，提高工作效率。

​	在手机生产流水线上，耗时最长的那道工序决定了整条流水线的吞吐率。CPU内部的流水线也是如此，流水线中耗时最长的那道工序单元的执行时间（即时间延迟）决定了CPU流水线的性能。**CPU流水线中的每一级电路单元一般都是由组合逻辑电路和寄存器组成的，组合逻辑电路用来执行本道工序的逻辑运算，寄存器用来保存运算输出结果，并作为下一道工序的输入。**（接下来针对每一级电路单元对“工序”再细化，增加几级流水线，就是超级流水线）。

​	　我们把5级以上的流水线称为超流水线结构。为了提升CPU主频，高性能的处理器一般都会采用这种超流水线结构。Intel的i7处理器有16级流水线，AMD的速龙64系列CPU有20级流水线，史上具有最长流水线的处理器是Intel的第三代奔腾四处理器，有31级流水线。

​	要想提升CPU的主频，本质在于减少流水线中每一级流水的执行时间，消除木桶短板效应。解决方法有三个：一是优化流水线中各级流水线的性能，受限于当前集成电路的设计水平，这一步最难；二是依靠半导体制造工艺，工艺制程越先进，芯片面积就会越小，发热也就越小，就更容易提升主频；三是不断地增加流水线深度，流水线越深，流水线中的各级时间延迟就可以做得越小，就更容易提高主频。

​	当然，流水线和超级流水线之间并没有很明显的区别。这样的技术，虽然提高了CPU的主频，但是也带来了很大的副作用：流水线是靠指令的并行来提升性能的，第一条指令还没有执行完，下面的第二条指令就开始取指、译码了。执行的程序指令如果是顺序结构的，没有中断或跳转，流水线确实可以提高执行效率。**但是当程序指令中存在跳转、分支结构时，下面预取的指令可能就要全部丢掉了，需要到跳转的地方重新取指令执行。**

​	流水线越深，一旦预取指令失败，浪费和损失就会越严重，因为流水线中预取的几十条指令可能都要丢弃掉，此时流水线就发生了停顿，无法按照预期继续执行，这种情况我们一般称为流水线冒险（hazard）。




## 2.8 总线与地址

CPU与内存、各种外部设备等IP之间都是通过总线相连的。**CPU内部的寄存器**是**没有地址**的，可直接通过寄存器名访问。而**内存和外部设备控制器中的寄存器**都需要**有一个地址**，然后CPU才能通过地址去读写这些外部设备控制器的寄存器，控制外部设备的运行，或者根据地址去读写指定的内存单元。

### 2.8.1 地址的本质

地址的本质其实就是由CPU管脚发出的一组地址控制信号。因为这些信号是由CPU管脚直接发出的，因此也被称为物理地址。地址信号线的位数决定了寻址空间的大小，如上面的两根A1A0地址信号线，有4字节的寻址空间；CSA1A0三根地址信号线有8字节的寻址空间。在一个32位的计算机系统中，32位的地址线有4GB大小的寻址空间。

### 2.8.2 总线的概念

总线其实就是各种数字信号的集合，包括地址信号、数据信号、控制信号等。一个计算机系统中可能会有各种不同的总线，不同的总线读写时序、工作频率不一样，不同的总线之间通过桥（bridge）来连接。桥一般是一个芯片组电路，用来将总线的电子信号翻译成另一种总线的电子信号。

### 2.8.3 总线编址方式

计算机一般采用两种编址方式：**统一编址**和**独立编址**。统一编址，顾名思义，就是内存RAM和外部设备共享CPU的寻址空间，如图2-64所示，ARM、MIPS架构的CPU都采用这种编址方式。

在**统一编址**模式下，内存RAM、外部设备控制器的寄存器、集成在外部设备控制器内部的RAM共享CPU的可寻址空间。在统一编址模式下，**CPU可以像操作内存一样去读写外部设备的寄存器和内部RAM**。

和统一编址相对应的是**独立编址**。在独立编址模式下，内存RAM和外部设备的寄存器独立编址，分别**占用不同的地址空间**。如X86架构的CPU，外部设备的寄存器有独立的64KB空间，需要**专门的IN/OUT指令**才能访问，这片独立编址的64KB大小的空间也被称为**I/O地址空间**。

## 2.9 指令集与微架构

不同架构的处理器支持的指令类型是不同的。ARM架构的处理器只支持ARM指令，X86架构的处理器只支持X86指令。如果你在ARM架构的处理器上运行X86指令，就无法运行，报未定义指令的错误，因为ARM架构的处理器只支持ARM指令集中定义的指令。CPU支持的有限个指令的集合，我们称之为指令集。

### 2.9.1 什么是指令集

指令集架构（Instruction Set Architecture，ISA）是计算机体系架构的一部分。指令集是一个很虚的东西，是一个标准规范。芯片工程师在设计CPU时，也要以指令集中规定的指令格式为标准，实现不同的译码电路来支持指令集各种指令的运行。指令集最终的实现就是**微架构**，就是CPU内部的各种译码和执行电路。

指令集作为CPU和编译器的设计规范和参考标准，主要用来定义指令的格式、操作数的类型、寄存器的分配、地址的格式等，指令集主要由以下内容组成。

● 指令的分发、预取、解码、执行、写回。

● 操作数的类型、存储、存取、旁路转移。

● Load/Store架构。

● 寄存器。

● 地址的格式、大端模式、小端模式。

● 字节对齐、边界对齐等。

指令集也不是一成不变的，也会随着应用需求的推动不断迭代更新，不断扩充新的指令。

### 2.9.2 什么是微架构

微架构，对应的英文是Microarchitecture，也就是处理器架构。一套相同的指令集，可以由不同形式的电路实现，可以有不同的微架构。在设计一个微架构时，一般需要考虑很多问题：处理器是否支持分支预测，单发射还是多发射，顺序执行还是乱序执行？流水线需要多少级？主频需要多高？Cache需要多大？需要几级Cache？根据不同的配置选项，我们可以基于一套指令集设计出不同的微架构。

在嵌入式处理器中，微架构不等于SoC，大家不要把概念混淆了，微架构一般也称为CPU内核。在一个ARM SoC芯片上，我们把CPU内核和各种外设IP通过AMBA总线连接起来，构成一个片上系统，即System On Chip，简称SoC。



# 第3章 ARM体系结构与汇编语言

## 3.1 ARM体系结构

计算机的指令集一般可分为4种：复杂指令集（CISC）、精简指令集（RISC）、显式并行指令集（EPIC）和超长指令字指令集（VLIW）。RISC指令集相对于CISC指令集，主要有以下特点。

● Load/Store架构，CPU不能直接处理内存中的数据，要先将内存中的数据Load（加载）到寄存器中才能操作，然后将处理结果Store（存储）到内存中。

● 固定的指令长度、单周期指令。

● 倾向于使用更多的寄存器来存储数据，而不是使用内存中的堆栈，效率更高。

ARM指令集虽然属于RISC，但是和原汁原味的RISC相比，还是有一些差异的，具体如下。

● ARM有桶型移位寄存器，单周期内可以完成数据的各种移位操作。

● 并不是所有的ARM指令都是单周期的。

● ARM有16位的Thumb指令集，是32位ARM指令集的压缩形式，提高了代码密度。

● 条件执行：通过指令组合，减少了分支指令数目，提高了代码密度。

● 增加了DSP、SIMD/NEON等指令。

ARM处理器有多种工作模式，如表3-1所示。应用程序**正常运行**时，ARM处理器工作在**用户模式（User mode）**，当程序运行**出错或有中断发生**时，ARM处理器就会切换到对应的**特权工作模式**。用户模式属于普通模式，有些特权指令是运行不了的，需要切换到特权模式下才能运行。在ARM处理器中，除了用户模式是普通模式，剩下的几种工作模式都属于特权模式。

![image-20230407165221186](https://cdn.jsdelivr.ren/gh/1eexk/Image@master/image-20230407165221186.png)

在ARM处理器内部，除了基本的算术运算单元、逻辑运算单元、浮点运算单元和控制单元，还有一系列寄存器，包括各种通用寄存器、状态寄存器、控制寄存器，用来控制处理器的运行，保存程序运行时的各种状态和临时结果，如图3-1所示。

![image-20230407165710385](https://cdn.jsdelivr.ren/gh/1eexk/Image@master/image-20230407165710385.png)

R13寄存器又称为堆栈指针寄存器（Stack Pointer，SP），用来维护和管理函数调用过程中的栈帧变化，R13总是指向当前正在运行的函数的栈帧，一般不能再用作其他用途。R14寄存器又称为链接寄存器（Link Register，LR），在函数调用过程中主要用来保存上一级函数调用者的返回地址。寄存器R15又称为程序计数器（Program Counter，PC），CPU从内存取指令执行，就是默认从PC保存的地址中取的，每取一次指令，PC寄存器的地址值自动增加。CPU一条一条不停地取指令，程序也就源源不断地一直运行下去。**在ARM三级流水线中，PC指针的值等于当前正在运行的指令地址+8**，后续的32位处理器虽然流水线的级数不断增加，但为了简化编程，PC指针的值继续延续了这种计算方式。

当前处理器状态寄存器（Current Processor State Register，CPSR）主要用来表征当前处理器的运行状态。除了各种状态位、标志位，CPSR寄存器里也有一些控制位，用来切换处理器的工作模式和中断使能控制。

在每种工作模式下，都有一个单独的程序状态保存寄存器（Saved Processor State Register，SPSR）。当ARM处理器切换工作模式或发生异常时，SPSR用来保存当前工作模式下的处理器现场，即将CPSR寄存器的值保存到当前工作模式下的SPSR寄存器。当ARM处理器从异常返回时，就可以从SPSR寄存器中恢复原先的处理器状态，切换到原来的工作模式继续运行。

## 3.2 ARM汇编指令

一个完整的ARM指令通常由操作码+操作数组成，.

```sh
<opcode> {<cond>{s}<Rd>,<Rn>{,<operand2>}}
```

这是一个完整的ARM指令需要遵循的格式规则，指令格式的具体说明如下。

● 使用<>标起来的是必选项，使用{}标起来的是可选项。

● `<opcode>`是二进制机器指令的操作码助记符，如MOV、ADD这些汇编指令都是操作码的指令助记符。

●` cond`：执行条件，ARM为减少分支跳转指令个数，允许类似BEQ、BNE等形式的组合指令。

● `S`：是否影响CPSR寄存器中的标志位，如SUBS指令会影响CPSR寄存器中的N、Z、C、V标志位，而SUB指令不会。

●` Rd`：目标寄存器。

● `Rn`：第一个操作数的寄存器。

● `operand2`：第二个可选操作数，灵活使用第二个操作数可以提高代码效率。

### 3.2.1 存储访问指令

ARM处理器属于冯·诺依曼架构，程序和数据都存储在同一存储器上，内存空间和I/O空间统一编址，ARM处理器对程序指令、数据、I/O空间中外设寄存器的访问都要通过Load/Store指令来完成。ARM处理器中经常使用的Load/Store指令的使用方法如下。

![image-20230407183006998](https://cdn.jsdelivr.ren/gh/1eexk/Image@master/image-20230407183006998.png)

在ARM存储访问指令中，我们经常使用的是LDR/STR、LDM/STM这两对指令。LDR/STR指令是ARM汇编程序中使用频率最高的一对指令，每一次数据的处理基本上都离不开它们。LDM/STM指令常用来加载或存储一组寄存器到一片连续的内存，通过和堆栈格式符组合使用，LDM/STM指令还可以用来模拟堆栈操作。LDM/STM指令常和表3-2的堆栈格式组合使用。

![image-20230407183207028](https://cdn.jsdelivr.ren/gh/1eexk/Image@master/image-20230407183207028.png)

如图3-3所示，在一个堆栈内存结构中，如果堆栈指针SP总是指向栈顶元素，那么这个栈就是满栈；如果堆栈指针SP指向的是栈顶元素的下一个空闲的存储单元，那么这个栈就是空栈。



### 3.2.2 数据传送指令

LDR/STR指令用来在寄存器和内存之间输送数据。如果我们想要在寄存器之间传送数据，则可以使用MOV指令。

## 3.3 ARM寻址方式

### 3.3.6 多寄存器寻址

STM/LDM指令就属于多寄存器寻址，一次可以传输多个寄存器的值。

![image-20230407190241045](https://cdn.jsdelivr.ren/gh/1eexk/Image@master/image-20230407190241045.png)

在多寄存器寻址中，用大括号{}括起来的是寄存器列表，寄存器之间用逗号隔开，如果是连续的寄存器，还可以使用连接符-连接，如R0-R3，就表示R0、R1、R2、R3这4个寄存器。LDM/STM指令一般和IA、IB、DA、DB组合使用，分别表示Increase After、Increase Before、Decrease After、Decrease Before。

栈是程序运行过程中非常重要的一段内存空间，栈是C语言运行的基础，函数内的局部变量、函数调用过程中要传递的参数、函数的返回值一般都是保存在栈中的。在运行C语言程序之前，必须要先运行一段汇编代码初始化内存和栈指针SP，然后才能跳到C语言程序中运行。

ARM没有专门的入栈和出栈指令，ARM中的栈操作其实就是通过上面所讲的STM/LDM指令和栈指针SP配合操作完成的。栈一般可以分为以下4类。

● 递增栈A：入栈时，SP栈指针从低地址往高地址方向增长。

● 递减栈D：入栈时，SP栈指针从高地址往低地址方向增长。

● 满栈F：SP栈指针总是指向栈顶元素。

● 空栈E：SP栈指针总是指向栈顶元素的下一个空闲存储单元。

### 3.3.7 相对寻址

相对寻址其实也属于基址寻址，只不过它是基址寻址的一种特殊情况。它是**以PC指针作为基地址**进行寻址的，以指令中的地址差作为偏移，两者相加后得到的就是一个新地址，然后可以对这个地址进行读写操作。ARM中的B、BL、ADR指令其实都是采用相对寻址的。

![image-20230407190742843](https://cdn.jsdelivr.ren/gh/1eexk/Image@master/image-20230407190742843.png)

## 3.4 ARM伪指令

ARM伪指令并不是ARM指令集中定义的标准指令，而是为了编程方便，各家编译器厂商自定义的一些辅助指令。**伪指令有点类似C语言中的预处理命令，在程序编译时，这些伪指令会被翻译为一条或多条ARM标准指令。**常见的ARM伪指令主要有4个：**ADR、ADRL、LDR、NOP**，它们的使用示例如下。

![image-20230407190836263](https://cdn.jsdelivr.ren/gh/1eexk/Image@master/image-20230407190836263.png)

NOP伪指令比较简单，其实就相当于MOV R0，R0。在以后的学习和工作中，大家在ARM汇编程序中经常看到的就是LDR伪指令。

### 3.4.1 LDR伪指令

ARM属于RISC架构，不能对内存中的数据直接操作，ARM通常会使用LDR/STR这对加载/存储指令，先将内存中的数据加载到寄存器，然后才能对寄存器中的数据进行操作，最后把寄存器中的处理结果存储到内存中。LDR伪指令的主要用途是**将一个32位的内存地址保存到寄存器中**。

在寄存器之间传递数据可以使用MOV指令，但是当传递的一个内存地址是32位的立即数时，MOV指令就应付不了了，如下面的第2条指令。

![image-20230411112925185](https://cdn.jsdelivr.ren/gh/1eexk/Image@master/image-20230411112925185.png)

RISC指令的特点是单周期指令，指令的长度一般都是固定的。在一个32位的系统中，一条指令通常是32位的，指令中包括操作码和操作数，如图3-5所示。

![image-20230411113033531](https://cdn.jsdelivr.ren/gh/1eexk/Image@master/image-20230411113033531.png)

指令中的操作码和操作数共享32位的存储空间：**一般前面的操作码要占据几个比特位，剩下来的留给操作数的编码空间就小于32位**了。当编译器遇到MOV R0，＃0x30008000这条指令时，因为后面的操作数是32位，编译器就无法对这条指令进行编码了。为了解决这个难题，编译器提供了一个LDR伪指令来完成上面的功能。

![image-20230411113318427](https://cdn.jsdelivr.ren/gh/1eexk/Image@master/image-20230411113318427.png)

在上面的示例代码中，LDR不是普通的ARM加载指令，而是一个伪指令。为了与ARM指令集中的加载指令LDR区别开来，LDR伪指令中的操作数前一般会有一个等于号=，用来表示该指令是个伪指令。通过LDR伪指令，编译器就解决了向一个寄存器传送32位的立即数时指令无法编码的难题。

在程序编译期间，这些伪指令会被标准的ARM指令替代。编译器在处理伪指令时，根据伪指令中的操作数大小，会使用不同的ARM标准指令替代。如当LDR伪指令中的**操作数小于8位**时，LDR伪指令一般会**被MOV指令替代**。

![image-20230411113508336](https://cdn.jsdelivr.ren/gh/1eexk/Image@master/image-20230411113508336.png)

当LDR伪指令中的**操作数大于8位**时，LDR伪指令会被编译器转换为**LDR标准指令+文字池**的形式。

![image-20230411114311946](https://cdn.jsdelivr.ren/gh/1eexk/Image@master/image-20230411114311946.png)

在上面的示例代码中，当LDR伪指令中的操作数为一个32位的立即数时，编译器会首先在内存中分配一个4字节大小的存储单元，然后将这个32位的地址0x30008000存放到该存储单元中，该存储单元通常也叫作文字池（literal pool）。接着编译器计算出该存储单元到LDR伪指令之间的偏移OFFSET，然后使用寄存器相对寻址，就可以将这个32位的立即数送到R0寄存器中。偏移量OFFSET的大小一般要小于4KB，所以在分析汇编代码时你会看到，存放这些32位地址常量的文字池一般紧挨着当前指令的代码段，直接放置在当前代码段的后面。

### 3.4.2 ADR伪指令



## 3.5 ARM汇编程序设计

### 3.5.1 ARM汇编程序格式

在一个汇编文件中，可以有不同的section，分为代码段、数据段等，各个段之间相互独立。**AREA伪操作来标识一个段的起始、段名和段的读写属性。**

![image-20230412194716736](https://cdn.jsdelivr.ren/gh/1eexk/Image@master/image-20230412194716736.png)

上图的汇编是由两个数据段构成的，一个数据段，一个代码段。由AREA伪操作来标识一个段的起始、段名、段的属性（CODE、DATA）和读写权限（READONLY、READWRITE）。

ARM汇编程序通过`ENTRY`这个伪操作来标识汇编程序的**运行入口**，使用伪操作`END`来标识汇编程序的**结束**。

在汇编语言中，**标号代表指令地址**，如上述代码中的LOOP标号，和BNE指令结合使用可以构建一个循环程序结构。

在汇编程序中，我们同样也可以添加注释，我们使用分号`；`来注释代码。在一个**空行的行首**或者一条**指令语句的末尾**添加一个分号，然后就可以在**分号后面添加注释**，以增加程序的可读性。

### 3.5.2 符号与标号
