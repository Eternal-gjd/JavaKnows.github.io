## day01

### JVM、JRE和JDK

```java
`java语言的跨平台原理`
	`通过JVM虚拟机实现跨平台， 在不同操作系统上安装对应的虚拟机`

`JVM、JRE和JDK`
	`JVM ：虚拟机， 实现java语言的跨平台`
	`JRE ：运行时环境， 包含了JVM和核心类库， 运行java程序`
	`JDK ：java开发工具包， 包含JRE和开发工具， 开发人员需要安装JDK`

`程序运行的过程：`
	`编写代码`
	`编译代码  javac 文件名.java`
	`运行代码  java 文件名`

`Path环境变量`
	`配置JAVA_HOME后， 可以在任意文件夹下使用JDK的bin目录下的开发工具`

`常见DOS命令`
	`dir : 查看目录下文件结构`
	`cd  : 进入子目录`
	`cd 目录1\目录2  ： 进入多级目录`
	`cd .. ：退出单级目录`
	`cd ..\.. : 退出多级目录`
	`cd \  ：回到根目录`
	`cls : 清屏`
	`exit : 退出命令提示符窗口`

`HelloWorld案例`
`public class HelloWorld{`
    `public static void main(String[] args){`
		`System.out.println("Hello World");`
    `}`
`}

```

### 数据类型

#### 基本数据类型

#### 引用数据类型

### 类型转换

#### 自动类型转换

#### 强制类型转换

### 常量--变量

~~~java
注释
	单行注释  //
	多行注释 /**/
	文档注释 /***/

关键字
	单词全部小写
	在高级开发工具中， 会有特殊的颜色标记
	
常量
	在程序运行中，值不能改变
	字符串 ：0到多个字符
	字符   ： 单个字符
	整数
	小数常量
	布尔常量 true false
	空常量 null
    
数据类型
	基本数据类型（4型8种）
		整型
			byte  short int long
		浮点型
			float double
		字符型
			char
		布尔型
			boolean
			

```
整数默认是int类型， 小数默认是double类型
引用数据类型 ：字符串，数组...
```

变量 
	在程序运行过程中，其值可以在一定范围内改变
	

```
定义格式：数据类型 变量名 = 初始化值;
定义格式2： 数据类型 变量名; 
	      变量名 = 初始化值;
```

变量的使用注意：
	未初始化的变量不能直接使用。
	long类型加L 
	float类型加F
	同一{}范围内，变量名不能重复
	
标识符
	组成规则：大小写字母、数字、下划线、美元符

类型转换
	自动类型转换 ：小类型转大类型
	强制类型转换 ：大类型转小类型，弊端是会丢失数据精度
		格式： 目标数据类型  变量名 = (目标数据类型)变量/常量值;
		int a = 100;
		byte b = (byte)a;
~~~

## day02

### 逻辑运算符

~~~java
算术运算符 : + - * / %
    / : 整数相除得整数, 想要得要小数, 需要有小数参与
    % : 取两个数相除的余数
    
 字符的 + 运算 : 将字符换算成编码表里面对应的数字进行运算
 字符串的 + 运算 : 与字符串进行拼接, 结果还是字符串

 表达式的类型提升:
	byte short char 变量之间参与运算, 类型会提升为int
	

```
	byte a = 10+5;
 	
其他类型参与运算, 结果以最高类型为准
byte short char-->int-->long -->float-->double
```

扩展赋值运算符: 隐含了强制转换

byte a = 10; 
//a = (byte)(a+5);不建议
a+=5;

自增自减
	a++
    a--
    ++a
    --a
    

```
参与其他运算:
		   a++  :先参与其他运算, 再自增
