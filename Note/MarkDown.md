# Bash/Shell学习笔记

## Bash脚本的声明

在我们写.sh文件时要在文档头部做出`#!/bin/bash`的声明。

```bash
    #!/bin/bash
    #
    echo "Hello World"
    ……
    #
```

如上所示，写出声明后我们的bash脚本才可以正常的运行。
其实第一行`#!/bin/bash`写错或者不写时，系统会有一个默认的解释器进行解释，此处为bash，但是为了标准化我们要加上它！
笔记其余的地方我都会省略这个声明，但是在读者写代码时记得要在你自己写的代码里加上这段声明~

## 变量

### 定义变量

* `var=Hello World!` 就是这么简单就声明了一个全局变量。
* `varnum=32` 但是要注意的是:这样直接声明的变量不论存入的数据是什么类型，都会当做字符串对待。
* 例如：

 ```bash
 varnum=10
 varnum2=$varnum + 10
 echo $varnum2
输出：10 + 10
 ```

可以看出，直接对变量进行增加或进行数学运算是不行的，因为直接声明的变量的类型就是字符串！如果想对变量进行数学运算那么你可能需要`declare`来帮助你给定义好的变量加上它们应有的“变量类型”

### 定义有类型的变量

* `declare [+/-][rxi][变量名称＝设置值]`
    * 参数说明：
    * +/-：+ 为取消相应的属性 - 为添加相应的属性
    * rxi：r 为只读变量（常量） x 为环境常量(在Bash脚本中也可以使用外面的定义的变量) i 为数值类型
* 例如：

    ```bash
    declare -i numsum
    numtest=30
    numsum=$numtest+10
    echo $numsum
    输出：40
    ```

可以看出如果我们给numsum这个变量设置好了他的类型，那么在输出的时候就会根据类型对其进行输出。
`declare`也可以更改已经声明好的变量的类型，不是必须要通过`declare`声明的变量才有效果。

* 例如：

    ```bash
    var=300
    declare -i var
    ```

也可以将var变量设置为数值型。
**注意：假如上面的var这个变量值为文本型或不是数值型或数学表达式的话会将其原有的变量值替换为0，而且无法找回原来的变量内容！**

### 变量的删除

* `${variable#[匹配符]}` 从这个变量的**前面**开始删除存储的信息，删除符合匹配条件的**第一个**。
* `${variable##[匹配符]}` 从这个变量的**前面**开始删除存储的信息，删除符合匹配条件的**最长匹配**
* `${variable%[匹配符]}` 从这个变量的**后面**开始删除存储的信息，删除符合匹配条件的**第一个**
* `${variable%%[匹配符]}` 从这个变量的**后面**开始删除存储的信息，删除符合匹配条件的**最长匹配**

### 变量的替换

* `var1=${variable/要替换的内容/替换为}` 从这个变量前面仅寻找要替换的**第一个**内容，之后替换为新的内容并赋值到一个新变量上。
* `var2=${variable//要替换的内容/替换为}` 从这个变量前面寻找要替换的**全部**内容，之后替换为新的内容并赋值到一个新变量上。


```bash
var1=${PATH/bin/Bin}
echo $var1
    输出  /usr/local/Bin:/usr/bin:/bin:/usr/sbin:/sbin:/Applications/VMware Fusion.app/Contents/Public:/usr/local/mysql/bin
var2=${PATH//bin/Bin}
echo $var2
    输出  /usr/local/Bin:/usr/Bin:/Bin:/usr/sBin:/sBin:/Applications/VMware Fusion.app/Contents/Public:/usr/local/mysql/Bin
```

### 局部变量和全局变量

* 不做特殊声明，Shell中变量都是全局变量
  
#### 局部变量

