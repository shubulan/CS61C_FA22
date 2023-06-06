# 11 Parallelism

## 1 pre-check

1.1 SIMD 是一种指令级别的并行  ~~对~~

错误。指令级并行是同时执行多个指令，例如：流水线。SIMD 是数据并行。

1.2 SIMD 针对控制逻辑较多的任务的理想模型

错，应该是数据密集型的

1.3 英特尔的 SIMD 指令调用了大的寄存器，用于一次性在多个值上执行操作 对

1.4 每一个硬件线程在 CPU 上使用共享的缓存 ~~对~~

错误，每个线程都有特自己的缓存，因此会导致缓存一致性问题

1.5 硬件线程的数量可以超过核心的数量 对

1.6 在线程级别的并行上 加速量与硬件线程数量的增加成正比
错误：阿姆达尔定律

## 2. 数据层的并行

2.1 将下列函数 SIMD 化，它返回所有元素的内积

```
static int product_vectorized(int n, int *a) {
    int result[4];
    __m128i prod_v = _mm_set1_epi32(1);
    for (int i = 0; i < n / 4 * 4; i += 4) {
        prod_v = _mm_mullo_epi32(prod_v, _mm_loadu_si128((__m128i*)(&a[i])));
    }
    __mm_storeu_si128((__m128i*)result, prod_v);
    for (int i = n / 4 * 4; i < n; i++) {
        result[0] *= a[i];
    }
    return result[0] * result[1] * result[2] * result[3];
}

```

## 3. 线程级并行
3.1 对下面的每一个问题，陈述并证明程序：有时不正确，总是不正确，比序列化还慢，比序列化快。都不是。

a): 

比序列化还慢。 没有 for 指示关键字，所以每个线程都完整执行整个循环。额外的并行开销减慢了运行速度。

b):

~~有时不正确。线程之间的数据有依赖~~
总是不正确。循环有数据依赖，所以除了第一个线程外都依赖上一个线程。因为我们认为：没有现成会在别的线程结束后才开始，因此这个代码认为总是错误的

c):

比序列化快。for 关键字会自动让循环变量 i 为私有的，所以这个是正确的。 for 关键字 将循环的迭代分割成连续的段给每一个线程，所以没有数据依赖或者假共享

d):

~~总是不正确~~
有时不正确。因为我们没有索引数组，所以对`arr++`会有数据竞争。

3.2 下面代码有什么潜在问题？


## 4 一致性
多线程编程的优势，只有在你理解了一致性之后才能获得。有两个最常见的一致性问题：
    1. 缓存一致性：可以通过绕过缓存来做到，例如 volatile 关键字或者使用协议来解决，例如：MOESI
    2. 读-修改-写：这是最常见的编程模式。在多线程编程中，读、修改、写三个阶段的插入总是会造成各种各样的问题


### 4.1 MOESI 协议
并行处理允许CPU的各个内核作为独立单元运行，并有自己的缓存。然而，要做到这一点，机器必须能够协调所有内核和所有缓存的信息流，使这些信息在某种程度上是可靠的。因此，我们强加了缓存状态，由 valid有效位、dirty 脏位和 shared 共享位组成，以表示特定缓存块的缓存数据的状态。这些缓存状态是在某一核心的缓存发生缺失或写入时使用的，这样，如果一个地方的信息被修改，其他的缓存也会被告知。总之，我们不希望两个有不同数据的缓存都说自己有最新的数据，因为这根本不可能是真的。换句话说，从主处理器的角度来看，它们的缓存行状态可能会因为代理处理器执行的动作而被更新。

4.1 将下面所有的状态和对应的状态关联
(a) data in host cache up-to-date

(b) data in main memory is outdated

(c) data in main memory up-to-date

(d) if evicted, host cache must write this line’s data back to main memory

(e) no copies exist in other (proxy) caches

(f) copies may exist in other (proxy) caches

(g) access from processor will result in a miss

1. Modified 
2. Owned
3. Exclusive
4. Shared
5. Invalid

## 4.2 原子指令
为了解决 RMW 问题，我们得依赖不可打断的执行操作，也就是说 原子操作。

RISC-V 里面有两类原子操作
1. Amoswap: 在单一指令内，允许不可打断的内存操作执行
2. Load-reserve, store-conditional: 在跨指令间 允许不可打断的执行

这两类都可以用于实现 原子元语。我们这里关注前面的例子
```
Test-and-set

```

4.3 为什么需要在并行化的实现中使用原子指令？

4.4 在上述程序的哪两行之间 线程开始并行化执行代码？