```

   			++a  :先自增, 再参与其他运算
   			
== : 等于的判断
= : 赋值的操作

逻辑运算符:
	&  : 有false则false
    |  : 有true则true
    !  :取反
    ^  :相同则false ,不同则true
短路逻辑运算
	&& :有false则false ,具有短路效应
	|| :有true则true ,具有短路效应
~~~

### 三元运算符

### If语句的三种结构

### Scanner类

~~~java
学习目标:
1、能够说出常见的运算符有哪几种
	算术运算符: + - * / %
    赋值运算符: = += -= *= /= %=
    自增自减 : ++  --
    关系运算符: == != > >= < <=
    逻辑运算符: & | ! ^ && ||
    三元运算符: 数据类型  变量名 = 关系表达式 ? 表达式1: 表达式2;
2、能够知道除法和取余的区别
	/ :取两个数的商, 整数相除得整数
	% :取两个数的余数
3、能够完成字符和字符串的加法
	字符加运算: 字符转换成编码表里面对应的数字进行运算
			a~z  97-122
            A~Z  65-90
            0~9  48-57
    字符串的加运算: 拼接操作

4、能够知道&&和&的区别
	&: 有false则false,所有的表达式都会执行
    &&:有false则false,具有短路效应.
        

```
|: 有true则true,所有的表达式都会执行
||:有true则true,具有短路效应.
```

5、能够使用三元运算符完成获取两个数中较大值
	int a = 10;
    int b = 20;
	int max = a>b? a:b;
6、能够使用键盘录入数据并完成两个数据求和
	键盘录入数据的三个步骤:
7、Scanner类:
	导包 import java.util.Scanner;
	创建对象 Scanner sc = new Scanner(System.in);
	调用方法获得键盘输入的整数 
		int a = sc.nextInt();
		int b = sc.nextInt();
		int sum = a+b;
if语句的三种格式
if(关系表达式){
	语句体1;
}

if(关系表达式){
	语句体1;
}else{
	语句体2;
}

if(关系表达式){
	语句体1;
}else if(关系表达式){
	语句体2;
}else{
	语句体n+1;
}

7、能够使用if语句完成获取两个数中较大值
8、能够使用if语句完成根据分数输出对应级别
~~~

## day03

### switch语句的结构

### for循环***

```java
switch结构
格式:
switch(表达式){
    case 值1:
        语句体1;
        break;
    case 值2:
        语句体2;
        break; 
    ......
    default:
        语句体n+1;
}
注意事项:
    1. 表示式值的类型 byte short char int 字符串  枚举
    2. case后面的值只能是常量, 不能重复
	3. break的作用是结束switch结构, 如果省略break, 会出现case穿透

for循环
格式:
    for(初始化语句; 循环判断语句; 循环控制语句){
        循环体语句;
    }
执行流程:
    1. 执行初始化语句(只执行一次)
    2. 执行循环判断语句, 如果为true, 执行循环体, 为false,结束循环
    3. 循环体语句
    4. 执行循环控制语句, 回到第2步继续执行
    
案例:
//循环打印1~5
for(int i=1; i<=5;i++){
   System.out.println(i); 
}

//打印5~1
for(int i=5; i>=1;i--){
    System.out.println(i); 
}

求某个数的个位, 十位, 百位
    个位: 该数%10
    十位: 该数/10%10
    百位: 该数/100%10
```

### while循环两种结构

### 三种死循环写法

#### While(true)-案例

### break和continue的区别

### 嵌套循环

### Random类

#### Random练习-猜数字-无限循环（应用）

```java
while循环
格式:
    初始化语句
    while(循环判断条件){
        循环体语句;
        循环控制语句;
    }
//案例:打印1~5
int i =1;
while(i<=5){
    System.out.println(i);
    i++;
}

do-while
格式:
初始化语句
    do{
         循环体语句;
         循环控制语句;
    }while(循环判断条件);

特点: 先执行,再判断(至少会执行一次)
    
三种循环的区别:
    1. for和while循环, 是先判断,再执行, do-while循环先执行,再判断.
    2. for循环的初始化值只能在循环体内部使用, while和do-while循环的初始化值可以在循环体外部使用.
    3.推荐使用的顺序 :for循环  while循环  do-while循环
    
三种循环的死循环简单写法:
for(;;){
    
}

while(true){
    
}

do{
    
}while(true);

break和continue
    break:用于结束循环
    continue:用于跳出本次循环, 开始下一次循环
    break和continue后面都不能直接跟其他语句
    
循环嵌套: 循环体是循环结构, 循环总次数= 内循环*外循环

Random类
	作用:生成随机数
	步骤:
		1. 导包 import java.util.Random;
		2. 创建对象 Random  r = new Random();
		3. 产生随机数  int num = r.nextInt(10);// 生成0~9之间的整数
```

## day04

### IDEA的创建工程,导入工程以及快捷键的运用

```java
创建idea工程:
	创建工程-->创建模块-->在src中建包--->在包中建类

