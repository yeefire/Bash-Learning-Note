# Bash学习笔记

## 变量

### 定义变量
* `var=Hello World!` 就是这么简单就声明了一个变量。
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
## 变量的测试
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


|  | 实现 | 备注 |
| --- | --- | --- |
| 方法一 | \$() |  |
| 方法二 | `` | 注意是反引号，不是单引号 |

在上面已经用到了命令替换，简单来说在用echo输出的时候如果使用命令替换，就会先执行命令，之后把返回值再输出回来。
* 比如：获得今年是第几年
* `echo 今年是第 $(date +%Y) 年`
    * 输出 今年是第 2019 年

### 数学计算
因为数学计算和命令替换的bash语法很相近，所以一起学习效果会好些吧。
#### 1、**用$(( ))来表达数学计算,和命令替换一起使用时要注意括号的个数**
* 比如：获得明年的年数
* `echo 明年是第 $(( **$(date +%Y)** +1))年`
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

 