* 定义局部变量时，使用local关键字
* 如果函数内与函数外变量名重复，那么在函数内会覆盖函数外的全局变量。
* 局部变量使用`local`关键字声明
* `local var_local="Hello World"`
* 例子：

    ```bash
    var1="Hello World"
    function new
    {
            local var2="Local Var"
            var3="Public Var"
    }
    # 调用函数
    new
    echo var1=$var1
    echo var2=$var2
    echo var3=$var3
        输出
        var1=Hello World
        var2=
        var3=Public Var
    ```

    通过例子可以看出用`local`关键字声明出的var2变量在其所在函数外是无法使用的。

### 变量的测试

很少会用到，一般来说我们使用if-else来进行替换变量的测试，只不过if-else麻烦一些。变量的检查一般用于初始化变量。
![变量的测试](https://blog.yeefire.com/usr/uploads/2019/07/2996501335.jpg)

```bash
str1=test1
var1=${str1-default}
echo $var1
    test1
var2=${xxx-default}
echo $var2
    default
```

## 字符串的处理

### 计算字符串的长度

* `echo ${#varstr}` 输出varstr字符串变量的字符长度

```bash
varstr=teststring-HelloWorld!
echo ${#varstr}
    输出  22
```

### 抽取子串

* `${varstr:num1}` 截取从 num1 开始到末尾的所有字符
* `${varstr:num1:num2}` 从 num1 开始截取之后的 num2 个字符
* `${varstr: -num1}` 从**末尾向前**截取从倒数 num1 个字符开始到末尾的所有字符（**注意冒号后有空格**）
* `${varstr: -num1:-num2}` 从**末尾向前**截取 num2 到 num1 之间的所有字符（**注意 -num1 要小于 -num2，下面有例子方便理解）**
* `${varstr: -num1:num2}` 从末尾倒数第num1个字符开始向后截取 num2 个字符
* `${varstr:num1:-num2}` 从开头第 num1 个的字符截取到倒数第 num2 个字符

**注意：以这种方式抽取字符串索引是从0开始的，上面所提到的所有数字都是以索引的形式体现。所有符号都是英文，并且在num为负值时在冒号（:）的后面要有一个空格**

```bash
varstr=Hello,World!
echo ${varstr:3}
    输出 lo,World!
echo ${varstr:3:6}
    输出 lo,Wor
echo ${varstr: -6}
    输出 World!
echo ${varstr: -6:-3}
    输出 Wor
echo ${varstr:0:-7}
    输出 Hello
```

## 命令替换和数学运算

### 命令替换


|        | 实现 | 备注                     |
|--------|------|--------------------------|
| 方法一 | \$() |                          |
| 方法二 | ``   | 注意是反引号，不是单引号 |

在上面已经用到了命令替换，简单来说在用echo输出的时候如果使用命令替换，就会先执行命令，之后把返回值再输出回来。

* 比如：获得今年是第几年
* `echo 今年是第 $(date +%Y) 年`
    * 输出 今年是第 2019 年

### 数学计算

因为数学计算和命令替换的bash语法很相近，所以一起学习效果会好些吧。

#### 1、**用$(( ))来表达数学计算,和命令替换一起使用时要注意括号的个数**

* 比如：获得明年的年数
* `echo 明年是第 $(( $(date +%Y) +1))年`
    * 输出 明年是第 2020年

#### 2、**使用bc来处理浮点型数学运算**

* 比如：
* `echo "3.14*3.14" | bc`
  * 输出：9.85 （黑人问号？不对啊，怎么精度只有2位？）
  
**注意咯，使用bc时默认保留小数点两位，如果可以整除的时候这两位也给你省去了！如果想保留更高精度的数，你需要使用`scale`。**

**浮点数的小数位数是由内建变量scale控制的。必须将这个值设置为你希望在计算结果中保留的小数位，否则无法得到期望的结果**
* 比如:
* `echo "scale=6;3.14*3.14" | bc `
  * 输出：9.8596
  * 精度设置为小数点后6位，但是后两位位是0，所以隐藏掉了。

## 函数

### 定义函数

  定义函数有两种方法：

```bash
  #第一种
  function f1
  {
      echo "这是第一种定义函数的方法"
      echo "传入参数$1"
  }
  #第二种
  f2()
  {
      echo "这是第二种定义函数的方法"
      echo "传入参数$1"
  }
  #调用f1函数并传参
  f1 "lalalal"
  #调用f2函数并传参
  f2 "lalallalal"
    输出：
        这是第一种定义函数的方法
        传入参数lalalal
        这是第二种定义函数的方法
        传入参数lalallalal
```

### 函数的返回值

函数有两种返回值的方法

| 代码   | 备注                                                                            |
|:-------|:--------------------------------------------------------------------------------|
| return | `return通常用来返回执行状态！执行成功返回 0 执行失败返回 1 ，返回值范围(0~255)` |
| echo   | `echo通常用来返回字符串或一个列表`                                              |

### 函数库

* 使用函数库，我们可以避免对一段代码重复的书写，我们可以将常有的一些方法封装到函数库中。
* 写好的函数库一般不直接执行，而是由其他的脚本进行调用。

例如现在要定义一个函数库，要满足以下几个函数：

* 1、加法函数add
* 2、减法函数reduce
* 3、乘法函数multiple
* 4、除法函数divide
* 5、打印系统的运行情况的函数sys_info,该函数用来显示内存运行情况。

  ```bash
    function add
    {
        echo $(($1+$2))
    }
    function reduce
    {
        echo $(($1-$2))
    }
    function multiple
    {
        echo $(($1\*$2))
    }
    function divide
    {
        echo $(($1/$2))
    }
    function sys_info
    {
        echo "内存的情况："
        echo `free -m`
        echo "================================="
        echo "硬盘剩余空间："
        echo `df -h`
    }
  ```

**OK,我们写好了一个函数库，把它命名为lib_test。现在需要来写一个普通脚本去调用我们的函数库。**

```bash
#!/bin/bash
#

#我们用一个.表示要调用函数库，空格后面是函数库的位置。我这里的函数库和脚本文件在同级目录，所以用./lib_test来访问函数库
. ./lib_test

add 1 3
reduce 5 2
multiple 5 2
divide 9 3
sys_info

```

## 条件、判断、分支

### if判断

```bash
if [ 条件语句 ];then
    满足条件执行的内容
else if [ 条件语句 ];then
    第一个if条件不满足，但第二个条件满足执行的内容
else
    都不满足条件后执行内容
fi
```

* bash/shell 对空格非常敏感
* 如果按照下面的例子练习if语句时运行报错那么请耐心仔细检查空格或语法。

**现在我们通过一个实例来学习if**

要求：

* 1、检测nginx服务是否正在运行
* 2、如果检测到nginx服务已经宕掉，那么重新启动它！
* 3、在测试的时候先把nginx服务手动停止`service nginx stop`
  
```bash
#!/bin/bash
#
if [ `ps -ef | grep nginx | grep -v grep | wc -l` -eq 0 ];then
    #检查nginx进程，如果没有进程的话(grep与wc筛选后没有任何内容)，那么就启动它吧！
    echo "Nginx服务已宕掉！"
    service nginx start
    echo "Nginx已经重新启动！"
else
    echo "Nginx服务正常"
fi

    输出：
    Nginx服务已宕掉！
    Starting nginx...  done
    Nginx已经重新启动
```

### while循环

刚才学习了if条件判断，我们可以来监测nginx服务是否宕掉，但是它需要我们每次都执行它这个脚本才能够去检测nginx。那么如果知道了while循环，是不是就可以循环去监测它了？

```bash
while 条件语句
do
    满足条件执行的内容
done
```

例子要求：那么我们在if的例子基础上来完善一下它，让它每隔1分钟就检测一下Nginx服务

```bash
while true
do
    if [ `ps -ef | grep nginx | grep -v grep | wc -l` -eq 0 ];then
        #检查nginx进程，如果没有进程的话(grep与wc筛选后没有任何内容)，那么就启动它吧！
        echo "Nginx服务已宕掉！"
        service nginx start
        echo "Nginx已经重新启动！"
    else
        echo "Nginx服务正常"
    fi
    # 60秒后再次执行循环体
    sleep 60
done
```

我们使用while-true死循环来重复监测Nginx服务，每次循环过后都休眠60s后再次执行循环体！

## sed 流编辑器

* `第一种形式：stdout | sed [option] "pattern command"`
* `第二种形式：sed [option] "pattern command" fileaddress`

### 简介：

> Linux sed 命令是利用脚本来处理文本文件。
sed 可依照脚本的指令来处理、编辑文本文件。
sed 主要用来自动编辑一个或多个文件、简化对文件的反复操作、编写转换程序等。

### 选项 option(常用)

| 选项 | 含义                                     |
|------|:------------------------------------------|
| -n   | 只打印模式匹配行                         |
| -e   | 直接在命令行进行sed编辑，默认选项        |
| -f   | 编辑的操作方式保存在文件中，指定文件执行 |
| -r   | 支持扩展正则表达式                       |
| -i   | 直接修改文件内容（将所作的修改保存到文件中）  |

**需要注意的是sed在处理文本时默认每一行都会输出出来，如果只想打印与自己设置好的匹行，需要选项中使用`-n`**

```bash
# 我们先创建一个用来测试的文本
    echo "I Like python \nI Like Python \nI Like PYTHON" > text.txt
# 现在我们直接使用sed来输出text.txt文件里面的内容
    sed n text.txt
        输出：
            I Like python
            I Like Python
            I Like PYTHON
# 现在我们来进行过滤，我们只要小写的python匹配行
    sed '/python/p' text.txt
        输出：
        I Like python
        I Like python
        I Like Python
        I Like PYTHON
        你会发现为什么I Like python会输出两行，而且还把其他的输出出来了？
        需要注意的是sed在处理文本时默认每一行都会输出出来，如果只想打印与自己设置好的匹行，需要选项中使用`-n`
# 那么我们加上 -n 选项再试试
    sed -n '/python/p' text.txt
        输出：
        I Like python
        这回没有问题了，去掉非匹配行，留下的就是我们想要的！
# 如果我既要 python也要PYTHON?
    sed -n -e '/python/p' -e '/PYTHON/p' text.txt
        输出：
        I Like python
        I Like PYTHON
        使用 -e 可以连接两个或多个匹配规则
```

### sed 的匹配模式

|匹配模式|作用|注意|
|--------|-------|--------|
|`8p`|打印第8行的内容||
|`8,10p`|打印第8行到第10行的内容|包含第8行和第10行|
|`8,+5p`|打印第8行和其之后的5行|包含第8行|
|`/正则表达式/p`|打印匹配正则表达式的行|
|`/正则表达式_1/,/正则表达式_2/p`|打印匹配正则表达式1和2之间的行|
|`8,/正则表达式/p`|打印第8行起直到匹配到正则表达式匹配行之间的所有内容|
|`/正则表达式/,8p`|打印从匹配到正则表达式开始到第8行之间的所有内容|

### sed 的增删改查

|功能|指令|备注|
|--------|------|-------|
|查|`p`|打印匹配的内容|
|删|`d`|删除匹配的内容|
|增|`a`|在匹配行的后一行追加内容|
|增|`i`|在匹配行的前一行增加一行内容|
|改|`s/匹配模式/要修改的内容/`|查找符合匹配模式的字符串，将其匹配到的**第一个**字符串进行替换|
|改|`s/匹配模式/要修改的内容/g`|查找符合匹配模式的字符串，将匹配到的**所有**的字符串都进行替换|
|改|`s/匹配模式/要修改的内容/2g`|只替换从第二个符合匹配模式后面的所有字符串|
|改|`s/匹配模式/要修改的内容/ig`|忽略大小写查找符合匹配模式的字符串，将匹配到的所有字符串都进行替换|

### sed 的显示行号

* `=` 可以显示输出的行号

例如：
`sed = /etc/passwd`
