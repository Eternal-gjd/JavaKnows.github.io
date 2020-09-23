#### 1.String按格式拼接字符串的案例

```java
/*

题目:

定义一个方法,把数组{1,2,3}按照指定格式拼接成一个字符串,[world1*world2*world3*world4].

分析;

1.首先准备一个int[]数组;

2.定义一个方法,用来将数组变成字符串

三要素:

返回值类型:

方法名称:

参数列表

3.格式[world1*world2*world3*world4]

用到:for循环,字符串拼接,区分是否是最后一个;

4.调用方法:得到返回值(String类),打印输出调用方法中的结果

*/
public class Demo04StringPractice {
    public static void main(String[] args) {
        //创建一个数组:
        int [] array= {1,2,3,4};
        String result = ArrayToString(array);//调用方法:
        System.out.println(result);//打印输出
    }
//定义一个方法,用来将数组变成字符串:
    public static String ArrayToString(int[]array){
        String str ="[";//创建一个字符串
        //遍历数组
        for (int i = 0; i < array.length; i++) {

            if (i==array.length-1){
                str+="world"+array[i]+"]";
            }else {
                str+="world"+array[i]+"*";
            }
        }
        return str;//返回值根据方法中的数据类型
    }
}
```

#### 2.统计字符串中各个数的案例

```java
/*
题目:
键盘录入一个字符，统计字符串中大小写字母及数字字符个数
*/
public class StringTest2 {
    public static void main(String[] args) {
        //键盘录入一个字符串数据
        Scanner sc = new Scanner(System.in);
        System.out.println("请输入一个字符串数据：");
        String s = sc.nextLine();
        //定义三个统计变量，初始化值都是0
        int bigCount = 0;
        int smallCount = 0;
        int numberCount = 0;
        //遍历字符串，得到每一个字符
        for(int x=0; x<s.length(); x++) {
            char ch = s.charAt(x); 

            //拿字符进行判断
            if(ch>='A'&&ch<='Z') {
                bigCount++;
            }else if(ch>='a'&&ch<='z') {
                smallCount++;
            }else if(ch>='0'&&ch<='9') {
                numberCount++;
            }else {
                System.out.println("该字符"+ch+"非法");
            }
        }
        //输出结果
        System.out.println("大写字符："+bigCount+"个");
        System.out.println("小写字符："+smallCount+"个");
        System.out.println("数字字符："+numberCount+"个");
    }
} 
```

#### 3.数组的练习(**)

```java
package cn.it.cast.demo03;
/*
定义一个方法传入一个int类型数组，输出这个数组中每一个数字及其出现的个数
      例如 传入数组[1,2,2,2,3,3,4,4,4,4]  打印结果：
                           数字1出现了1次
                        数字2出现了3次…
 */
  public static void main(String[] args) {
        int[] arr = new int[]{1, 2, 2, 2, 3, 3, 4, 4, 4, 4, 22, 0, 0};
        int[] ele = new int[arr.length];
        ele[0] = arr[0];
        int index = 1;
        int[] count = new int[arr.length];
        count[0] = 1;

        int i;
        for(i = 1; i < arr.length; ++i) {
            int element = arr[i];
            boolean isFind = false;

            for(int j = 0; j < index; ++j) {
                if (ele[j] == element) {
                    isFind = true;
                    ++count[j];
                }
            }

            if (!isFind) {
                ele[index] = element;
                count[index] = 1;
                ++index;
            }
        }

        for(i = 0; i < index; ++i) {
            System.out.println("数字" + ele[i] + "出现了" + count[i] + "次");
        }
    }
```

#### 4.案例--生成4位网站登录验证码：

~~~java
/*
    生成网站登录验证码：
        4位
        组成包括 数字0—9  英文大写小写， 随机生成
*/

import java.util.Random;

public class CharTest {

```
public static void main(String[] args) {
    //随机数工具类
    Random r = new Random();

    for(int i=0; i<4; i++){

        int number = r.nextInt(3); //0 1 2

        switch (number){
            case 0:
                //随机生成数字0—9
                int num1 = r.nextInt(10);
                System.out.print(num1);
                break;
            case 1 :
                //随机生成英文大写 65 -90
                int num2 = r.nextInt(26)+65;
                System.out.print((char)num2);
                break;
            case 2:
                //随机生成英文小写97 -122
                int num3 = r.nextInt(26)+97;  //0~25 + 97
                System.out.print((char)num3);
                break;
        }
    }
}
```

}
~~~

