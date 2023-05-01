# 1 Precheck

1.1 False; 这取决于 x 数组的类型

1.2 False; 'd' 是100， 100 / 4 = 25 所以应该是 25 

1.3 True;

1.4 False; jalr 跳转到一个寄存器指定的绝对地址，运行时行为，而 jal 跳转到一个 label 上，汇编器会把它翻译成立即数

1.5 False; j 不保存返回地址，而 jal 保存

# 2. 指令

2.1 有一个 int 数组 arr = [1 2 3 4 5 6 0], s0 保存 arr 地址， 判断一下指令的作用


```
a) lw t0, 12(s0) --> 把 arr[3] 存入寄存器 t0 = 4
b) sw t0, 16(s0) --> 把 t0 寄存器数值存入 arr[4] = 4
c) slli t1, t0, 2 --> t1 = 16
add t2, s0, t1    --> t2 = &arr[4]
lw t3, 0(t2)      --> t3 = arr[4] = 4
addi t3, t3, 1    --> t3 = arr[4] + 1 = 5
sw t3, 0(t2)      --> arr[4] = t3 = 5
d) lw t0, 0(s0)   --> t0 = arr[0] = 1
xori t0, t0, 0xFFF --> t0 ^= 0xFFF = 0xFFE； 取反
addi t0, t0, 1  ---> t0 += 1 = 0xFFF ；+ 1
```

2.2 
```
blt s0 s1

bne s0 s1 ； bge s1 s0

ble s0 s1

blt s1 s0

```


# 3 继续翻译

```
// s0 -> a, s1 -> b
// s2 -> c, s3 -> z
int a = 4, b = 5, c = 6, z;
z = a + b + c + 10;

addi s0 x0 4
addi s1 x0 5
addi s2 x0 6
add  s3 s0 s1
add s3 s3 s2
addi s3 s3 10
```
```
// s0 -> int * p = intArr;
// s1 -> a;
*p = 0;
int a = 2;
p[1] = p[a] = a;

sw x0 0(s0)
addi s1 x0 2 # a = 2
slli t0 s1 2
add t0 t0 s0
sw s1 0(t0)
sw s1 4(s0)
```
```
// s0 -> a, s1 -> b
int a = 5, b = 10;
if(a + a == b) {
  a = 0;
} else {
  b = a - 1;
}

addi s0 x0 5
addi s1 x0 10

add t0 s0 s0

bne t0 s1 ELSE
add s0 x0 x0
jal x0 DONE
ELSE: 
addi s1 s0 -1
DONE
```
```
addi s0, x0, 0 # s0 = 0
addi s1, x0, 1 # s1 = 1
addi t0, x0, 30 # t0 = 30
loop:
beq s0, t0, exit # if s0 != t0 goto exit
add s1, s1, s1 # s1 += s1
addi s0, s0, 1 # s0++
jal x0, loop # goto loop
exit:
int a = 0, b = 1, c = 30
while (a != c) {
  b = b + b
  a++
}


// s0 -> n, s1 -> sum
// assume n > 0 to start
for(int sum = 0; n > 0; n--) {
sum += n;
}

add s1 x0 x0
loop:
ble s0 x0 exit
add s1 s1 s0
addi s0 s0 -1
jal x0 loop
exit
```

# 4 RISC-V 中的数组
4.1
```
lw t0, 0(s0)    # 将数组中第一个元素加载到 t0 = 3
lw t1, 8(s0)    # 将数组中第三个元素加载到 t1 = 4
add t2, t0, t1  # t2 = t0 + t1 = 7
sw t2, 4(s0)    # 将 t2 存入数组第二个位置 arr[1] = t2 = 7

```

4.2
``` 循环将链表元素 +1
loop: beq s1, x0, end   # if s1 == 0 goto end
lw t0, 0(s1)            # 将 s1 指向的结构体第一个元素加载到 t0 =
addi t0, t0, 1          # t0++
sw t0, 0(s1)            # 将 t0 存回 s1 指向的结构体的第一个元素
lw s1, 4(s1)            # 将 s1 指向的结构体的第二个元素加载到 s1
jal x0, loop            # 无条件跳转到 loop 标签
end:            
```

4.3
``` 循环将数组元素变为相反数
add t0, x0, x0       # 将 t0 设置为 0
loop: slti t1, t0, 6 # 如果 t0 < 6, 将 t1 设置为 1
beq t1, x0, end      # 如果 t1 == 0, goto end
slli t2, t0, 2       # 将 t0 左移两位(x 4)，结果存到 t2 = 0
add t3, s0, t2       # t3 = s0 + t2 = s0
lw t4, 0(t3)         # 将 t3 指向的四字节存放到 t4 = 3
sub t4, x0, t4       # t4 = -t4
sw t4, 0(t3)         # 将 t4 存入 t3 指向的四个字节
addi t0, t0, 1       # t0++
jal x0, loop         # 无条件跳转到 loop
end:

```

# 5 内存获取
5.1 
```
li x5 0x00FF0000 # 将立即数存入 x5
lw x6 0(x5)      # 将 x5 指向的 4 字节存入 x6 = 36
addi x5 x5 4     # x5 += 4 x5 = 0x00FF0004
lh x7 2(x5)      # 将 x5 + 2 指向的 2 字节存入 x7 = 0x0000000C
lw x8 0(x6)      # 将 x6 指向的 4 字节存入 x8 = *(36) = 0xDEADB33F
lb x9 3(x7)      # 将 x7 + 3 指向的 1 字节 存入 x9 = 0x000000C5

```

5.2
```
li x5 0xABADCAFE # x5 = 0xABADCAFE
li x6 0xF9120504 # x6 = 0xF9120504
li x7 0xBEEFCACE # x7 = 0xBEEFCACE
sw x5 0(x6)      # (*x6) = x5 = 0xABADCAFE         0xF9120504 -> 0xABADCAFE
addi x6 x6 4     # x6 += 4 = 0xF9120508
addi x5 x5 4     # x5 += 4 = 0xABADCB02
sh x6 2(x5)      # 在 x5 + 2 位置存入 x6 的两个字节  0xABADCB04 -> 0xCAFE
sb x7 1(x7)      # 在 x7 + 1 位置存入 x7 的一个字节  0xBEEFCACF -> 0xCE
sb x7 3(x6)      # 在 x6 + 3 位置存入 x7 的一个字节  0xF9120508 -> 0xCE
sb x7 3(x5)      # 在 x5 + 3 位置存入 x7 的一个字节  0xABADCB05 -> 0xCE

```