# 06 SDS FSM 同步信号系统，有限状态机

## 1. Pre-Check
1.1 简化逻辑表达式对硬件实现的性能没有影响： False.不同的逻辑表达式会形成不同的逻辑门电路，进而会有不同的传播延迟。

1.2 假设每个门的传播延迟相同，逻辑门越少，电路越快： ~~True~~ false, 逻辑门并行的话对电路没有太大影响。真正影响速度的是电路关键路径的延迟。

1.3 clock-to-q 时间 和寄存器设置时间可以比时钟周期长： False. 这可能导致不稳定性。如果寄存器相互连接，寄存器的输出可能没有正确的被传播，因为在下一个上升沿，不满足下一个电路的各种输入时间而产生未定义行为。

1.4 任何组合逻辑电路都可以表达成 NOR 门的组合: ~~False~~ True，NOR可以用来表达任何组合电路逻辑

1.5 两种状态元素的最短的`组合逻辑路径`在确定`电路频率`和`最小时钟周期`时有用：~~True~~

最小的时钟周期必须给出充足的时间来让每一个电路确定一个输出，因此频率是基于电路中的最长路径的。

## 2. 逻辑门

2.1
* not
* and
* or
* xor
* nand
* nor
* xnor

2.2

$\overline{AB}$ = $\overline{A} + \overline{B}$

$\overline{A}B+A\overline{B}$

$\overline{A}*\overline{B} + AB$

2.3 使用 NAND gate 创造 AND gates

ppt 中有答案

## 3. 布尔逻辑
3.1

$\overline{A} + AB =\overline{A(\overline{AB})}=\overline{A(\overline{A}+\overline{B})}=\overline{0+A\overline{B}}=\overline{A\overline{B}}=\overline{A}+B$

3.2 
可用数学归纳法证明：假设 n 个信号的德摩根定律成立，则有：

对于 n + 1 的情况有 ... (变换到基于 n 个信号的形式)。所以 n + 1 的情况成立

3.3 

(a) 看成 + 的分配律，可化简
$(A+B)(A+\overline{B})C=(A+(B\overline{B}))C=AC$

(b) 分组合并之后

$\overline{A}*\overline{C}+A$ 根据3.1 $\overline{C}+A$

(c) 
$\overline{A} + \overline{B}C + B\overline{C}$

(d)
$A+B$

## 4. 状态介绍

4.1 画图的

4.2 
最大 hold time: 4 + 5 + 5 = 14 ns

最小时钟周期： 4 + 5 + 5 + 5 + 6 = 25ns 40MHz

## 5. 有限状态机

5.1
* 初始状态有点问题，不过能猜出结果
* 探测11
* 以上序列输出：001000000110

5.2

5.3
都是画图，就不写在这了
