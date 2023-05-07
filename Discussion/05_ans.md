# 05 CALL, RISC-V, Procedures

## 1. Pre-Check
1.1 False; 因为 a 类型的寄存器也会被改变。只有 saved 类型的寄存器不会被改变

1.2 False;

1.3 False; 在调用函数前后，保存 t 类型寄存器是也会操作 stack

1.4 True; 编译器也可能生成伪指令，伪指令会被汇编器翻译成机器指令

1.5 False; 汇编器不是优化代码的

1.6 False; 链接器会调整目标文件中未确定地址的变量

1.7 ~~True;~~False 有点 trick 相对跳转地址是不知道的，但不影响。

## 2. 调用约定练习

2.1 

s0 s3 s9 ra

2.2

t0 a7

2.3

$t_0$ $t_1$ $t_5$ -> a7 也需要保存，t0 不需要保存，因为后面没有用

2.4

s0 s3 s9

# 3. 翻译二进制指令
3.1
```
addi s1 x0 -24 -> 0b1111 1110 1000 0000 0000 0100 1001 0011 = 0xFE800493

sh s1 4(t1) -> 0b0000 0000 0110 0100 1001 0010 0010 0011 = 0x0064A223 对sh系列指令，s1 s2 顺序相反
0b 0000 0000 1001 0011 0001 0010 0010 0011 = 0x93 1223
```
3.2 
```
0x234554B7=0b 0010 0011 0100 0101 0101 0100 1 011 0111= lui s1 ~~13397~~ 错了0x23455
0xFE050CE3=0b 1111 1110 0000 0101 0 000 1100 1 110 0011= beq x10 x0 -3~~ -8 错了 #有4位0省略了
```

# 4 RISC-V 寻址

4.1 

branch 指令是 B 型指令，B 型指令有 12 bits 立即数用于 label。32位指令有2位0，16位指令有1位0。所以我们可以省略1位0。
所以总共有13位来表示一个地址。使用 2 进制补码的话，可以表示$[2^{12}, 2^{12}-1]$地址范围的指令，除以 4 得到可以跳转的32位指令个数范围是：$[2^{10}, 2^{10}-1]$

4.2 

jump 指令是 J 型指令有 20 位来立即数用于寻址，算法跟上面差不多。最终结果是：
$[2^{20}, 2^{20}-1]$地址范围，$[2^{19}, 2^{19}-1]$ 32 位指令个数

4.3 
```
0x002cff00: loop: add t1, t2, t0       0x00538333
0x002cff04: jal ra, foo                0x0000E0EF 错了# J 指令可以省一位0, 排布复杂
0x002cff08: bne t1, zero, loop         0xFE0318E3
0x002cff2c: foo: jr ra             ra = 0x002cff08

0x002cff04: jal ra, foo -> 0b 0000 0010 1000 0000 0000 0000 1110 1111 -> 0x280 00EF

```