idea中的项目组成:
	一个工程是idea中管理的代码的单位,工程中可以创建多个模块,模块中可以创建多个包,包中可以创建多个类

idea的快捷键
    单行注释:  Ctrl + /
    多行注释:  Ctrl + Shift + /
    格式化代码:  Ctrl +  Alt + L
    快速生成main方法: psvm + 回车
    快速生成输出语句 : sout + 回车
    快速生成方法调用的返回值:Alt+ctrl+v
工程中有模块文件夹, 
有out目录,存放class文件的地方
.idea 文件夹 : 存放工程的管理和配置文件.
    
模块中:
模块名.iml :模块的配置文件, 导入模块时可以选择该文件导入模块
```

### 数组的格式

### 数组元素初始化的两种方式

#### 静态初始化,动态初始化

### 数组的遍历

### 数组的最值问题

```java
1、能够使用IDEA完成HelloWorld案例
2、能够知道IDEA的项目结构
	工程 模块 包  类
3、能够使用IDEA完成模块的导入
	
4、能够知道数组的定义格式
	int[]  arr
	int arr[]

5、能够使用两种方式完成数组元素的初始化
	动态初始化 : 指定数组长度, 元素值系统默认给出
	int[] arr = new int[5];
		数组元素的默认值:
            整数:0
            小数:0.0
            字符: ' '
            布尔: false
            字符串: null

	静态初始化 : 指定数组元素, 长度由系统给出
		标准格式: int[] arr = new int[]{1,2,3,4,5}; (了解)
		简化格式: int[] arr = {1,2,3,4,5}; (掌握)

6、能够知道数组在内存中的初始化过程(了解)
	栈内存: 存储局部变量, 方法运行结束就消失
    堆内存: 储存new出来的数据, 数组, 对象 , 在堆中的数据等待垃圾回收器回收.
        
    int[] arr = new int[5];
		在栈内存中创建int[] arr, 
		new int[5]在堆内存中创建数组, 并为数组元素赋初始化值, 将地址返回给栈内存的变量arr
	
7、能够完成数组的遍历操作(掌握)
	索引: 数组元素的编号, 从0开始, 最大值是 数组长度-1,
	获取数组长度: 数组名.length
	使用for循环遍历数组
    for(int i=0; i<arr.length; i++){
    	System.out.println(arr[i]);
    }

8、能够完成数组的获取最值操作(掌握)
	最大值, 最小值
```

## day05

#### 无参数无返回值方法定义:

```public static void 方法名(){

	方法体;

}


调用:  方法名();



#### 带参数的方法定义:

​```java
public static void 方法名(参数类型1  参数名1, 参数类型2  参数名2, ...){
    方法体;
}

调用: 方法名(参数值1, 参数值2,...);


```

#### 形参: 

方法定义时在小括号里定义的参数.

#### 实参: 

方法调用时传递的实际的值

方法参数为引用数据类型时,地址传递, 形参的改变直接影响实参,这样可以用void.

#### 带返回值的方法定义:

public static 数据类型  方法名(参数类型 参数名...){
	方法体;
	return  数据值/变量名;
}

#### 带返回值的调用:

​	直接调用  : 方法名(参数值1, 参数值2,...); //造成返回值的丢失
​	输出调用 : 需要直接打印返回值  Syste.out.println(   方法名(参数值1, 参数值2,...)  );

##### 	赋值调用 : 

数据类型 变量名 = 方法名(参数值1, 参数值2,...); //使用最多的方式, 推荐的方式

#### 调用方法的两个明确

##### 1.明确返回值类型:

方法计算的是整数的求和,结果也必然是个整数,返回值类型定义为int类型.

##### 2.明确参数列表:

计算哪两个整数的和,并不清楚,但是可以确定是整数,参数列表可以定义为两个int类型的变量,由调用者调用方法时传递

#### 方法重载的定义(条件)

是指同一个类中,方法名相同,参数列表不同(参数个数或者类型不同均可),与修饰符跟返回值类型无关.

#### 知识点回顾