#### 5.案例--手机验证码**



```java
/*
	需求：定义数组，存储6位手机验证码
		 要求验证码为随机生成的0~9之间的整数，且不能重复。
*/
public class RandomNum {
    public static void main(String[] args) {
        //静态初始化数组
       int[] arr = {-1,-1,-1,-1,-1,-1};
            
	   Random r = new Random();
    
    //定义一个记录数组索引的变量
    int index = 0;
    
    loop:while(true){
        //生成随机数
        int num = r.nextInt(10);
        
        //将num在数组中遍历,如果在数组中存在,则重新生成随机数
        for(int i=0 ;i<=index; i++){

            if( num==arr[i] ){
                continue loop;
            }
        }
        
        //如果代码能走到这里,说明num在数组中不会重复
        arr[index++] = num;  //index的范围0~5
        //index++;
        
        if(index==6){
            break;
        }
    }
    System.out.println(Arrays.toString(arr));
}
```

#### 对随机字符串进行升序排列,并倒叙打印(Arrays)

```java
//如何进行排序:public static void sort(int[] a) ：对指定的 int 型数组按数字升序进行排序。 
//必须是一个数组,才能用到Arrays.sort方法;
//String-->数组,用到toCharArray
//倒叙遍历数组:array.forr-->for(int i = array.length-1; i>=0; i--)
//正序遍历数组:array.fori-->for(int i = 0; i<array.length; i++)
```

#### 6.比较数字字符串数组是否对称

```java
(1)定义数字字符串数组{"545","6886","888","1239123","778778"}
  (2)判断该数字字符串数组中的数字字符串是否是对称
  (第一个数字和最后一个数字相等，第二个数字和倒数第二个数字是相等的，依次类推)
  逐个输出并统计个数如：545是对称的，6886是对称的，778778不是对称的。
     对称的字符串有: 3个
     分析:
    1.定义字符串数组
    2.遍历字符串数组,拿到每个字符串
    3.定义一个方法isMirror.判断字符串是否是对称的,参数字符串,返回值boolean
    4.在for循环中拿到每个字符串都去调用isMirror方法.
        4.1. 定义2个变量,一个变量用于表示小索引,一个变量用于表示大索引
        4.2. 通过这2个索引去字符串中取出这2个字符
        4.3. 判读这2个字符是否相等
        4.4. 如果不相等,表示不是对称的返回false
        4.5. 如果是相等的.小索引++,大索引--.循环再去判断
        4.6. 循环判断的条件是小索引<大索引
    5.如果是对象的字符串,数量+1
 */
public class ComparString {
    public static void main(String[] args) {
        int count = 0;
        //定义字符串数组
        String[] array = {"545","6886","888","1239123","778778"};
        //遍历字符串数组,拿到每个字符串
        for (int i = 0; i < array.length; i++) {
            String str = array[i];
            //如果字符串是对称的,那个计数器自加1
            if(cha(str)){
                count++;
                System.out.println(str+"是对称的");
            }
        }
        System.out.println("对称的字符串有"+count+"个");
    }
	//定义一个方法,来判断字符串是否对称,参数是字符串,返回值是boolean
    public static boolean cha(String str){
        boolean isMorror = true;
        //定义2个变量 ,一个变量用于表示小索引,一个用来表示大索引
        for (int start = 0,end=str.length()-1;start<end;start++,end--){
            //通过2个索引去字符串中取出这2个字符,判断是否不相等,返回结果.
            if (str.charAt(start)!=str.charAt(end)){
                return false;
            }

        }
        return isMorror;
    }
}
```

#### 7.查找指定的数据在数组中的索引

