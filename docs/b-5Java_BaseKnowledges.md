## **Java 基础**

### HashMap 的源码，实现原理，JDK8 中对 HashMap 做了怎样的优化。

1. 优化方面

- 在JDK1.6,JDK1.7,HashMap采用位桶+链表实现,使用链表解决冲突,同一hash值的链表都存储在一个链表里,但是当位于一个桶中的元素较多,也就是hash值相等的元素较多时,通过key一次查找的效率较低.
- 在JDK1.8中.HashMap采用的是位桶+链表+红黑树实现,当链表长度超过阈值8时,将链表转换成红黑树,这样大大减少了查找的时间

2. 原理
   - 当添加一个键值对时,首先计算key的hash值,以此确定插入数组的位置,但是可能存在同一hash值的元素已经放在这个数组的同一位置上了,这时候就只能添加到同一hash值的元素的后面.他们在数组的同一位置,但是形成了链表,同一各链表上的Hash值是相同的,所以说数组存放的是链表.
   - 而当链表长度太长时,链表就转换成`红黑树`了,这就大大的提高了查询效率
3. 当链表数组的容量超过初始容量的0.75时,再散列将链表数组扩大2倍,再把原来的链表数组搬到新的数组中

![1594885307609](D:/Typora/Pictures/1594885307609.png)

### HaspMap 扩容是怎样扩容的，为什么都是 2 的 N 次幂的大小。

- 使用2的N次幂进行位运算时,一方面能高效的计算,其次就是能够充分的散列,使得添加的元素均匀的分布在HashMap的每个位置上,减少hash碰撞.

### HashMap，HashTable，ConcurrentHashMap 的区别。

1. HashMap
   1. 底层:数组+链表+红黑树
   2. 键值对:允许存储key = null && value = null ====>只能用containsKey()来判断是否存在某个key==
   3. 线程上:不安全
   4. 初始size:16
2. HashTable
   1. 底层:数组+链表
   2. 键值对:不允许存储键值对为null的情况
   3. 线程安全,一次锁住整个Hash表
   4. 初始size:11
3. ConcurrentHashMap
   1. 底层:分段数组+链表
   2. 替代了HashTable,扩展性比较好,一次锁住一个桶,默认将Hash表分成16个桶,这样就能并发的16个写线程执行.

### 极高并发下 HashTable 和 ConcurrentHashMap 哪个性能更好，为什么，如何实现的。

### HashMap 在高并发下如果没有处理线程安全会有怎样的安全隐患，具体表现是什么。

1. 多线程在put的时候可能导致get的无限循环,导致CPU的使用率达到100%
   - 原因
     - 在向HashMap put元素时，会检查HashMap的容量是否足够，如果不足，则会新建一个比原来容量大两倍的Hash表，然后把数组从老的Hash表中迁移到新的Hash表中，迁移的过程就是一个rehash()的过程，多个线程同时操作就有可能会形成循环链表，所以在使用get()时，就会出现Infinite Loop的情况
2. 在put的时候,可能导致值的丢失
   - 原因
     - 在多线程进行addEntry(hash,key ,value,i)时,如果产生了碰撞,导致两个线程得到同样的bucketIndex去存储,就可能发生元素覆盖丢失的情况
3. 解决方法
   1. **使用并发包下的java.util.concurrent.ConcurrentHashMap，ConcurrentHashMap实现了更高级的线程安全；或者使用synchronizedMap() 同步方法包装 HashMap object，得到线程安全的Map，并在此Map上进行操作。**

### java 中四种修饰符的限制范围。

| 访问权限  | 本类 | 本包 | 不同包子类 | 不同包非子类 |
| :-------: | :--: | :--: | :--------: | :----------: |
|  public   |  √   |  √   |     √      |      √       |
| protected |  √   |  √   |     √      |              |
|  default  |  √   |  √   |            |              |
|  private  |  √   |      |            |              |

### Object 类中的方法。

