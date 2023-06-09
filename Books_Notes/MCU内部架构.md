## MCU内部模块：

* CPU，执行用户程序指令
* 总线：MCU内部数据交换的桥梁
* 非易失性存储器：”硬盘“，永久性保存用户数据、程序，掉电后数据依然可以保存
* 易失性存储器：”内存“，用于保存程序的中间变量、堆栈，掉电后数据丢失
* 外设，执行指定功能
* 地址映射表：”存储映射“，CPU读写操作都会有对应的地址，每个模块都是自己的地址
* 系统控制器：用于控制MCU内部各个模块的状态
* 中断控制器：连接并管理来自各个模块的中断信号与CPU的中断线
* 时钟树：各个模块的时钟分频。每个模块的时钟信号都可以选择
* 复位树：每个模块的复位分配，一个MCU中可能有多种复位状态



### 1. CPU

CPU：中央控制器，MCU的大脑，负责指令的执行控制整个MCU完成特定的功能

指令集.   

CPU CORE：8051、RISC-V、ARM



#### 1.1. ARM Cortex-M3

![image-20221007174704410](https://cdn.staticaly.com/gh/1eexk/Image/master/image-20221007174704410.png)

1.NVIC:中断向量控制器

2.CPU 核心

3.DEBUG接口

4.MPU，用于控制读写访问权限，保护数据

5.总线接口，与外部交换数据



##### 1.1.1. 核心

![image-20221007175030703](https://cdn.staticaly.com/gh/1eexk/Image/master/image-20221007175030703.png)

1.Instruction Fetch Unit 指令获取单元，用户获取指令

2.Decoder 译码器 将指令进行译码，获得指令的具体信息

3.ALU 算数逻辑运算单元

4.Register Bank 寄存器组 通用寄存器和特殊功能寄存器

5.Memory Interface 储存器接口

6.Trace Interface 调试接口



##### 1.1.2 指令执行流水线

* 取址->译码->执行

* 完整的：取址->译码->执行->暂存->写回
* 下一条指令的执行无需等待上一条指令执行完成

![image-20221007180326545](https://cdn.staticaly.com/gh/1eexk/Image/master/image-20221007180326545.png)

##### 1.1.3 内部寄存器组

通用寄存器：数据的暂存 R0-R15

功能性寄存器：程序状态寄存器、中断屏蔽寄存器、控制寄存器



主要关注:

通用寄存器的R13和R15，

R13负责保存堆栈指针，

R15程序计数器PC，看程序运算到哪一步

都是WORD对齐



##### 1.1.4 总线接口

* CPU通过总线接口与外部进行数据的交换
* 指令总线IBUS:只读，用于读取指令
* 数据总线DBUS接口：可读可写，用于读写中间变量、堆栈、存放用户数据
* 系统总线SBUS接口：可读可写，用来读写外设等模块的寄存器
* 取址以WORD长度进行，每个总线都是地址对齐



##### 1.1.4 NVIC

* 中断向量控制器
* 最多支持1~240个外部中断，支持1个外部不可屏蔽中断，支持16个异常中断
* 每个外部中断都有其对应的内部寄存器
  * 使能寄存器SETENA 允许该外部中断
  * 失能寄存器CLRENA 禁止该外部中断
  * 优先级寄存器PRI 配置该中断的优先级
  * 活跃状态寄存器 ACTIVE 只读，读取可以得到该中断是否活跃
  * 悬起状态寄存器SETPEND 读取可以得到该中断是否悬起，也可以手动悬起该中断
  * 解悬寄存器CLRPEND 清除该中断的活跃、悬起状态

活跃是指正在被相应处理中断处理函数的中断，不能立即处理的中断称为悬起，如 因为CPU正在执行更改优先级的中断而未执行的中断称为悬起

* 中断优先级有两级：主优先级（抢占优先级），次优先级（响应优先级）。主优先高的中断可以打断低的中断，但是当主优先级相同次优先级高的中断不可以打断低的优先级。中断优先级的数字越小，优先级越高。

​		响应属性则应用在抢占属性相同的情况下，当两个中断向量的抢占优先级相同时，如果两个中断同时到达， 则先处理响应优先级高的中断， 响应属性由NVIC_IRQChannelSubPriority 参数配置。



* 系统异常：系统运行进入异常的状态

复位：程序复位

NMI:外部不可屏蔽中断

HardFault：硬件错误

MemManageFault:错误的访问了MPU保护的区域

UsageFault：指令错误

SysTick：滴答计时器溢出



中断向量表1~15为异常，后面的为外部中断

![image-20221010114251746](https://cdn.staticaly.com/gh/1eexk/Image/master/image-20221010114251746.png)