```java
/*
定义一个方法，查找指定数字在数组中出现的位置(若出现多次，多次打印)
如: 数组[1232]要查找的数是2 则方法内部会打印索引值 1 ,3
数组[1232] 要查找的数是5 则方法尾部会打印 “-1”
 */
public class TestArrayIndex {
    public static void main(String[] args) {
        int[] arr = {19,28,37,46,50};
        Scanner sc = new Scanner(System.in);
        System.out.println("请输入你要查找的元素:");
        int num = sc.nextInt();

       isFind(arr, num);
        
    }

    public static void isFind(int[] arr,int num) {
        boolean isMorror = false;
        for (int i = 0; i < arr.length; i++) {
            if (arr[i]==num){
                System.out.println("该元素的索引值为: "+i);
                isMorror=true;
            }
        }

        if (!isMorror){
            System.out.println("-1");
        }
        return ;
    }
}
```

#### 8.数组元素反转

已知一个数组 arr = {19, 28, 37, 46, 50}; 用程序实现把数组中的元素值交换，
       交换后的数组 arr = {50, 46, 37, 28, 19}; 并在控制台输出交换后的数组元素。

```java
import java.lang.reflect.Array;
import java.util.Arrays;

public class Test6Reverse {
    public static void main(String[] args) {
        int[] arr ={19,28,37,46,50};

        int temp = 0;
        for (int start = 0,end=arr.length-1; start <end ; start++,end--) {
            temp=arr[start];
            arr[start]=arr[end];
            arr[end]=temp;
        }
        String str = Arrays.toString(arr);
        System.out.println(str);
    }
}
```

#### 9.随机生成6个0~9之间的整数存放到数组中, 并且不能重复

```java
public class Test2 {

    public static void main(String[] args) {

        //定义一个数组
        //int[] arr = new int[6];//默认值都是0的话 需要不断地重头比较,改成-1 只要跟已赋值过的比较
        int[] arr = {-1,-1,-1,-1,-1,-1};

        Random r = new Random();
        //定义变量, 存储索引值
        int len = 0;

        wc:while (true){
            //不停的生成随机数
            int num = r.nextInt(10); //0-9
            //在数组里面遍历, 如果重复了, 重新生成随机数

            for(int i=0; i<=len; i++){
                if(num==arr[i]){
                    continue wc;
                }
            }

            //如果代码能走到这里, 说明num不会重复, 放进数组
            arr[len++] = num;
            //len++;

            //如果len为6时,停止循环
            if(len==6){
                break ;
            }

        }

        //打印数组
        System.out.println(Arrays.toString(arr));


    }
}
```

三. 

 长度5-12位QQ号码的每一位都是数字。 

\1. 根据键盘输入的一个字符串类型的QQ号码，判断QQ号码的合法性，要求： 

\1. 如果QQ号码长度不合法，在控制台打印输出：长度不合法（如长度不合法就不判断后续的条件） 

​        \2. 如果QQ号码中包含非数字的字符：在控制台打印输出：含非法字符 

​        \3. 如果QQ号码符合标准格式：在控制台打印输出：合法号码 

106 32 天以前个人损失保险  一个月内没有工作 (500r  重读机会) 

#### 10.身份证号码的合法判断(String)

```java
  我国的居民身份证号码，由由十七位数字本体码和一位数字校验码组成。请定义方法判断用户输入的身份证号码是否合法，并在主方法中调用方法测试结果。规则为：号码为18位，不能以数字0开头，前17位只可以是数字，最后一位可以是数字或者大写字母X。
import java.util.Scanner;

public class StringIDnumber {
    public static void main(String[] args) {
        Scanner sc = new Scanner(System.in);
        System.out.println("请输入你的身份证号码:");
        String id = sc.nextLine();
        boolean idnumber = idnumber(id);
        System.out.println("你输入的身份证号码是否合法:" + idnumber);

    }

    public static boolean idnumber(String id) {
        //号码为18位的判断&&号码第一位
        if (id.length() != 18 || id.charAt(0) == '0')
            return false;

        //前17位的判断
        for (int i = 0; i < id.length() - 1; i++) {
            char c = id.charAt(i);

            if (c < 48 || c > 57) {
                return false;
            }
        }
        //第18位的判断
        char ch = id.charAt(id.length() - 1);
        if ((ch<48||ch>57)&&ch!='X')
            return false;
        return true;
    }
}
```

## 学生管理系统

### 2.1  需求分析

