# HashMap 相关知识点和面试题
HashMap 是 Java 面试中最常见、也是最基础的用于考察面试者的数据结构。可以说，我们不仅要在日常的开发中会用 HashMap，更要深入研究它的源码，学习它的实现原理。从而在面试中能够较为理想的回答出面试官想要的答案。
## HashMap 的常见知识点
- [数据结构](#DataStructure)
- [扩容机制](#Capacity)
- [线程安全](#ThreadSafe)
- [equals 和 HashCode ](#EqualsAndHashcode)

### <span id="DataStructure">数据结构</span>
**关键点** 
1. 数组和链表组合而成的
2. Java7 中，数组叫`Entry`，Java8 中叫`Node`；Java7 中，是正常的链表，Java8 之后，改成了链表+红黑树
3. Java7 中是头插；Java8 之后变成了尾插法
4. 默认初始化长度是16

回答：  \
HashMap 在Java7 和Java8 中的数据结构不一样。Java7 中就是纯粹的数组+链表。但是在Java8 之后，HashMap 就变成了数组+链表+红黑树的数据结构。还有一点，没什么逻辑上的改变，但是Java 7 的数组叫Entry ，Java8 中的数组叫 Node。
### <span id="Capacity">扩容机制</span>
**关键点**
1. `put` 方法的整个过程
2. 数组和链表的插入细节
3. 尾插和头插的原因(保证一部分的线程安全)  

回答： \
当我们put一个数据时，首先会通过哈希算法计算出这个元素的插入位置。假如put("userId","7721"):  
> int pos = hash("userId");

因为数组总是有限的，所以哈希一定数量的元素后，就会出现不同的key，hash到了同一个位置上。这就会形成链表。Node 的每个节点都会保存自身的hash、key、value、以及下一个节点。

![HashMap扩容示意图](http://normalupdate.oss-cn-hangzhou.aliyuncs.com/2020-09-23-HashMap%E6%89%A9%E5%AE%B9%E7%A4%BA%E6%84%8F%E5%9B%BE.jpg)

数组的容量是有限的，因此到达一定的数量就会进行扩容，也就是resize。

resize 涉及到两个参数：Capacity 和 LoadFactor -HashMap当前的长度和负载因子。

HashMap的扩容分为两步：
- 扩容：创建一个新的 Entry（Node）数组，长度是原数组的2倍；
- ReHash : 遍历原来的Entry 数组，把所有的Entry重新 hash 到新数组中。

插入链表，Java 8 之前是头插法：新来的值会取代原有的值，原有的值会被推送到链表中去。比如pos=1已经被A元素占用，现在B元素被哈希到了pos=1的位置，那么就把A推到链表中去，B成为pos=1的数组中的值。

Java8 之后，就改成了头插法。主要是考虑在扩容的时候，使用头插方式，可能会导致无限循环。

### <span id="ThreadSafe">线程安全</span>
**HashMap 不是线程安全的**  \
Java 7 中，因为头插法的存在，很有可能导致死循环的问题。Java 8 中的使用了尾插法，虽然防止了这类问题的出现。但是我们认为，HashMap 没有假如同步锁，无法保证多线程时的安全性。因此我们认为它不是线程安全的。
### <span id="EqualsAndHashcode">equals 和 HashCode</span>
**重写equals 方法时必须重写 hashCode 方法** \
equals 继承的是Object类的euqals方法，比较的是两个类的内存地址。

## HashMap 的常见面试题
1. 谈一下HashMap的特性？
2. 谈一下HashMap的底层原理是什么？
3. 谈一下hashMap中put是如何实现的？
4. 谈一下hashMap中什么时候需要进行扩容，扩容resize()又是如何实现的？
5. 谈一下hashMap中get是如何实现的？
6. 谈一下HashMap中hash函数是怎么实现的？还有哪些hash函数的实现方式？
7. 为什么不直接将key作为哈希值而是与高16位做异或运算？
8. 谈一下当两个对象的hashCode相等时会怎么样？
9. 如果两个键的hashcode相同，你如何获取值对象？
10. 如果HashMap的大小超过了负载因子(load factor)定义的容量，怎么办？
11. HashMap和HashTable的区别
12. 请解释一下HashMap的参数loadFactor，它的作用是什么？
13. 传统hashMap的缺点(为什么引入红黑树？)

### 文档参考：
1. https://zhuanlan.zhihu.com/p/40760616
2. https://github.com/AobingJava/JavaFamily
3. https://zhuanlan.zhihu.com/p/32355676