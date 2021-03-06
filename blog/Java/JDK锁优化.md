> * 这些带“锁”的名词，不是具体的java编程过程，可以使用的“锁”；而是JDK在运行程序时，对“锁”这一功能的优化。    

### 自旋锁
* 挂起线程再切回来，太慢；可以不放弃CPU时间而“等待一会儿”，看看是否很快就拿到锁。
* 前提： 多个CPU；当这两个线程不在一个CPU上，这番考量才有意义
* 这个过程称为自旋
* JDK 1.6 开始设置为默认开启，并引入了自适应能力
* 自适应： 由之前等待的耗时，来决定这次是否自旋，最多自旋等待多久。
 
### 锁消除
* 不会触发竞争的地方，去除锁
* 不会竞争，却加锁，很多不是程序员自己有意加入的。例如：`String str = str1 + str2 + str3; return str;` 实际上会变成
`StringBuffer sb = new StringBuffer();sb.append(str1);sb.append(str2);sb.append(str3);return sb.toString();`而append()内部是加锁的。

### 锁粗化
* 某一块代码反复使用某一共享数据，反复加锁/解锁；JDK优化成再整个代码块外面加锁。
* 总的效率更高
 
### 轻量级锁
* 预备知识：对象有一个2bit的标记位，01表示未锁定，00表示轻量级锁定，10表示重量级锁定 

1. 如果同步对象被锁住 - 常规等待
2. 如果没有被锁住 - 锁标志位01
    1. clone一个同步对象的对象头的MarkWord部分，到当前线程的栈中，称起LockRecord
    2. 尝试用CAS 将原对象头的MarkWord更新为指向这个LockRecord
        * 如果成功，01->00
        * 如果失败，进入阻塞等待
3. 书上的说法是“大部分锁，在程序运行时实际上不会真的发生竞争”，“**轻量级锁使用CAS操作避免了互斥量的开销**”  ->**==不懂这是啥开销==**
  
### 偏向锁
* 与轻量级锁，原理类似
* 取到锁以后，对象头的标记为置01，表示偏向模式；
* CAS成功后，以后不再做任何同步操作
* 第二个线程来取资源，就退回到轻量级锁
* MarkWord除了标记位，还有其它部分，如threadID等，用于区分若干种状态
* **==书上没说清==**