- ## **案例需求**

  ​	针对目前我们的所学内容，完成一个综合案例：学生管理系统。该系统主要功能如下：

  ​	添加学生：通过键盘录入学生信息，添加到集合中

  ​	删除学生：通过键盘录入要删除学生的学号，将该学生对象从集合中删除

  ​	修改学生：通过键盘录入要修改学生的学号，将该学生对象其他信息进行修改

  ​	查看学生：将集合中的学生对象信息进行展示

  ​	退出系统：结束程序

- **实现步骤**

  1. 定义学生类，包含以下成员变量

     ​       private String sid            // 学生id

     ​       private String name       // 学生姓名

     ​       private String age          // 学生年龄

     ​       private String address   // 学生所在地

  2. 学生管理系统主界面的搭建步骤

     2.1 用输出语句完成主界面的编写
     2.2 用Scanner实现键盘输入
     2.3 用switch语句完成选择的功能
     2.4 用循环完成功能结束后再次回到主界面

  3. 学生管理系统的添加学生功能实现步骤

     3.1 定义一个方法，接收ArrayList<Student>集合
     3.2 方法内完成添加学生的功能
     ​         ①键盘录入学生信息
     ​         ②根据录入的信息创建学生对象
     ​         ③将学生对象添加到集合中
     ​         ④提示添加成功信息
     3.3 在添加学生的选项里调用添加学生的方法

  4. 学生管理系统的查看学生功能实现步骤

     4.1 定义一个方法，接收ArrayList<Student>集合
     4.2 方法内遍历集合，将学生信息进行输出
     4.3 在查看所有学生选项里调用查看学生方法

  5. 学生管理系统的删除学生功能实现步骤

     5.1 定义一个方法，接收ArrayList<Student>集合
     5.2 方法中接收要删除学生的学号
     5.3 遍历集合，获取每个学生对象
     5.4 使用学生对象的学号和录入的要删除的学号进行比较,如果相同，则将当前学生对象从集合中删除
     5.5 在删除学生选项里调用删除学生的方法

  6. 学生管理系统的修改学生功能实现步骤

     6.1 定义一个方法，接收ArrayList<Student>集合
     6.2 方法中接收要修改学生的学号
     6.3 通过键盘录入学生对象所需的信息，并创建对象
     6.4 遍历集合，获取每一个学生对象。并和录入的修改学生学号进行比较.如果相同，则使用新学生对象替换当前学生对象
     6.5 在修改学生选项里调用修改学生的方法

  7. 退出系统

     使用==System.exit(0)==退出JVM

### 2.2  需求实现

#### 2.2.1  学生类的定义

```java
/*
	学生类的成员变量（属性）：
	学号 sid
	姓名 name
	年龄 age
	居住地 address
	
	注意：为了录入信息方便，年龄也采用String类型
*/
public class Student {
    //学号
    private String sid;
    //姓名
    private String name;
    //年龄
    private String age;
    //居住地
    private String address;
	//无参构造方法
    public Student() {}
	
    //满参构造方法
    public Student(String sid, String name, String age, String address) {
        this.sid = sid;
        this.name = name;
        this.age = age;
        this.address = address;
    }
	
    //---------getter和setter方法---------
    public String getSid() {
        return sid;
    }

    public void setSid(String sid) {
        this.sid = sid;
    }

    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }

    public String getAge() {
        return age;
    }

    public void setAge(String age) {
        this.age = age;
    }

    public String getAddress() {
        return address;
    }

    public void setAddress(String address) {
        this.address = address;
    }
}
```

#### 2.2.2  主界面的搭建

```java
解决学号不存在问题
解决学号重复问题
```

