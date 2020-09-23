# JavaEE_Practice Knowledge Point

## JQuery

### 1.实现表单的复选框全选或者全不选

```html
<script>
    //"all"为<th><input type="checkbox" id="all"></th>
    //"item"为每一行的 <th><input type="checkbox" class="item"></th>
$("#all").click(function () {
               // 获得所有复选框元素对象并设置选择状态
               $(".item").prop("checked",this.checked);

               // $(".item").prop("checked",$(this).prop("checked"));
           });
</script>
```

### 2.购物车的删除功能

```html
//删除功能
<script>
    //"deleteRow"为删除按钮的点击事件 
    //<td><input type="button" value="删除"onclick="deleteRow(this)"></td>
	function deleteRow(deleteBtn) {

            if (confirm("你确定要删除该行记录吗?")){
                // deleteRow：js对象
                // deleteBtn.parentNode.parentNode.remove();

                // $(deleteRow)：jq对象
                // jq对象.parent()：获得父元素对象
                $(deleteBtn).parent().parent().remove();
            }
        }
    </script>
```

### 图片抽奖程序

####  案例需求

1. 当用户点击开始按钮时，小像框中的像片快速切换。
2. 当用户点击停止按钮时，小像框中的像片停止切换，大像框中淡入显示与小像框相同的像片。

```
1. 将所有的图片地址保存在一个数组中，定义三个全局变量：总计数num，数组长度total，计时器timer
2. 一开始让停止按钮不可用
3. 点开始按钮调用方法imgStart()，点击后让开始按钮不可用，停止按钮可用。
4. 每过25毫秒调用一次切换函数imgChange()，不停的替换小图片中的src地址。
5. 每次替换图片计数总数加1，数组下标计算方法：数组下标=计数总数%数组的长度
6. 点结束按钮调用方法imgStop()，让结束按钮不可用，让开始按钮可用。
7. imgStop()清除计时器，将小图片中的地址赋值给大图片的地址，先隐藏，再并且淡入显示图片
```

```html
<!DOCTYPE html>
<html lang="zh-CH">
<head>
    <meta charset="UTF-8">
    <title>图片抽奖</title>
    <script src="js/jquery-3.3.1.js"></script>
    <style type="text/css">
        #small {
            border: 1px solid blueviolet;
            width: 75px;
            height: 75px;
            margin-bottom: 20px;
        }

        #smallPhoto {
            width: 75px;
            height: 75px;
        }

        #big {
            border: 2px solid orangered;
            width: 400px;
            height: 400px;
            position: absolute;
            left: 300px;
            top: 10px
        }

        #bigPhoto {
            width: 400px;
            height: 400px;
        }

        #btnStart {
            width: 100px;
            height: 100px;
            font-size: 22px;
        }

        #btnStop {
            width: 100px;
            height: 100px;
            font-size: 22px;
        }
    </style>
</head>
<body>
<!-- 小像框 -->
<div id="small">
    <img id="smallPhoto" src="img/man00.jpg"/>
</div>

<!-- 大像框 -->
<div id="big">
    <img id="bigPhoto" src="img/man00.jpg"/>
</div>
<!-- 开始按钮 -->
<input id="btnStart" type="button" value="点击开始">
<!-- 停止按钮 -->
<input id="btnStop" type="button" value="点击停止">

<script type="text/javascript">
    // 准备一个一维数组，用户的图片的路径
    var imgs = [
        "img/man00.jpg",
        "img/man01.jpg",
        "img/man02.jpg",
        "img/man03.jpg",
        "img/man04.jpg",
        "img/man05.jpg",
        "img/man06.jpg"
    ];

    /**
     1. 网页加载完毕设置按钮的状态

     2. 监听开始按钮的点击事件
     2.1 设置开始和停止按钮的可点状态
     2.2 开始定时器：每隔0.2秒切换小图片


     3. 监听停止按钮的点击事件
     3.1 停止定时器
     3.2 将小相框的图片显示在大相框上
     */

        // 定义变量记录当前显示的图片索引
    var index = 0 ;


    // 定义变量接收定时器id
    var task;

    // 1. 网页加载完毕设置按钮的状态
    $("#btnStop").prop("disabled",false);

    //  2. 监听开始按钮的点击事件
    $("#btnStart").click(function () {
        // 2.1 设置开始和停止按钮的可点状态
        $("#btnStart").prop("disabled",true);
        $("#btnStop").prop("disabled",false);


        // 2.2 开始定时器：每隔0.2秒切换小图片
        task = setInterval(imgStart,100);
    })



    //  3. 监听停止按钮的点击事件
    $("#btnStop").click(function () {
        // 3.0 设置开始和停止按钮的可点状态
        $("#btnStart").prop("disabled",false);
        $("#btnStop").prop("disabled",true);
        // 3.1 停止定时器
        clearInterval(task);
        // 3.2 将小相框的图片显示在大相框上
        $("#bigPhoto").attr("src",$("#smallPhoto").attr("src"))
        // 3.3 先隐藏大相框图片
        $("#bigPhoto").hide()
        // 3.4 然后以动画形式慢慢显示出来
        $("#bigPhoto").show(1000);
    })

    // 每隔0.2秒切换小图片
    function imgStart() {
        // 索引值加一
        index++; // 100
        // 计算图片真正的索引值
        var i = index % imgs.length; // [0,6]
        // 根据i获得图片路径，并设置的小图片img的src属性上
        $("#smallPhoto").attr("src",imgs[i])
    }
</script>
</body>
</html>
```
## 关于相对路径和绝对路径
```java
 /**
         * 关于相对路径和绝对路径：
         *      当前路径：
         *           http://127.0.0.1:8080/project/user/login.do
         *
         *      相对路径：
         *          不加上斜杠，是相对路径。相对当前的路径。下一步的请求路径：
         *              http://127.0.0.1:8080/project/user/+目标url
         *
         *      绝对路径：
         *          加上斜杠，是绝对路径。与当前路径没有任何关系。下一步请求路径：
         *              http://127.0.0.1:8080/project/+目标url
         *
         *      最佳实践：
         *          1.原则上来说，相对路径和绝对路径都可以实现跳转
         *          2.在同一个资源内部操作的时候，建议使用相对路径
         *          3.在跨资源之间操作的时候，建议使用绝对路径
         */
```



## 3.字节流和字符流的区别

1.字节流读取的时候，读到一个字节就返回一个字节； 字符流使用了字节流读到一个或多个字节（中文对应的字节数是两个，在UTF-8码表中是3个字节）时。先去查指定的编码表，将查到的字符返回。 
2.字节流可以处理所有类型数据，如：图片，MP3，AVI视频文件，而字符流只能处理字符数据。只要是处理纯文本数据，就要优先考虑使用字符流，除此之外都用字节流。

```properties
注:
a.高效流不能直接对文件进行操作,必须通过普通流操作.高效流是从输入流中读取文本，缓冲各个字符，从而实现字符、数组和行的高效读取。 可以指定缓冲区的大小，或者可使用默认的大小。大多数情况下，默认值就足够大了
b.字节流在操作的时候本身是不会用到缓冲区（内存）的，是与文件本身直接操作的，而字符流在操作的时候是使用到缓冲区的,并且可以使用flush方法强制进行刷新缓冲区
```

