 目前搭建了一套的开源双足机械加硬件（包含驱动），在硬件上可行走，但是整体动态性能不如宇树H1（但是开源和便宜啊！）可以自己手搓，开源协议为GPL3.0，整体架构如图所示：
 ![run](https://github.com/Alexhuge1/Alexbot/assets/79268846/a2eb591e-d456-4b0f-b197-b859c2b0c155)
1.机械&结构
![屏幕截图 2024-04-13 121716](https://github.com/Alexhuge1/Alexbot/assets/79268846/150308cd-8ca5-4cd7-801d-cbc1b5fc478d)

一条腿五个关节，胯关节3自由度，膝关节1自由度，踝关节1自由度，其中为了降低整体小腿惯量，将膝关节电机上移至胯部。电机使用了达妙DM-J8009作为胯关节大腿电机，额定20NM,峰值40NM。别的使用DM-J8006作为其他关节，DM-J6006作为踝关节。整体机械使用SW2021版本设计，做了拓扑优化和结构有限元仿真，使用Altair Inspire做的。其中大腿使用CNC加工，大概价格在5000元左右。到了价格部分我会细说的。
![2 10](https://github.com/Alexhuge1/Alexbot/assets/79268846/501826ab-541a-40ca-8109-51b4ecc5542e)
AlexBot双足机器人设计仿真图
![屏幕截图 2024-04-13 1716](https://github.com/Alexhuge1/Alexbot/assets/79268846/c7eb36f5-861d-4384-a486-29a93eeace56)
使用Altair Inspire进行二维拓扑优化全流程
![图片1](https://github.com/Alexhuge1/Alexbot/assets/79268846/3afe8f92-c4c3-46a8-9cc9-73fd7369d808)
使用Altair Inspire进行三维拓扑优化全流程
2.硬件&驱动

然后硬件部分主要做了一个STM32H7B0核心板，集成了一个九轴陀螺仪做外环控制，然后上位机使用了一个英伟达的 Jetson Xavier nx作为主电脑（之后考虑TX2是否可以胜任）整体电路所包含的电路板总共如下，包含两个分电板以及一个STM32H7B0VBT6为核心的控制板和上位机 jetson Xavier nx。由电源供电24V给到4个DM6006，4个DM8006和两个DM8009。接入24V转压模块输出5V和19V电压分别给主控核心板和jetson Xavier nx供电，主控核心板主要处理矩阵变换以及和电机之间的CAN通讯问题，一条腿一共有五个电机，分别是两个DM6006,DM8006和一个DM8009,考虑到CAN总线带宽的占用问题，一个STM32H7B0核心板的一个CAN通道最多接入5个电机，也就是一条腿的所有电机。CAN1接入左腿的五个电机，CAN2接入右腿的两个电机。同时核心板皆有带有隔离的多路串口和RS485接口。可以顺利的完成和jetson Xavier nx的通讯。在电源选取上，我们一共所需四个DM6006电机，四个8006电机以及两个DM8009电机，根据他们的额定电压和额定电流，这里我们假设他们的输入电压都为24V，我们可得到总的功率为1968W的功率。所以我们这里选取了输出功率为2KW的DC直流源作为我们的供电电源。

选用STM32H7B0VBT6作为核心芯片，引出两路CAN通道FDCAN1和FDCAN2。电路设计包含输入的5V过压保护设计，通过一个PNP和一个P-MOS来完成电路过压时自动断开电路并直接接入GND。同时内置IMU，可以选择多种方式与之通讯，可以通过模拟IIC来获取九轴数据或者通过串口实现九轴的数据获取。同时板内内置两路485通讯接口，通过MAX13487RS485逻辑电平转换芯片，将TTL电平转换成RS485协议的电平，并通过外部电路的设计实现数据传输的自动换向，同时引出多路串口接口，为了防止高压通过串口直接损坏芯片，在每一路的串口输出中，接入了Π122M31隔离芯片进行隔离。并引出几路定时器接口可以输出PWM波方便后续方案改进或者调试。
![图片3](https://github.com/Alexhuge1/Alexbot/assets/79268846/45d509cd-a820-4300-86af-de6eaf1d7879)
![屏幕截图 2024-04-13 121239](https://github.com/Alexhuge1/Alexbot/assets/79268846/e625a7bd-51c1-4a25-b6b3-4c22a0512e78)
双足机器人实物建造流程图
![微信图片_20240413123450](https://github.com/Alexhuge1/Alexbot/assets/79268846/be32c725-1c60-49a8-b3aa-c7ed3b4e22c1)
双足机器人实物
说明整个电路板&驱动好使https://www.zhihu.com/video/1762611365804695552

3.模仿学习算法&全身MPC&WBC

还没做到

4.价格部分
电机价格，我去和达妙谈价格了，现在买这个说是我这边来的可以给到1万的价格，还送USB转CAN和核心板
加工厂这边还在谈，基本5000可以下来

整体价格在20000左右，目前和达妙电机谈好价格了，说如果我这边来的话这样一套电机10500全部，并且送USB转CAN模块&H723开发板。加工厂的话说可以到6000的价格，所以加上jetson&一些轴承零零散散的标准件总共20000能下来。
然后CNC也谈了下价格也谈下来了，可以加群找一下加工厂，可以做到5000左右，等后期有量的话再去谈价格。
开源文档里附有中期做的文档和PPT，拿去不谢。
详细细节看这篇，我把中期报告中技术细节都放上来了，时间有点赶不过。
https://http://zhuanlan.zhihu.com/p/69235601413 赞同 · 5 评论文章
开发文档&驱动全套设计资料见开源地址

硬件主要包含这些，机械是拿SW2021画的，Keyshot渲染；硬件使用免费大神嘉立创eda pro完成，简单双层板，这里特别谢谢下我们的硬件，驱动也是他移植的。希望各位大佬看到以后多多宣传宣传，发发机器人交流群这些，争取更多的指导意见。

开源地址：先放百度网盘，之后理好去github
链接：https://http://pan.baidu.com/s/1oPcLiiXsUdc53Dmvo2ovkQ?pwd=tkqy
提取码：tkqy 
