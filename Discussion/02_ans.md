# 1. Pre-Check
1.1 C 是传值语言 True

1.2 下面是正确的 C 语法： False
```
int num = 43
```

1.3 在编译的语言中，编译时间通常很快，然而运行时间显著慢于解释性语言： False

1.4 正确的声明数组的方式是 `char[]` False

1.5 位表达式和逻辑表达式结果是一样的. False

# 2. 位操作
```
a = 0b1000 1011
b = 0b0011 0101
c = 0b1111 0000


(a) a & b = 0b0000 0001
(b) a ^ c = 0b0111 1011
(c) a | 0 = 0b1000 1011
(d) a | (b >> 5) = 0b1000 1011
(e) ~((b | c) & a) = 0b0111 1110
```

# 3. 传什么？

```
(a) 
void swap(int* a, int*b) {
    int c = *a;
    *a = *b;
    *a = c;
}
(b) int mystrlen(const char* str) {
    int ret = 0;
    while (*str++) {
        ret++;
    }
    return ret;
}
```

# 4. 调试
```
(a) 无法知道数组长度

(b) 字符长度未判断，++运算符优先级

(c) 没错：
> 一个表达式中两个 ++ 会导致未定义行为，不好排错，最好别这么写

(d) 声明两个指针需要： char *srcptr, *replaceptr;
> 未设置 '\0'

```