```java
/*
    学生管理系统实现步骤：
        1、主界面的搭建。
        2、定义添加学生信息的方法。
        3、定义查看学生信息的方法。
        4、定义删除学生信息的方法。
        5、定义修改学生信息的方法。
	   
*/
public class StudentManager {
    
    public static void main(String[] args) {
        
        //创建集合存储学习对象信息
        ArrayList<Student> list = new ArrayList<>();
        
        //开发时, 为了方便功能演示, 可以直接新增几条测试数据, 不需要每次都录入数据
        //list.add(new Student("it001","jack","18","广州"));
		//list.add(new Student("it002","rose","20","上海"));
		//list.add(new Student("it003","andy","30","香港"));
        
        //-----------------1. 主界面的搭建开始----------------
        while (true) {
            //1. 用输出语句完成主界面的编写。
            System.out.println("--------欢迎来到学生管理系统--------");
            System.out.println("1 添加学生");
            System.out.println("2 删除学生");
            System.out.println("3 修改学生");
            System.out.println("4 查看所有学生");
            System.out.println("5 退出");
            System.out.println("请输入你的选择：");

            //2. 用Scanner实现键盘录入数据。
            Scanner sc = new Scanner(System.in);
            String line = sc.nextLine();

            //3. 用switch语句完成操作的选择。
            switch (line) {
                case "1": //添加
                    addStudent(list);
                    //System.out.println("添加学生");
                    break;
                case "2": //删除
                    deleteStudent(list);
                    //System.out.println("删除学生");
                    break;
                case "3": //修改
                    updateStudent(list);
                   //System.out.println("修改学生");
                    break;
                case "4": //查看
                    findAllStudent(list);
                    //System.out.println("查看所有学生");
                    break;
                case "5": //退出
                    System.out.println("谢谢使用");
                    System.exit(0); //JVM退出
            }
        }
        //-----------------主界面的搭建完成----------------
    }
    
    /*
        2. 定义添加学生信息的方法。
    */
    public static void addStudent(ArrayList<Student> array) {

        //创建键盘录入对象
        Scanner sc = new Scanner(System.in);
        
		//定义学生学号
         String sid = "";
        
		//判断学号是否重复，如果重复重新录入
        while (true) {
            System.out.println("请输入学生学号：");
            sid = sc.nextLine();

            boolean flag = isUsed(array, sid); //如果返回true, 表示学号重复
            if (flag) {
                System.out.println("你输入的学号已经被占用，请重新输入");
            } else {
                break;
            }
        }
		
        
        System.out.println("请输入学生姓名：");
        String name = sc.nextLine();

        System.out.println("请输入学生年龄：");
        String age = sc.nextLine();

        System.out.println("请输入学生居住地：");
        String address = sc.nextLine();

        //创建学生对象，并为成员变量赋值
        Student s = new Student();
        s.setSid(sid);
        s.setName(name);
        s.setAge(age);
        s.setAddress(address);

        //将学生对象添加到集合中
        array.add(s);

        //给出添加成功提示
        System.out.println("添加学生成功");
    }
    
    /*
    	3. 定义查看学生信息的方法。
    */
    public static void findAllStudent(ArrayList<Student> list) {
        //判断集合中是否有数据，如果没有显示提示信息
        if (list==null || list.size() == 0) {
            System.out.println("无信息，请先添加信息再查询");
            //方法结束
            return;
        }

        //显示表头信息
        //\t其实是一个tab键的位置
        System.out.println("学号\t\t姓名\t\t年龄\t\t居住地");

        //将集合中数据取出，按表头格式对应输出
        for (int i = 0; i < list.size(); i++) {
            Student s = list.get(i);
            System.out.println(s.getSid() + "\t\t" 
                               + s.getName() + "\t"
                               + s.getAge() + "岁\t\t" 
                               + s.getAddress());
        }
    }
    
    
    /*
    	4. 定义删除学生信息的方法。
    */
    public static void deleteStudent(ArrayList<Student> array) {
        //键盘录入要删除的学生学号,显示提示信息
        Scanner sc = new Scanner(System.in);

        System.out.println("请输入你要删除的学生的学号：");
        String sid = sc.nextLine();

        //在删除/修改学生操作前，对学号是否存在进行判断
        //如果不存在，显示提示信息
        //如果存在，执行删除/修改操作

        //假设sid不存在
        boolean noSid = true;

        for (int i = 0; i < array.size(); i++) {
            Student s = array.get(i);
            if (s.getSid().equals(sid)) {
                array.remove(s);
                System.out.println("删除学生成功");
                noSid = false;
                break;
            }
        }

        if (noSid) {
            System.out.println("该信息不存在，请重新输入");
        } 
    }
    
    
    /*
    	5. 定义修改学生信息的方法。
    */
    public static void updateStudent(ArrayList<Student> array) {
        //键盘录入要修改的学生学号，显示提示信息
        Scanner sc = new Scanner(System.in);

        System.out.println("请输入你要修改的学生的学号：");
        String sid = sc.nextLine();
        
        //假设sid不存在
        boolean noSid = true;

        //遍历集合修改对应的学生信息
        for (int i = 0; i < array.size(); i++) {
            Student s = array.get(i);
            if (s.getSid().equals(sid)) {

                //键盘录入要修改的学生信息
                System.out.println("请输入学生新姓名：");
                String name = sc.nextLine();
                System.out.println("请输入学生新年龄：");
                String age = sc.nextLine();
                System.out.println("请输入学生新居住地：");
                String address = sc.nextLine();

                s.setSid(sid);
                s.setName(name);
                s.setAge(age);
                s.setAddress(address);
                //给出修改成功提示
                System.out.println("修改学生成功");
                noSid = false;
                break;
            }
        }
        if(noSid){
            System.out.println("你输入的学号不存在，请重新输入！");
        }
    }

    
    /*
    	6. 定义方法判断学号是否重复。
    */
    public static boolean isUsed(ArrayList<Student> array, String sid) {
        //如果键盘录入的学号已经存在，返回true，否则返回false
        boolean flag = false;

        for(int i=0; i<array.size(); i++) {
            Student s = array.get(i);
            if(s.getSid().equals(sid)) {
                flag = true;
                break;
            }
        }

        return flag;
    }
    
}
```