```java
1、能够知道方法定义和通用格式 (重点掌握和应用)

​    通用格式:
public static 数据类型(void) 方法名(数据类型 参数名,...){
​    方法体;
​    return 数据值/变量名; //如果方法时void, 该行代码省略
}

void方法的调用: 方法名(实参);
有返回值的方法调用:
	直接调用: 方法名(实参); 会丢失返回结果
	输出调用 : System...
    赋值调用 : 数据类型  变量名 = 方法名(实参);

两个明确:
	明确参数:
	明确返回值:
    
    
2、能够知道形参和实参的区别 (理解)
    形参: 方法定义的时候在小括号里面定义的参数
	实参: 方法调用的时候传递的实际的值(常量, 变量)
    
3、能够使用方法完成两个较大数的获取并调用方法 (应用)

4、能够知道方法重载的特点 (理解)
   构成方法重载的条件: 同一个类中, 方法名相同, 参数列表不同(参数个数或者类型不同均可)
    
5、能够使用方法重载完成比较两个整数是否相同并调用方法 (应用)
       

6、能够知道方法的参数是基本类型和引用类型的区别 (理解)
       参数是基本类型: 形参的改变不会影响实参
	   参数是引用类型: 形参的改变会直接影响实参
    
7、能够使用方法完成数组遍历并调用方法 (应用)

8、能够使用方法完成获取数组最大值并调用方法 (应用)
```

## day08

#### **知识总结**

```java
1. String类的特点

   1) String是不可变的
   2) String底层是字节数组, 可以看成字符组数
   3) 字符串常量存在常量池里的, 可以共享的

2. String类的构造方法
   //无参构造方法
   String();

   //带参数的构造方法
   String("字符串常量");
   String(char[] arr);
   String(byte[] arr);

   //常用的方式, 使用双引号创建对象
   String 变量名 = "字符串内容";

3. String创建对象的特点
   String s = new String("abc"); // 该行代码创建了几个对象?
   两个:
   	首先, 会去常量池检查有没有"abc"常量, 此时常量池中并没有"abc" ,所以会在常量池中创建, 
   	其次通过new关键字, 会在堆中创建一个对象, 并引用了常量池的字符串内容, 并将堆中的地址返回给变量s

   String s = new String("abc"); 
   String s2 = new String("abc");// 两行代码创建了几个对象?
   共创建3个对象

   String s = "abc";
   String s2 = "abc";
   //以上两行代码总共创建了一个对象, 并且是存在于常量池中的

4. 字符串的比较

   基本数据类型: 比较数据值是否相等
   引用数据类型: 比较堆内存地址是否相同

    equlas()
        字符串的equlas()方法比较的是字符串内容是否相等, 而且区分大小写
        equalsIgnoreCase()  不区分大小写比较字符串内容是否相同

5. 字符串长度: length()
   根据索引获取某个字符 : charAt(int index)
   将字符串转换成字符数组 : char[]  toCharArray();

6. StringBuilder类
   StringBuilder在java.lang包中, 不需要导包
   和String的转换
   	StringBuilder转String : StringBuilder的toString()方法
   	String 转 StringBuilder :使用StringBuilder的带参构造方法 new StringBuilder(String s)

   常用的方法:
   	字符串的拼接 : append(任意类型) ,返回StringBuilder对象本身
   	字符串反转 : reverse(), 对字符串内容进行反转扩展：String类常用方法汇总
```

#### **扩展**--**String类常用方法汇总**

```java
   //获取长度
   int length()
   	获取字符串长度。
   	
   //字符操作
   char charAt(int index)
   	根据索引返回指定字符，索引从0开始。
   public char[] toCharArray()
   	将字符串转换为字符数组返回。

   //判断相等
   boolean equals(Object anObject)
       判断字符串内容是否相等，区分大小写。
   boolean equalsIgnoreCase(String anotherString)
       判断字符串内容是否相等，不区分大小写。
    
    
       
   //字符串比较
   boolean startsWith(String prefix)
       判断字符串是否以指定的前缀开始。
   boolean endsWith(String suffix)
       判断字符串是否以指定的后缀结束。
       
       
   //字符串替换
   String trim()
   	截去字符串两端的空格，但对于中间的空格不处理。
   String replace(char oldChar, char newChar)
   	用字符newChar替换当前字符串中所有的oldChar字符，并返回一个新的字符串。

   //字符串切割
   String[] split(String regex)
   	根据指定的regex符号将字符串分割成字符串数组返回。   
       
       
   //大小写转换
   String toLowerCase()
   	字符串全部转小写返回。
   String toUpperCase()
   	字符串全部转大写返回。
   	

   //提取子串
   String substring(int beginIndex)
   	从beginIndex位置起，获取后面剩余子串返回。
   String substring(int beginIndex, int count)
   	取出beginIndex开始，共count个字符。
```