```properties
 toString();

 equals();

 hashCode();
```



###  接口和抽象类的区别，注意 JDK8 的接口可以有实现。

###  动态代理的两种方式，以及区别。

1. JDK动态代理
2. CGlib代理

### Java 序列化的方式。

> 对象的序列化就是将对象写入输出流中。
>
> 反序列化就是从输入流中将对象读取出来。

**ObjectOutputStream**:提供序列化对象并把其写入流的方法

**ObjectInputStream**：读取流并反序列化对象

------

**Serializable**：

- 一个对象想要被序列化，那么它的类就要实现 此接口，这个对象的所有属性（包括private属性、包括其引用的对象）都可以被序列化和反序列化来保存、传递。

**Externalizable：**

- 他是**Serializable**接口的子类，有时我们不希望序列化那么多，可以使用这个接口，这个接口的writeExternal()和readExternal()方法可以指定序列化哪些属性;

​        但是如果你只想隐藏一个属性，比如用户对象user的密码pwd，如果使用Externalizable，并除了pwd之外的每个属性都写在writeExternal()方法里，这样显得麻烦，可以使用Serializable接口，并在要隐藏的属性pwd前面加上transient就可以实现了。

### 传值和传引用的区别，Java 是怎么样的，有没有传值引用。

1. 值传递(形式参数类型是基本数据类型)

   - 方法调用时,实参把他的值传递给对应的形参,形参只是用实参的值初始化自己的存储单元内容,是两个不同的存储单元,所以方法中执行的形参的数值的改变不会影响实参.

2. 引用传递(形参类型的引用类型)

   - 也称为传地址,方法调用时,实参(或数组),这时实参与形参指向同一个地址,在方法执行中,对形参的操作实际上就是对实参的操作,这个结果在方法结束后被保留下来,指向同一内存地址.

   

### 一个 ArrayList 在循环过程中删除，会不会出问题，为什么。

- for正向循环

  - 由于执行remove方法,删除时会执行System.arraycopy(elementData, index+1, elementData, index,
    numMoved)方法，导致删除元素时涉及到数组元素的移动。====>这就导致会有一个字符串并没有遍历到,也就没有办法删除了==
  - 报并发修改异常====>ConcurrentModificationException==

- for逆向循环

  - 逆向循环的时候则不会出现上述问题

- 使用迭代器时

  - 单线程情况下

    - 　通过 remove 方法删除元素最终是调用的 fastRemove() 方法，在 fastRemove() 方法中，首先对 modCount 进行加 1 操作（因为对集合修改了一次），然后接下来就是删除元素的操作，最后将 size 进行减 1 操作，并将引用置为 null 以方便垃圾收集器进行回收工作。

        　　那么注意此时各个变量的值：对于 iterator，其 expectedModCount 为 0，cursor 的值为 1，lastRet 的值为 0。

        　　对于 list，其 modCount 为 1，size 为 0。

        　　接着看程序代码，执行完删除操作后，继续 while 循环，调用 hasNext 方法 () 判断，由于此时 cursor 为 1，而 size 为 0，那么返回 true，所以继续执行 while 循环，然后继续调用 iterator 的 next()方法：

        　　注意，此时要注意 next() 方法中的第一句：checkForComodification()。

        　　在 checkForComodification 方法中进行的操作是：

      ```java
      final void checkForComodification() {
          if (modCount != expectedModCount)
          throw new ConcurrentModificationException();
      }
      ```

       　　如果 modCount 不等于 expectedModCount，则抛出 ConcurrentModificationException 异常。

      　　很显然，此时 modCount 为 1，而 expectedModCount 为 0，因此程序就抛出了 ConcurrentModificationException 异常。

      　　到这里，想必大家应该明白为何上述代码会抛出 ConcurrentModificationException 异常了。

      　　关键点就在于：调用 list.remove() 方法导致 modCount 和 expectedModCount 的值不一致。

      　　注意，像使用 for-each 进行迭代实际上也会出现这种问题。

  - 因此，在迭代器中如果要删除元素的话，需要调用 Itr 类的 remove 方法。

    - ```java
      public class Test {
          public static void main(String[] args)  {
              ArrayList list = new ArrayList();
              list.add(2);
              Iterator iterator = list.iterator();
              while(iterator.hasNext()){
                  Integer integer = iterator.next();
                  if(integer==2)
                      iterator.remove();   //注意这个地方
              }
              
          }
      }
      ```