## 统计录入字符串中每个字符串出现的次数

### 需求分析

```java
 "aaaabbbccceeeee"
        Map ==> 映射：一个对一个
        a=4
        b=3
        c=3
        e=5

        Map<Character,Integer> map;
        map.put("a",4);
        map.put("b",2);


    步骤分析：
        1. 创建键盘录入对象：Scanner
        2. 接收用户输入的字符串：String
        3. 创建Map集合存储字符与其出现次数的对应关系：Map<Character,Integer> map;
        4. 遍历字符串获得每一个字符：就是map集合的键
        5. 根据字符获得次数
            5.1 如果没有获取到，则代表字符是第1次出现，则添加键值对到map集合
            5.2 如果获得到，则将次数加一更新到Map集合中
        6. 遍历Map集合输出键和值
```

### 实现需求

```java
public class Demo_Count {
    public static void main(String[] args) {
        // 1. 创建键盘录入对象：Scanner
        Scanner sc = new Scanner(System.in);
        // 2. 接收用户输入的字符串：String
        System.out.println("请输入一个字符串...");
        String line = sc.nextLine();
        // 3. 创建Map集合存储字符与其出现次数的对应关系：Map<Character,Integer> map;
        Map<Character,Integer> map = new LinkedHashMap<>();
        // 4. 遍历字符串
        for (int i = 0; i < line.length(); i++) { // aabc
            // 根据索引获得字符:就是map集合的键
            char ch = line.charAt(i); // b
            // 将字符作为键到map集合中获得值：字符出现次数
            Integer count = map.get(ch); // null
            // 判断count是否有值
            if (count != null) {
                map.put(ch, count + 1); // {a=2}
            } else {
                // 第1次出现：添加键值对到map集合
                map.put(ch, 1); // {a=1,b=1}
            }
        }
        // 6. 遍历Map集合输出键和值
        Set<Character> keySet = map.keySet();
        for(Character key:keySet){
            System.out.println(key+"="+map.get(key));
        }
    }
}
```



