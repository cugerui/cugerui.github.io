---
title: Buck
date: 2019-09-27 15:46:31
categories: 
tags: 
- DCDC
- Buck
keywords:
toc: true
reward: true
---
1.工作原理
2.实例应用
3.参数计算
4.芯片举例
5.常见问题
6.PCB布局
<!-- more -->

## 工作原理

### 基本工作原理

![Buck](https://cugerui-img.oss-cn-beijing.aliyuncs.com/2021/Buck.png)
1.
开关闭合后（Ton），二极管D关断，电流流经L（电感电流iL不能突变，线性增加）。
L是储能滤波电感，在Ton期间（开关闭合），限制大电流通过，防止输入电压Ui直接加到负载R上，对R进行电压冲击，同时把电感电流iL转化成磁能进行能量存储。
励磁**(Ui-Uo)$\cdot$Ton**
与R并联的C是储能滤波电容，如此R两端的电压在Ton期间是稳定的直流电压。
2.
开关断开后（Toff），L将产生反向电动势，流过电流iL由反向电动势eL的正极流出，通过负载R，再经过续流二极管D，最后回到反向电动势el的负极。
削磁**Uo$\cdot$Toff**
由于C的储能稳压，Toff阶段的输出电压也是稳定的直流电压。
3.
有**(Ui-Uo)$\cdot$Ton = Uo$\cdot$Toff**，所以**Uo=Ui$\cdot$Ton/(Ton+Toff)=D$\cdot$Ui**
4.
周期**T=Ton+Toff**，开关频率**fsw=1/T**，**D**为占空比
![LM2596](https://cugerui-img.oss-cn-beijing.aliyuncs.com/2021/LM2596.png)

操作模式：
CCM模式：电压增益M是占空比D
DCM模式：电压增益M和占空比D呈非线性关系，但从总体上来看，随D的增大M值会增大

### 基本组件

#### 电子开关

![switch](https://cugerui-img.oss-cn-beijing.aliyuncs.com/2021/switch.png)
1.NMOS、IGBT、BJT
2.内置（小电流<=5A）
外置，外置NMOS可以并联，增大电流

#### 二极管

选用肖特基二极管或者快恢复二极管。
原因：
1)肖特基二极管导通压降低，相同电流下，功耗低，发热量小
2)肖特基二极管高频特性更好
![diode](https://cugerui-img.oss-cn-beijing.aliyuncs.com/2021/diode.png)
常用的肖特基二极管：
![schottky](https://cugerui-img.oss-cn-beijing.aliyuncs.com/2021/schottky.png)

#### 电感
1.参数
直流电阻(DCR)：由绕线圈数和线的直径决定。
通流能力(Irms)：电感由于有电阻损耗，所以会发热，在热量不损坏电感的情况下，最大允许持续流过的电流。
饱和电流(Isat)：带磁芯的电感才有。
自谐振频率(SRF)：超过自谐振频率，电感呈容性。
2.选用
工字电感：成本低，小电流
屏蔽电感：
磁环电感（黄白环、铁硅铝电感）：大电流
线艺电感：
耦合电感：四个脚

#### 电容

Cin：电解电容并联0.1uF电容
Cout：选用ESR较小的
MLCC X7R
钽电容
固态电容
Cout也可以选用电解电容，后面跟LC低通滤波，平滑输出
![cout](https://cugerui-img.oss-cn-beijing.aliyuncs.com/2021/cout.png)

### 同步整流Buck
![SyncBuck](https://cugerui-img.oss-cn-beijing.aliyuncs.com/2021/SyncBuck.png)
1.功耗：削磁时，电流为iL，Q2上的功耗为**P=iL$\cdot$iL$\cdot$Rds(on)**
所以选用Rds(on)更小的NMOS功耗更低
2.加一个续流二极管效率更高：在死区时间（两个NMOS均不导通），通过这个续流二极管和NMOS的体二极管续流，
3.PWM带死区：防止两个NMOS同时导通

## 实例应用

降压、负电压、非隔离式FlyBuck、隔离式FlyBuck
### 降压

内置非同步整流
![内置非同步整流](https://cugerui-img.oss-cn-beijing.aliyuncs.com/2021/%E5%86%85%E7%BD%AE%E9%9D%9E%E5%90%8C%E6%AD%A5%E6%95%B4%E6%B5%81.png)
内置同步整流
![内置同步整流](https://cugerui-img.oss-cn-beijing.aliyuncs.com/2021/%E5%86%85%E7%BD%AE%E5%90%8C%E6%AD%A5%E6%95%B4%E6%B5%81.png)
外置同步整流
![外置同步整流](https://cugerui-img.oss-cn-beijing.aliyuncs.com/2021/%E5%A4%96%E7%BD%AE%E5%90%8C%E6%AD%A5%E6%95%B4%E6%B5%81.png)

### 负电压
将原来的输出端接地，原来的地全部接为输出。

![负电压](https://cugerui-img.oss-cn-beijing.aliyuncs.com/2021/%E8%B4%9F%E7%94%B5%E5%8E%8B.png)

![负电压仿真](https://cugerui-img.oss-cn-beijing.aliyuncs.com/2021/%E8%B4%9F%E7%94%B5%E5%8E%8B%E4%BB%BF%E7%9C%9F.png)

### FlyBuck

1.通过给电感加上耦合线圈和Flyback式的副边电路来产生隔离的电源输出
2.可以使用多绕组的变压器来产生多路的隔离输出
3.必须使用同步降压芯片，不需要光耦比Flyback简单
![FlyBuck](https://cugerui-img.oss-cn-beijing.aliyuncs.com/2021/FlyBuck.png)
实例：
![](https://cugerui-img.oss-cn-beijing.aliyuncs.com/2021/FlyBuck1.png)

## 参数计算

### 输入电容

![输入电容](https://cugerui-img.oss-cn-beijing.aliyuncs.com/2021/%E8%BE%93%E5%85%A5%E7%94%B5%E5%AE%B9.png)
1)最大RMS电流：IRMS
2)输入电容值：Cin
3)耐压值：Vout
4)取ESR较小的，电容值较大的

### 输出电压
![输出电压](https://cugerui-img.oss-cn-beijing.aliyuncs.com/2021/%E8%BE%93%E5%87%BA%E7%94%B5%E5%8E%8B.png)
### 电感
![电感](https://cugerui-img.oss-cn-beijing.aliyuncs.com/2021/%E7%94%B5%E6%84%9F.png)
1)电感最小值：Lmin
2)电感饱和电流Isat
3)选用低DCR的电感，效率更高

### 续流二极管
![续流二极管](https://cugerui-img.oss-cn-beijing.aliyuncs.com/2021/%E7%BB%AD%E6%B5%81%E4%BA%8C%E6%9E%81%E7%AE%A1.png)
1)最大电流：IDAVG
2)反向耐压值

### 输出电容
![输出电容](https://cugerui-img.oss-cn-beijing.aliyuncs.com/2021/%E8%BE%93%E5%87%BA%E7%94%B5%E5%AE%B9.png)
1)输出电容值：Cout
2)选用低ESR的电容：由输出电压纹波确定ESR
3)耐压值Vcout

## 其它知识

### 自举电路
1.基本原理
自举电容：作为电压源，构成高侧开关驱动器的浮动电源
2.建议在SW和BOOT之间放置一个20Ω的电阻，来降低SW的尖峰电压
3.外部自举二极管

![外部自举二极管1](https://cugerui-img.oss-cn-beijing.aliyuncs.com/2021/%E5%A4%96%E9%83%A8%E8%87%AA%E4%B8%BE%E4%BA%8C%E6%9E%81%E7%AE%A11.png)

![外部自举二极管2](https://cugerui-img.oss-cn-beijing.aliyuncs.com/2021/%E5%A4%96%E9%83%A8%E8%87%AA%E4%B8%BE%E4%BA%8C%E6%9E%81%E7%AE%A12.png)

### 软启动

1.上电时，Vin没有超过UVLO（欠压），电流源Iss接地，即SS脚接地，即Vfb=0V，这时Vout=0V
2.当Vin超过UVLO后，电流源Iss开始给Css充电，电压线性增加
在Vss<Vfb之前，限制FB跟随Css的充电斜率进行上电
![软启动](https://cugerui-img.oss-cn-beijing.aliyuncs.com/2021/%E8%BD%AF%E5%90%AF%E5%8A%A8.png)

## 常用开关电源芯片

### 参数
输入电压范围
输出电压范围
最大输出电流
开关频率
参考电压Vref，反馈电压
EN使能的门限电压
内部VCC、VDD稳压值
软启动时间
运行结温
### 实例
MPS公司：mp2307、mp4560、mp1593、mp1584
TI：TPS5450、TPS5430、TPS5420
芯龙XLSEMI
## 常见问题

### 芯片的开关
为防止开关SW引起的电压抖动和尖峰电压，使用低通滤波，时间常数约为4ms。
![EN1](https://cugerui-img.oss-cn-beijing.aliyuncs.com/2021/EN1.png)
使用NMOS，逻辑高，NMOS导通，拉低EN端
![EN2](https://cugerui-img.oss-cn-beijing.aliyuncs.com/2021/EN2.png)
使用三极管，光电耦合器进行隔离
![EN3](https://cugerui-img.oss-cn-beijing.aliyuncs.com/2021/EN3.png)

## PCB布局

### EMI
Buck转换器中的EMI源头：磁场=》由小型电流环中的高频电流产生
辐射超标：
1)极小的环路变成了大的环路
2)线路上的导线形成了多余的辐射（天线效应）
1.转换器中的电流回路
找到芯片的Vin和GND脚，Cin越靠近Vin和GND越好，减小高频电流环路的面积。
![电流回路](https://cugerui-img.oss-cn-beijing.aliyuncs.com/2021/%E7%94%B5%E6%B5%81%E5%9B%9E%E8%B7%AF.png)
2.输入和输出的滤波处理
输入：ΠLC滤波 （高频低阻电解电容或者固态电容、电感）+ MLCC X7R 2X10uF + 0.1uF 
输出：MLCC X7R 0.1uF + MLCC X7R 2X22uF + ΠLC滤波 （固态电容、磁珠）
![输入和输出滤波](https://cugerui-img.oss-cn-beijing.aliyuncs.com/2021/%E8%BE%93%E5%85%A5%E5%92%8C%E8%BE%93%E5%87%BA%E6%BB%A4%E6%B3%A2.png)
3.降低开关频率以降低辐射水平
4.在自举电容上串联一个电阻
5-10Ω
![自举电容串电阻](https://cugerui-img.oss-cn-beijing.aliyuncs.com/2021/%E8%87%AA%E4%B8%BE%E7%94%B5%E5%AE%B9%E4%B8%B2%E7%94%B5%E9%98%BB.png)
5.RC缓冲抑制电路
![RC缓冲抑制电路](https://cugerui-img.oss-cn-beijing.aliyuncs.com/2021/RC%E7%BC%93%E5%86%B2%E6%8A%91%E5%88%B6%E7%94%B5%E8%B7%AF.png)
6.RL缓冲抑制电路
![RL缓冲抑制电路](https://cugerui-img.oss-cn-beijing.aliyuncs.com/2021/RL%E7%BC%93%E5%86%B2%E6%8A%91%E5%88%B6%E7%94%B5%E8%B7%AF.png)
实例：
![EMI实例](https://cugerui-img.oss-cn-beijing.aliyuncs.com/2021/%E6%8C%AF%E9%93%83%E7%8E%B0%E8%B1%A1%E5%8F%8A%E6%88%90%E5%9B%A0.png)

### RC缓冲抑制电路
1.振铃现象及成因：
![振铃现象及成因](https://cugerui-img.oss-cn-beijing.aliyuncs.com/2021/%E6%8C%AF%E9%93%83%E7%8E%B0%E8%B1%A1%E5%8F%8A%E6%88%90%E5%9B%A0.png)
此信号频率通常在200-400MHz之间，可导致高频EMI辐射。
2.RC缓冲抑制电路可以有效地抑制振铃现象，但会降低效率（最大1%-2%）
![RC缓冲抑制](https://cugerui-img.oss-cn-beijing.aliyuncs.com/2021/RC%E7%BC%93%E5%86%B2%E6%8A%91%E5%88%B6.png)
3.RC值的确定
![RC值的确定1](https://cugerui-img.oss-cn-beijing.aliyuncs.com/2021/RC%E5%80%BC%E7%9A%84%E7%A1%AE%E5%AE%9A1.png)
![RC值的确定2](https://cugerui-img.oss-cn-beijing.aliyuncs.com/2021/RC%E5%80%BC%E7%9A%84%E7%A1%AE%E5%AE%9A2.png)

### PCB布局
不好的布板会引起：
> EMI
地线噪声
布线上产生电压降

布局建议：
1)反馈：
反馈电阻靠近FB脚，短而细。
反馈线要避开电感、二极管、大电容，可以从底层走线，使用地线包围。连在输出电容之后。
（6-12mil，降低天线效应）
2)电容：
输入电容尽可能靠近芯片的Vin和GND，以减小高频电流环路。
输出电容放在电感旁边即可，Cout的地和Cin的地要保持1-2cm的距离。
减小Cin、上管、下管、二极管组成的回路面积。
3)电场：
SW节点和BOOT引脚有很高的电压变化率dV/dt，会导致严重的电场辐射，因而其铜箔面积应当保持最小化，还要避开其它敏感电路。
4)大电流和小信号分开：
信号地（AGND，有FB、COMP、SS、FET...）和功率地（PGND，输入电容、肖特基二极管、EN）分开，AGND位于干净无噪声的地方，单点连接到PGND。
5)使用地线层时，要尽力保持输入切换回路下面地层的完整性。
6)芯片、电感、肖特基是主要的发热器件，注意PCB热量的均匀分配，避免局部温升高。
7)电感尽量靠近SW脚，电感下不要走线。
1盎司（35um）板材，线宽>1mm/A
2盎司（70um）板材，线宽>0.7mm/A
8)RC缓冲抑制电路必须放置靠近芯片的GND和SW引脚，外置NMOS的，将其直接接在下管的D、S两端。
9)Cin和Cout的地附近加过孔连接到底层的地，以降低阻抗，电流更易流过，确保电容起到滤波作用。
（12/24mil或16/34mil）
10)Vin、SW、Vout、GND是大电流路径，大电流处可以在solder层加焊料。



