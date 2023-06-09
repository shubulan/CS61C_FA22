# OS/VM/IO 

## 1. Precheck
1.1 操作系统的职责包括加载程序、处理服务、将程序组合起来以提高效率 

~~虽然有些名词比较怪，但是应该是对的~~
错的: 处理服务是有的：网络、文件系统等，但是程序组合起来提高效率是错的。他是将程序隔离保护

1.2 操作系统使用上下文切换来实现多进程同时运行在多个 CPU

~~对~~，上下文切换用于在一个cpu上切换任务。这种同时运行程序(多道程序的设计)的幻觉与真正的并行执行是不同的

1.3 虚拟内存帮助保护系统 

对。操作系统可以确保页面给到特定的程序并不会被别的程序访问

1.4 虚拟地址空间被系统中的内存限制了

错. 物理地址空间受限于硬件，但虚拟地址空间由 操作系统设置

1.5 虚拟与物理的页数量必须相等

错，数量可以不同，但是页面大小必须相等

1.6 如果在TLB中没找到页表项，则发生页错误。

错，先去内存中的页表中找

1.7 Polling 和 中断是低层次编程的唯一概念

错，还有DMA。类似的概念适用于几乎所有类型的应用程序，包括 Web 程序、移动端、分布式系统

1.8 内存映射 IO 只在 polling 中起作用

错, ~~有时候会主动请求设备，这不是polling~~ 中断后也会使用

## 2. 寻址

2.1 使用虚拟内存有什么具体的好处？

三个：
* 解决空间不足: 虚拟空间没有不足的, 提供无限空间的幻觉
* 保护内存: 不同进程间内存隔离, 由操作系统设置的只读页等隔离技术
* 防止内存碎片: 使用 page, 虚拟空间总是连续的，链接、加载器不用担心别的进程造成的空洞

2.2 当新值被加载到页表地址寄存器，TLB 发生什么？

相当于发生上下文切换，页表被完全刷新, 有效位全部置 0

## 3 VM 访问模式

3.1
| VPN  | PPN  | Valid | Dirty | LRU |
|------|------|-------|-------|-----|
| 0x01 | 0x11 | 1     | 1     | 5   |
| 0x13 | 0x17 | 1     | 1     | 3   |
| 0x10 | 0x13 | 1     | 1     | 6   |
| 0x20 | 0x12 | 1     | 1     | 1   |
| 0x23 | 0x18 | 1     | 1     | 2   |
| 0x11 | 0x14 | 1     | 0     | 4   |
| 0xac | 0x15 | 1     | 1     | 7   |
| 0x34 | 0x19 | 1     | 1     | 0   |

## 4 Polling & Interrupts

4.1 比较二者优缺点 

* Polling:

定义： cpu 定期的去检查 IO 设备是否就绪，从而处理 IO 事件

优点： 时效更高, 延迟低，数据可用时开销低。适用于 设备总是忙碌,或者设备数据必须获得才能继续的情况。

缺点： cpu 干了太多与计算无关的事, 对于 大量的 IO 密集操作不友好。轮询时无法进行其他操作，且满速运转

* Interrupts

定义： io 设备就绪时打断 CPU 当前的执行流，去做我的事情

优点： 无需 cpu 主动去做事情,即使等待响应也可以做别的事。适用于需要很长时间才能响应的设备

缺点： 对于大量 io 密集操作，不友好。中断发生时间不确定，中断处理程序具有一定的开销(上下文切换),延迟高(CPU可以选择做一些收尾工作，等一段时间再处理中断),吞吐量差


## 5 内存映射 IO

5.1 
```
    li t0 x0 0x80000000
    li t1 x0 0xFFFF0000
loop_rec:
    lw t2 0(t1)
    and t2 t2 t0 
    beq t2 x0 loop_rec 
    li t1 x0 0xFFFF0004
    lb t2 0(t1)
    li t1 x0 0xFFFF0008
loop_tra:
    lw t3 0(t1)
    and t3 t3 t0 
    beq t3 x0 loop_tra
    li t1 x0 0xFFFF000C
    sb t2 0(t1)
```