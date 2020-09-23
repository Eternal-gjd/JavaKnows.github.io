### 3-1 Random练习-猜数字-无限循环（应用）

- 需求：

  程序自动生成一个1-100之间的数字，使用程序实现猜出这个数字是多少？

  当猜错的时候根据不同情况给出相应的提示

  A. 如果猜的数字比真实数字大，提示你猜的数据大了

  B. 如果猜的数字比真实数字小，提示你猜的数据小了

  C. 如果猜的数字与真实数字相等，提示恭喜你猜中了

- 示例代码：

```java
package cn.it.cast.demo03;

import java.util.Random;
import java.util.Scanner;

public class Demo01WhiletrueDemo {
    public static void main(String[] args) {
        //要完成猜数字的游戏，首先需要有一个要猜的数字，使用随机数生成该数字，范围1到100
        Random r = new Random();
        int number = r.nextInt(100) + 1;

        while(true) {
            //使用程序实现猜数字，每次均要输入猜测的数字值，需要使用键盘录入实现
            Scanner sc = new Scanner(System.in);

            System.out.println("请输入你要猜的数字：");
            int guessNumber = sc.nextInt();

            //比较输入的数字和系统产生的数据，需要使用分支语句。
            //这里使用if..else..if..格式，根据不同情况进行猜测结果显示
            if(guessNumber > number) {
                System.out.println("你猜的数字" + guessNumber + "大了");
            } else if(guessNumber < number) {
                System.out.println("你猜的数字" + guessNumber + "小了");
            } else {
                System.out.println("恭喜你猜中了");
                break;
            }
        }
    }
}1563267221151](C:\Users\DaX\AppData\Roaming\Typora\typora-user-images\1563267221151.png)
```

### While(true)-案例

注意:

Java中break和continue可以跳出指定循环，break和continue之后不加任何循环名则默认跳出其所在的循环，在其后加指定循环名，则可以跳出该指定循环（指定循环一般为循环嵌套的外循环）。

```java
public class Demo01WhiletrueDemo {
    public static void main(String[] args) {
        //要完成猜数字的游戏，首先需要有一个要猜的数字，使用随机数生成该数字，范围1到100
        Random r = new Random();
        int number = r.nextInt(100) + 1;

        loop:while(true) {
            //使用程序实现猜数字，每次均要输入猜测的数字值，需要使用键盘录入实现
            Scanner sc = new Scanner(System.in);

            System.out.println("请输入你要猜的数字：");
            int guessNumber = sc.nextInt();

            //比较输入的数字和系统产生的数据，需要使用分支语句。
            //这里使用if..else..if..格式，根据不同情况进行猜测结果显示
            if(guessNumber > number) {
                System.out.println("你猜的数字" + guessNumber + "大了");
            } else if(guessNumber < number) {
                System.out.println("你猜的数字" + guessNumber + "小了");
            } else {
                System.out.println("恭喜你猜中了");
                break loop;//可以指定停止while循环,需要在while加指定循环名
            }
        }
    }
}
```