## day09

```java
1.集合和数组的区别
	数组的特点: 长度固定, 存储的数据一致
	集合的特点: 动态存储元素, 数据类型可以多样

2.ArrayList集合创建对象
	ArrayList<引用类型>  list = new ArrayList<引用类型>();

3.ArrayList常用方法
    添加集合元素
		add(元素值);
		add(要添加的位置索引, 元素值);

    删除集合元素
    	boolean remove(要删除的元素);
		remove(要删除的元素索引);


    修改集合元素
		set(要修改的元素索引, 新的元素值);

    查询集合元素
    	E get(元素索引);
	
	获取元素个数
		list.size();
	
4.ArrayList集合元素遍历
for(int i=0; i<list.size(); i++){
	数据类型 变量 = list.get(i);
}
```



## 构造方法

1. 如果你不提供构造方法，系统会给出无参数构造方法。
2. 如果你提供了构造方法，系统将不再提供无参数构造方法。
3. 构造方法是可以重载的，既可以定义参数，也可以不定义参数 
4. 引用类型的方法一般可以不用返回类型

## Math

![1563452310111](C:\Users\DaX\AppData\Roaming\Typora\typora-user-images\1563452310111.png)

## Arrays--操作数组的方法

```java
public static String toString(int[] a) ：返回指定数组内容的字符串表示形式。
public class ClassName {
static {
// 执行语句
}
} 
public class Game {
public static int number;
public static ArrayList<String> list;
static {
// 给类变量赋值
number = 2;
list = new ArrayList<String>();
// 添加元素到集合中
list.add("张三");
list.add("李四");
}
}

public static void sort(int[] a) ：对指定的 int 型数组按数字升序进行排序。
3.3 练习
请使用 Arrays 相关的API，将一个随机字符串中的所有字符升序排列，并倒序打印。

4.1 概述
public static void main(String[] args) {
// 定义int 数组
int[] arr = {2,34,35,4,657,8,69,9};
// 打印数组,输出地址值
System.out.println(arr); // [I@2ac1fdc4
// 数组内容转为字符串
String s = Arrays.toString(arr);
// 打印字符串,输出内容
System.out.println(s); // [2, 34, 35, 4, 657, 8, 69, 9]
} p
ublic static void main(String[] args) {
// 定义int 数组
int[] arr = {24, 7, 5, 48, 4, 46, 35, 11, 6, 2};
System.out.println("排序前:"+ Arrays.toString(arr)); // 排序前:[24, 7, 5, 48, 4, 46, 35, 11,
6, 2]
// 升序排序
Arrays.sort(arr);
System.out.println("排序后:"+ Arrays.toString(arr));// 排序后:[2, 4, 5, 6, 7, 11, 24, 35, 46,
48]
} 
```



## String的用法

String() 创建一个空的字符串对象
String(String original) 根据字符串来创建一个字符串对象 

String(char[] value) 通过字符数组来创建字符串对象
String(byte[] bytes) 通过字节数组来构造新的字符串对象
String(byte[] bytes, int offset, int length) 通过字节数组一部分来构造新的字符串对象 

