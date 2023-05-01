# 1. Pre-check
1.1 内存段由硬件定义，无法修改： False

1.2 对于大的递归程序，你应该把数据存储在堆上： True 争议，看内存管理
    

1.3 浮点数的目标是：表示更大的数值范围，更低的精度以及更准确的计算结果：False

1.4 浮点数之间的剧烈，会随着浮点数的绝对值的增加而增加： True

1.5 浮点数加法是可结合的：False，存在大数吃小数的情况

# 2. 内存管理

2.1 判断以下变量在哪个区域
> (a) 静态变量 静态区域
> 
> (b) 局部变量 栈
> 
> (c) 全局变量 静态区域
> 
> (d) 常量 静态区 ？？？？ 静态、代码段、栈上都有可能
> 
> (e) 机器指令 代码区
> 
> (f) 动态分配的内存 堆
> 
> (g) 字符串常量 静态区
> 
> 

2.2 写出内存分配代码

```
(a) k 个整数

int *arr = (int*)malloc(sizeof(int) * k);

(b) p 个字符

char *chr = (char*)malloc(sizeof(char) * (p + 1));

(c) n x m 大小的整数矩阵，初始化为 0

int **mat = (int**)malloc(sizeof(int*) * n);
for (int i = 0; i < n; i++)
    mat[i] = (int*)calloc(m, sizeof(int));

第二种解法：
mat = (int *) calloc(n * m, sizeof(int));
```
2.3 以下两个复制字符串的代码，哪一个对？哪一个快？

~~都对，第一个快。第二个还需要初始化内存~~
第一个不对，没有初始化最后的`'\0'`


2.4 下面代码的主要问题是？

两次可能得内存越界：64 和 11

# 3 链表
3.1 prepend函数
```
void prepend(struct ll_node** lst, int value) {
    struct ll_node* new_ll = (ll_node*)malloc(sizeof(ll_node));
    new_ll->first = value;
    new_ll->rest = *lst;
    *lst = new_ll;
}
```

3.2 free函数

```
void free_ll(struct ll_node** lst) {
    ll_node* p = *lst;
    *lst = NULL;
    while (p) {
        ll_node* np = p->rest;
        free(p);
        p = np;
    }
}
```

# 4. 浮点数

4.1 二进制转换

* 0x00000000 -> +0

* 8.25 -> 0b0100 0001 0000 0100 000...
       
       -> 0x41040000

* 0x00000F00 -> 0b 0 000 0000 0 000 0000 0000 1111 0000 0000 
             
             -> 非常小的数 `5.38098610301e-42`
             
             -> $(2^{-12} + 2^{-13} + 2^{-14} + 2^{-15}) * 2^{-126}$

* 39.5625 -> 0b 0 100 0010 0 001 1110 0100 0000 0000 0000
          -> 0x 421E4000

* 0xFF94BEEF -> 0b 1 111 1111 1 001 0100 1011 1110 1110 1111 
             
             -> NaN

*  -∞ -> 0b 1 111 1111 1 000 0000 0000 000... 
      
      -> 0xff800000

* 1/3 -> 0b 0011 1110 1010 1xxxxx 不想转了 N/A 无法准确表示

      -> 0x3EAAAAAB


# 5 更多的浮点表示
5.1 比 2 大的第一个准确表示的浮点数是多少(32位)？
$2 + 2^{-22}$
```
2 表示为： 0b 0100 0000 0....
比它大 1 的数字为
0x40000001 -> 2.00000023842
```

5.2 比 4 大的第一个准确表示的浮点数是多少(32位)？
$4 +4^{-21}$
```
4 表示为： 0b 0100 0000 100....
比它大 1 的数字为
0x40800001 -> 4.00000047684
```
5.3 最大的奇数？
$2^{24}-1$