- 多线程情况下

  - 多线程情况下并不是能通过使用线程安全的Vector就能解决的

  - 实际上通过 Iterator 访问的情况下，每个线程里面返回的是不同的 iterator，就是说 expectedModCount 是每个线程私有。假若此时有 2 个线程，线程 1 在进行遍历，线程 2 在进行修改，那么很有可能导致线程 2 修改后导致 Vector 中的 modCount 自增了，线程 2 的 expectedModCount 也自增了，但是线程 1 的 expectedModCount 没有自增，此时线程 1 遍历时就会出现 expectedModCount 不等于 modCount 的情况了。

    　　因此一般有 2 种解决办法：

      　　1）在使用 iterator 迭代的时候使用 synchronized 或者 Lock 进行同步；

      　　2）使用并发容器 CopyOnWriteArrayList 代替 ArrayList 和 Vector。

### @transactional 注解在什么情况下会失效，为什么。

1、service 类标签 (一般不建议在接口上) 上添加 @Transactional，可以将整个类纳入 spring 事务管理，在每个业务方法执行时都会开启一个事务，不过这些事务采用相同的管理方式。

2、@Transactional 注解只能应用到 public 可见度的方法上。 如果应用在 protected、private 或者 package 可见度的方法上，也不会报错，不过事务设置不会起作用。

3、默认情况下，Spring 会对 unchecked 异常进行事务回滚；如果是 checked 异常则不回滚。 
辣么什么是 checked 异常，什么是 unchecked 异常

java 里面将派生于 Error 或者 RuntimeException（比如空指针，1/0）的异常称为 unchecked 异常，其他继承自 java.lang.Exception 得异常统称为 Checked Exception，如 IOException、TimeoutException 等

辣么再通俗一点：你写代码出现的空指针等异常，会被回滚，文件读写，网络出问题，spring 就没法回滚了。然后我教大家怎么记这个，因为很多同学容易弄混，你写代码的时候有些 IOException 我们的编译器是能够检测到的，说以叫 checked 异常，你写代码的时候空指针等死检测不到的，所以叫 unchecked 异常。这样是不是好记一些啦

4、只读事务： 
@Transactional(propagation=Propagation.NOT_SUPPORTED,readOnly=true) 
只读标志只在事务启动时应用，否则即使配置也会被忽略。 
启动事务会增加线程开销，数据库因共享读取而锁定 (具体跟数据库类型和事务隔离级别有关)。通常情况下，仅是读取数据时，不必设置只读事务而增加额外的系统开销。



## **数据结构和算法**

1. B + 树。
2. 快速排序，堆排序，插入排序（其实八大排序算法都应该了解）。
3. 一致性 Hash 算法，一致性 Hash 算法的应用。

**JVM**

1. JVM 的内存结构。
2. JVM 方法栈的工作过程，方法栈和本地方法栈有什么区别。
3. JVM 的栈中引用如何和堆中的对象产生关联。
4. 可以了解一下逃逸分析技术。
5. GC 的常见算法，CMS 以及 G1 的垃圾回收过程，CMS 的各个阶段哪两个是 Stop the world 的，CMS 会不会产生碎片，G1 的优势。
6. 标记清除和标记整理算法的理解以及优缺点。
7. eden survivor 区的比例，为什么是这个比例，eden survivor 的工作过程。
8. JVM 如何判断一个对象是否该被 GC，可以视为 root 的都有哪几种类型。
9. 强软弱虚引用的区别以及 GC 对他们执行怎样的操作。
10. Java 是否可以 GC 直接内存。
11. Java 类加载的过程。
12. 双亲委派模型的过程以及优势。
13. 常用的 JVM 调优参数。
14. dump 文件的分析。
15. Java 有没有主动触发 GC 的方式（没有）。