```java
 String类常用方法
         char charAt(int index) 获得指定位置的字符

         String	concat(String str) 字符串拼接

         boolean contains(CharSequence s)
            判断字符串是否包含指定的子串，有返回true，否则false

         boolean endsWith(String suffix)
            判断字符串是否以指定的子串结尾，是返回true，否则false
         boolean startsWith(String prefix)
            判断字符串是否以指定的子串开始，是返回true，否则false

         boolean	equals(Object anObject)
            判断两个字符串内容是否相同，相同返回true，否则false

         byte[]	getBytes()
            将字符串转换为字节数组

         int indexOf(String str)
            获得字符串str出现的位置，返回索引值
            如果没有找到则返回-1

         boolean isEmpty()
            判断字符串是否为空串：长度是否为0，是返回true，否则false

         int length()
            获得字符串的长度

         String[] split(String regex)
            使用指定的分隔符分割字符串，得到一个字符串数组

         String	toLowerCase()
            将字符串转换为小写
            "Abc" ==> "abc"

         String	toUpperCase()
            将字符串转换大写字符串
            "abc" ==> "ABC"

         String	trim()
            去除字符串左右的空格
            "  abc  ".trim() ==> "abc"

         static String	valueOf(int i)
            将整数转换字符串
            String.valueOf(100) ==> "100"
        // 字符串
        String str = "abc";
        // char charAt(int index) 获得指定位置的字符
        System.out.println(str.charAt(1)); // b

        //  String	concat(String str) 字符串拼接
        System.out.println(str.concat("123")); // abc123

        // boolean contains(String s) 判断字符串是否包含指定的子串，有返回true，否则false
        System.out.println(str.contains("a")); // true

       /* boolean endsWith(String suffix)
        判断字符串是否以指定的子串结尾，是返回true，否则false*/
        System.out.println(str.endsWith("c")); // true
        System.out.println(str.endsWith("d")); // false

       /* boolean startsWith(String prefix)
        判断字符串是否以指定的子串开始，是返回true，否则false*/
       System.out.println(str.startsWith("ab")); // true
       System.out.println(str.startsWith("cb")); // false

        System.out.println("----------------");
       /*
       int indexOf(String str)
        获得字符串str出现的位置，返回索引值
        如果没有找到则返回-1
        */
       System.out.println(str.indexOf("yyy"));
       /* boolean isEmpty()
            判断字符串是否为空串：长度是否为0，是返回true，否则false */
        System.out.println(str.isEmpty()); // false
        System.out.println(str.length() == 0);

        System.out.println("----------------");
        /*String[] split(String regex)
            使用指定的分隔符分割字符串，得到一个字符串数组 */
        String s = "192-167-134-10";
        String[] strs = s.split("-");
        System.out.println(Arrays.toString(strs)); // [192,167,134,10]

        // String	substring(int beginIndex)
        // 从指定位置开始截取字符串，直到末尾
        String a = "abcffasfasfaf";
        System.out.println(a.substring(3,6));

```



```java
   public static void main(String[] args) {
// 1.String() 创建一个空的字符串对象
        String str1 = new String();
        System.out.println("str1:" + str1);
// 2.String(String original) 根据字符串来创建一个字符串对象
        String str2 = new String("abc");
        System.out.println("str2:" + str2);
// 3.String(char[] value) 通过字符数组来创建字符串对象
        char[] chs = {'A', 'B', 'C', 'D', 'E'};
        String str5 = new String(chs);
        System.out.println("str5:" + str5);
// 4.String(byte[] bytes) 通过字节数组来构造新的字符串对象
        byte[] bytes = {97, 98, 99, 100, 101}; // a, b, c, d, e
        String str3 = new String(bytes);
        System.out.println("str3:" + str3);
// 5.String(byte[] bytes, int offset, int length) 通过字节数组一部分来构造新的字符串对象
        String str4 = new String(bytes, 1, 2);
        System.out.println("str4:" + str4);
        String str6 = "abcde";
        System.out.println("str6:" + str6);
```

#### 判断功能用法

public boolean equals (Object anObject) ：将此字符串与指定对象进行比较。
public boolean equalsIgnoreCase (String anotherString) ：将此字符串与指定对象进行比较，忽略大小
写。 

```java
public class String_Demo01 {
public static void main(String[] args) {
// 创建字符串对象
       String s1 = "hello";
       String s2 = "hello";
       String s3 = "HELLO";
// boolean equals(Object obj):比较字符串的内容是否相同
       System.out.println(s1.equals(s2)); // true
       System.out.println(s1.equals(s3)); // false
       System.out.println("‐‐‐‐‐‐‐‐‐‐‐");
//boolean equalsIgnoreCase(String str):比较字符串的内容是否相同,忽略大小写
       System.out.println(s1.equalsIgnoreCase(s2)); // true
       System.out.println(s1.equalsIgnoreCase(s3)); // true
       System.out.println("‐‐‐‐‐‐‐‐‐‐‐");
   }
} 
```

#### 获取功能的方法

public int length () ：返回此字符串的长度。
public String concat (String str) ：将指定的字符串连接到该字符串的末尾。
public char charAt (int index) ：返回指定索引处的 char值。
public int indexOf (String str) ：返回指定子字符串第一次出现在该字符串内的索引。
public String substring (int beginIndex) ：返回一个子字符串，从beginIndex开始截取字符串到字符
串结尾。
public String substring (int beginIndex, int endIndex) ：返回一个子字符串，从beginIndex到
endIndex截取字符串。含beginIndex，不含endIndex。 