## 动态代理示例演示

   *    * ```java
             1 现有一个数据访问层类(StudentDao)
             
             - 类中提供了对学生进行增删改查的方法。
             
             2 现有一个业务层类(StudentService)
             
             - 该类需要调用StudentDao类中定义好的方法对学生进行增删改查操作。
             
             3 要求：在调用StudentDao方法之前要先判断用户是否有权限
             
             - 如果有权限则允许调用StudentDao方法，并在调用完StudentDao方法之后要记录日志。
               如果没有权限则不允许调用StudentDao方法。
             - 不允许修改StudentDao类中代码实现以上需求。
             
             
             动态代理的开发步骤小结：
                         1. 明确要代理的功能(方法)有哪些？
                         2. 将要代理的方法定义在接口中
                         3. 真实对象需要实现接口重写接口中的方法
                         4. 创建真实对象：不能直接通过真实对象调用方法了
                         5. 通过Proxy工具类的静态方法创建代理对象：需要传递以下三个参数：
                             参数1：真实对象的类加载器
                             参数2：真实对象实现的接口Class数组
                             参数3：回调处理对象：InvocationHandler接口实现类对象
                                 创建InvocationHandler实现类对象并重写invoke方法
                         6. 在invoke方法中根据需求对代理方法进行拦截，
                             根据需求决定是否应该调用真实对象的方法
             ```

             ```java
             /**
              * 对学生进行增删改查操作的类：数据访问层，直接跟数据库打交道
              * @author pkxing
              */
             public class StudentDao implements Dao<Student>{
             
                 @Override
                 public void save(Student t) {
                     System.out.println("增加学生信息:" + t);
                 }
             
                 @Override
                 public void delete(Student t) {
                     System.out.println("删除学生信息:" + t);
                 }
             
                 @Override
                 public void update(Student t) {
                     System.out.println("更新学生信息:" + t);
                 }
             
                 @Override
                 public Student find(int id) {
                     System.out.println("查询学生信息");
                     Student s =  new Student();
                     System.out.println(s);
                     return s;
                 }
             }
             
             
             
             package com.itheima._16动态代理演示;
             
             import java.lang.reflect.InvocationHandler;
             import java.lang.reflect.Method;
             import java.lang.reflect.Proxy;
             
             // 业务层调用数据访问层
             public class StudentService {
                 public static void main(String[] args) {
                     // 创建数据访问层对象：真实对象
                     StudentDao stuDao = new StudentDao();
             
                     // 创建代理对象
                     Dao proxy = (Dao) Proxy.newProxyInstance(
                             stuDao.getClass().getClassLoader(), // 类加载器对象
                             new Class[]{Dao.class}, // 真实对象实现的接口
                             new InvocationHandler() {
             
                                 // 检查是否有权限：有返回true，否则false
                                 public boolean check(){
                                     System.out.println("检查权限...");
                                     return true;
                                 }
                                 // 记录日志信息
                                 public void log(){
                                     System.out.println("记录日志信息...");
                                 }
             
                                 @Override
                                 public Object invoke(Object proxy, Method method, Object[] args) throws Throwable {
                                      // 检查是否有权限
                                     if (check()){
                                         // 有则调用真实对象的方法
                                         Object result = method.invoke(stuDao, args);
                                         // 记录日志
                                         log();
                                         return result;
                                     }
                                     return null;
                                 }
                             }
                     );
                     // 创建学生对象
                     Student stu = new Student();
                     // 对学生进行增删改查：调用代理对象方法
                     proxy.save(stu);
                  Student s = (Student) proxy.find(1);
                     proxy.delete(stu);
                     proxy.update(stu);
             }
             }
             
             
             
             public class Demo161 {
                 public static void main(String[] args) {
                     // 创建Set对象：真实对象
                     Set<String> set = new HashSet<>();
             
                     // 创建代理对象
                     Set proxy = (Set) Proxy.newProxyInstance(
                             set.getClass().getClassLoader(),   // 类加载器对象
                             new Class[]{Set.class},// 真实对象实现的接口
                             new InvocationHandler() {  // 回调处理对象
                                 // invoke调用时机：每当通过代理对象调用方法时都会被该方法拦截
                                 // 参数1：proxy 代理对象
                                 // 参数2：method 代理对象的调用的方法：拦截到的方法
                                 // 参数3：args 代理对象调用方法时传递的参数
                                 // 返回值：调用真实对象方法的返回值
                                 @Override
                                 public Object invoke(Object proxy, Method method, Object[] args) throws Throwable {
                                     // [aaa]
                                     String str = (String) args[0]; // aaaa
                                     // 将元素添加真实对象set上
                                     boolean b = set.add(str);
                                     System.out.println("添加成功");
                                     return b;
                                 }
                             }
                     );
             
                     // 通过代理对象调用方法添加元素
                     proxy.add("aaa"); // 添加成功
                     proxy.add("bbb"); // 添加失败
             
                     System.out.println(set);
                 }
             }
             
             ```

             