**多线程**

1. Java 实现多线程有哪几种方式。
2. Callable 和 Future 的了解。
3. 线程池的参数有哪些，在线程池创建一个线程的过程。
4. volitile 关键字的作用，原理。
   1. 保证了内存的可见性
      - 每次`读取前`必须先从主内存刷新最新的值。
      - 每次`写入后`必须立即同步回主内存当中。
      - 也就是说，**volatile关键字修饰的变量看到的随时是自己的最新值**。线程1中对变量v的最新修改，对线程2是可见的。
   2. 防止指令重排
      - 在基于`偏序关系`的`Happens-Before内存模型`中，指令重排技术大大提高了程序执行效率，但同时也引入了一些问题。
5. synchronized 关键字的用法，优缺点。
6. Lock 接口有哪些实现类，使用场景是什么。
7. 可重入锁的用处及实现原理，写时复制的过程，读写锁，分段锁（ConcurrentHashMap 中的 segment）。
8. 悲观锁，乐观锁，优缺点，CAS 有什么缺陷，该如何解决。
9. ABC 三个线程如何保证顺序执行。
10. 线程的状态都有哪些。
11. sleep 和 wait 的区别。
12. notify 和 notifyall 的区别。
13. ThreadLocal 的了解，实现原理。

**数据库相关**

1. 常见的数据库优化手段。
2. 索引的优缺点，什么字段上建立索引。
3. 数据库连接池。
4. durid 的常用配置。

## **计算机网络**

1. TCP，UDP 区别。
2. 三次握手，四次挥手，为什么要四次挥手。
3. 长连接和短连接。
4. 连接池适合长连接还是短连接。

## **设计模式**

1. 观察者模式。
2. 代理模式。
3. 单例模式的五种写法。
4. Spring 中使用了哪些设计模式

## **分布式相关**

1. 分布式事务的控制。
2. 分布式锁如何设计。
3. 分布式 session 如何设计。
4. dubbo 的组件有哪些，各有什么作用。
5. zookeeper 的负载均衡算法有哪些。
6. dubbo 是如何利用接口就可以通信的。

## **框架相关**

### Spring是如何解决循环依赖的

- Spring通过三级缓存解决了循环依赖,其中一级缓存为单例对象池==>singletonObjects,二级缓存为早期曝光对象earlySingletonObjects,三级缓存为早期曝光对象工厂===>singletonFactories
- 当A,B两个类发生循环依赖引用时,在A完成实例化后,就使用实例化后的对象去创建一个对象工厂,并添加到三级缓存中,如果A被AOP代理,那么通过这个工厂获取得到的就是A代理后的对象,如果A没有被AOP代理,难么这个工厂获取得到的就是A实例化的对象,当A进行属性注入时,会去创建B,同时B又去依赖A,所以创建B的同时又会去调用getBean(a)来获取需要的依赖,此时getBean(a)会从缓存中获取,第一步,先获取到三级缓存中的工厂;
- 第二步,调用对象工厂的getObject方法来获取得到对应的对象,得到对象后将其注入到B中.
- 紧接着B会走完他的生命周期流程,包括初始化,后置处理器等.当B创建完,再将B注入到A中,此时A再完成它的生命周期.最终,整个循环依赖结束

为什么使用三级缓存呢?二级缓存能解决循环依赖吗?

- 如果要使用二级缓存解决循环依赖,意味着所有Bean在实例后就要被AOP代理,这样句违背 Spring设计的原则,Spring在设计之初就是通过AnnotationAwareAspectJAutoProxyCreator这个后置处理器来在Bean生命周期的最后一步来完成AOP代理,而不是在实例化就立马进行AOP代理.