```java
public class String_Demo02 {
   public static void main(String[] args) {
//创建字符串对象
       String s = "helloworld";
// int length():获取字符串的长度，其实也就是字符个数
       System.out.println(s.length());
       System.out.println("‐‐‐‐‐‐‐‐");
// String concat (String str):将将指定的字符串连接到该字符串的末尾.

       String s = "helloworld";
       String s2 = s.concat("**hello itheima");
       System.out.println(s2);// helloworld**hello itheima
// char charAt(int index):获取指定索引处的字符
       System.out.println(s.charAt(0));
       System.out.println(s.charAt(1));
       System.out.println("‐‐‐‐‐‐‐‐");
// int indexOf(String str):获取str在字符串对象中第一次出现的索引,没有返回‐1
       System.out.println(s.indexOf("l"));
       System.out.println(s.indexOf("owo"));
       System.out.println(s.indexOf("ak"));
       System.out.println("‐‐‐‐‐‐‐‐");
// String substring(int start):从start开始截取字符串到字符串结尾
       System.out.println(s.substring(0));
       System.out.println(s.substring(5));
       System.out.println("‐‐‐‐‐‐‐‐");
// String substring(int start,int end):从start到end截取字符串。含start，不含end。
       System.out.println(s.substring(0, s.length()));
       System.out.println(s.substring(3, 8));
   }

} 
```

#### 转换功能的方法

public char[] toCharArray () ：将此字符串转换为新的字符数组。
public byte[] getBytes () ：使用平台的默认字符集将该 String编码转换为新的字节数组。
public String toLowerCase() ：使用默认语言环境的规则将此 String所有字符转换为小写。
public String toUpperCase() ：将此 String所有字符转换为大写，使用默认语言环境的规则。
public String replace (CharSequence target, CharSequence replacement) ：将与target匹配的字符串使
用replacement字符串替换。

```java
 public class String_Demo03 {
       public static void main(String[] args) {
//创建字符串对象
           String s = "abcde";
// char[] toCharArray():把字符串转换为字符数组
           char[] chs = s.toCharArray();
           for (int x = 0; x < chs.length; x++) {
               System.out.println(chs[x]);
           }
           System.out.println("‐‐‐‐‐‐‐‐‐‐‐");
// byte[] getBytes ():把字符串转换为字节数组
           byte[] bytes = s.getBytes();
           for (int x = 0; x < bytes.length; x++) {
               System.out.println(bytes[x]);
           }
           System.out.println("‐‐‐‐‐‐‐‐‐‐‐");
// 替换字母it为大写IT
           String str = "itcast itheima";
           String replace = str.replace("it", "IT");
           System.out.println(replace); // ITcast ITheima
           System.out.println("‐‐‐‐‐‐‐‐‐‐‐");
       }
   }

```



#### 分割功能的方法

public String[] split(String regex) ：将此字符串按照给定的regex（规则）拆分为字符串数组。 

```java
public class String_Demo03 {
public static void main(String[] args) {
	//创建字符串对象
	String s = "aa,bb,cc";
	String[] strArray = s.split(","); // {"aa","bb","cc"}
  }
} 
```

#### 继承的注意事项

![1563689567780](C:\Users\DaX\AppData\Roaming\Typora\typora-user-images\1563689567780.png)

#### 继承时子类重名的三种变量区分

局部变量: 	直接写成员变量名;

本类的成员变量:	this.成员变量名

父类的成员变量:	super.父类成员变量名

子父类中出现了同名的成员变量时，在子类中需要访问父类中非私有成员变量时，需要使用 super 关键字，修饰
父类成员变量，类似于之前学过的 this 。 

Fu 类中的成员变量是非私有的，子类中可以直接访问。若Fu 类中的成员变量私有了，子类是不能
直接访问的。通常编码时，我们遵循封装的原则，使用private修饰成员变量，那么如何访问父类的私有成员
变量呢？对！可以在父类中提供公共的getXxx方法和setXxx方法。 

#### this关键字的三种用法

super关键字用来访问父类内容,而this关键字用来访问本来内容.

1.在本类的成员方法中,访问本类的成员变量.

2.在本类的成员方法中,访问本类的另一个成员方法.

3.在本类的构造方法中,访问本类的另一个构造方法.

   A:其中this(...);调用也必须是构造方法的第一个语句,唯一一个.

   B:super和this两种构造调用,不能是同时使用

## static静态方法

![1563795156992](C:\Users\DaX\AppData\Roaming\Typora\typora-user-images\1563795156992.png)

![1563795175063](C:\Users\DaX\AppData\Roaming\Typora\typora-user-images\1563795175063.png)

![1563795185329](C:\Users\DaX\AppData\Roaming\Typora\typora-user-images\1563795185329.png)

![1563795193798](C:\Users\DaX\AppData\Roaming\Typora\typora-user-images\1563795193798.png)

![1563795203641](C:\Users\DaX\AppData\Roaming\Typora\typora-user-images\1563795203641.png)

## 接口--interface

关键字:interface.  编译后字节码文件依旧是.java---.class

java7--接口包含的内容有:1. 常量 2. 抽象方法

java8--接口包含的内容有:3. 默认方法 4.静态方法

java9--接口包含的内容有:5. 私有方法

注意事项:

1.接口当中的抽象方法啊,修饰符必须是两个固定的关键字:public abstract

2.这两个关键字修饰符,可以选择性省略(省其一 或者 两个一起)

接口的使用步骤:

![1563892859282](C:\Users\DaX\AppData\Roaming\Typora\typora-user-images\1563892859282.png)

1.接口不能直接使用,必须有一个"实现类"来"实现"该接口 

格式: public class 实现类名称 implement 接口名称{	//......	}

2.接口的实现类必须覆盖重写(实现)接口中所有的抽象方法<实现:去掉abstract关键字,加上方法体大括号>

3.创建实现类的对象,进行使用.

## 接口静态方法:

注意事项:不能通过接口实现类的对象来调用接口当中的静态方法.

正确用法:通过接口名称,直接调用其中的静态方法.

格式**:接口名称.静态方法名(参数**);

1.**普通私有方法**:解决**<u>多个默认方法</u>**之间重复代码的问题.

格式:private 返回值类型 方法名称(参数列表){

​	方法体

}

2**.静态私有方法**:解决多个**<u>静态方法</u>**之间重复代码问题.

格式:private static 返回值类型 方法名称(参数列表){

​	方法体

}

#### 接口的"成员变量"—–>接口的**<u>[常量]</u>**

格式:public static final 数据类型 常量名称 = 数据值;(一旦被final修饰,说明不可改变)

注意事项:接口中的常量必须赋值,常量的名称,使用完全大写的字母,用下划线进行分割.

![1564274880259](C:\Users\DaX\AppData\Roaming\Typora\typora-user-images\1564274880259.png)

![1564283896411](C:\Users\DaX\AppData\Roaming\Typora\typora-user-images\1564283896411.png)

![1564300436510](E:\JavaSe\Demo_Picture\1564300436510.png)

![1564323733673](E:\JavaSe\Demo_Picture\1564323733673.png)

![1564370319607](E:\JavaSe\Demo_Picture\1564370319607.png)

!['[1564372694650](E:\JavaSe\Demo_Picture\1564372694650.png)

![1564457086626](E:\JavaSe\Demo_Picture\1564457086626.png)

### 多态

访问成员变量的两种方式:

1.直接通过对象名称访问成员变量,看等号左边是谁,优先用谁,没有则向上找.

2.间接通过成员方法访问成员变量,看该方法属于谁,优先用谁,没有则向上找.

在多态的代码当中,成员方法的访问规则是:

看new的是谁,就优先用谁,没有则向上找.

口诀:

成员方法:编译看左边,运行看右边;

成员变量:编译看左边,运行还看左边.

![1564621644853](E:\JavaSe\Demo_Picture\1564621644853.png)

![1564626024347](E:\JavaSe\Demo_Picture\1564626024347.png)

接口指向一个实现类就是多态==>接口--------->实现类对象

### 增强for循环:

用来遍历数组和集合;

格式:for(数组/集合类型 变量名:数组/集合名){  sout(变量名)  }

JDK1.5之前的包装类泛型



![1564815324776](E:\JavaSe\Demo_Picture\1564815324776.png)

![1564815448360](E:\JavaSe\Demo_Picture\1564815448360.png)