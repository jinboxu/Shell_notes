## 流编辑器  sed

#### 一.sed工作流程

![流编辑器sed](D:\github_projects\shell笔记\pics\流编辑器sed.png)

sed 是一种在线的、非交互式的编辑器，它一次处理一行内容。

sed编辑器逐行处理文件，并将输出结果打印到屏幕上。sed命令将当前处理的行读入模式空间（pattern space）进行处理，sed在该行上执行完所有命令后就将处理好的行打印到屏幕上（**除非之前的命令删除了该行,-d**），sed处理完一行就将其从模式空间中删除，然后将下一行读入模式空间，进行处理、显示。处理完文件的最后一行，sed便结束运行。sed在临时缓冲区（模式空间）对文件进行处理，所以不会修改原文件，除非显示指明-i选项 



#### 二. 命令格式

sed [options]  'command'   file(s) 

sed [options]  -f scriptfile   file(s) 

*注:sed 和 grep 不一样，不管是否找到指定的模式，它的退出状态都是 0*

只有当命令存在语法错误时，sed 的退出状态才是非 0 



 ```
sed [选项] [动作]

选项与参数：
-n ：使用安静(silent)模式。在一般 sed 的用法中，所有来自 STDIN 的数据一般都会被列出到终端上。但如果加上 -n 参数后，则只有经过sed 特殊处理的那一行(或者动作)才会被列出来。
-e ：多个sed 的动作编辑，一般使用";"
-f ：直接将 sed 的动作写在一个文件内， -f filename 则可以运行 filename 内的 sed 动作；
-r ：sed 的动作支持的是延伸型正规表示法的语法。(默认是基础正规表示法语法)
-i ：直接修改读取的文件内容，而不是输出到终端。

function：
a ：新增行， a 的后面可以是字串，而这些字串会在新的一行出现(目前的下一行)
c ：取代行， c 的后面可以接字串，这些字串可以取代 n1,n2 之间的行
d ：删除行，因为是删除，所以 d 后面通常不接任何参数，直接删除地址表示的行；
i ：插入行， i 的后面可以接字串，而这些字串会在新的一行出现(目前的上一行)；
p ：列印，亦即将某个选择的数据印出。通常 p 会与参数 sed -n 一起运行
s ：替换，可以直接进行替换的工作,通常这个 s 的动作可以搭配正规表示法，例如 1,20s/old/new/g 一般是替换符合条件的字符串而不是整行

n 输出模式空间行，读取下一行替换当前模式空间的行，执行下一条处理命令而非第一条命令。
N 读入下一行，追加到模式空间行后面，此时模式空间有两行。

h 把模式空间里的内容复制到暂存缓冲区(覆盖),暂存缓冲区默认有一个空行
H 把模式空间里的内容追加到暂存缓冲区
g 取出暂存缓冲区的内容，将其复制到模式空间，覆盖该处原有内容
G 取出暂存缓冲区的内容，将其复制到模式空间，追加在原有内容后面
x 交换暂存缓冲区与模式空间的内容

一般function的前面会有一个地址的限制，例如 [地址]function，表示我们的动作要操作的行。下面我们通过具体的例子直观的看看sed的使用方法。
 ```



####三.sed命令

======================================================================================

地址（定址）用于决定对哪些行进行编辑。地址形式可以是数字、正则表达式或二者的结合。如果没 有指定 地址，sed 将处理输入文件中的所有行。 

sed 命令告诉 sed 对指定行进行何种操作，包括打印、删除、修改等。 

=======================================================================================





> 删除

```shell
#sed -r '2d' passswd 
#sed -r '/^root/d' passswd
```



> 替换

```shell
# sed -r 's/west/north/g' datafile
# sed -r 's/^west/north/' datafile
# sed -r 's/[0-9][0-9]$/&.5/' datafile //&代表在查找串中匹配到的内容
# sed -r 's#3#88#g' datafile 

#注释1到第5行
#sed -r '1,5s/(.*)/#\1/' passswd  //1
#sed -r '1,5s/.*/#&/' passswd    //2
#sed -r '1,5s/^/#/' passswd   //3 最简洁的用法
```



> 追加

```shell
# sed -r '2a\1111111111111' /etc/hosts
# sed -r '2a\1111111111111\
> 222222222222\
> 333333333333' /etc/hosts
```



> 插入

```shell
# sed -r '2i 1111111111111' /etc/hosts
# sed -r '2i 111111111\
> 2222222222\
> 3333333333' /etc/hosts
```



> 修改

```shell
# sed -r '2c\1111111111111' /etc/hosts
# sed -r '2c\111111111111\
> 22222222222\
> 33333333333' /etc/hosts
```



> 读入文件

```shell
#sed -r '/Suan/r /etc/hosts' datafile
#sed -r '2r /etc/hosts' a.txt       //第二行
#sed -r '/2/r /etc/hosts' a.txt     //找到有'2'的行
```



> 写到文件

```shell
# sed -r '/north/w newfile' datafile
# sed -r '3,$w /new1.txt' datafile
```



> 获取下一行命令: n

```shell
# sed -r '/eastern/{ n; d }' datafile  //必须用{},否则就是多重编辑了: -e
# sed -r '/eastern/{ n; s/AM/Archile/ }' datafile 
```



> 反向选择:  !

```shell
# sed -r '3d' /etc/hosts
# sed -r '3!d' /etc/hosts

# sed -r '/^nobody/ d' passwd
# sed -r '/^nobody/! d' passwd
```



> 暂存空间和模式空间

- 1.在文件每一行下面输出一个空行  

```shell
# sed G test
```

- 2.在文件的每一个非空行下面增加空行

```shell
#在文件的每一个非空行下面增加"一个"空行
# sed  '/^$/d;G' test     //不严谨
# sed  '/^[[:space:]]*$/d;G' test  //严谨

#使有两个空行
# sed  '/^[[:space:]]*$/d;G;G' test
```

- 3.在匹配regex的所有行前面插入一个空行

```shell
#sed '/regex/{x;p;x;}' datafile
```

```shell
#cat foo 
11111111111111 
22222222222222 
test33333333333333 
44444444444444 
55555555555555 
#sed '/test/{x;p;x;}' foo 
11111111111111 
22222222222222 

test33333333333333 
44444444444444 
55555555555555 
[root@localhost ~]# sed '/test/{p;x;}' test
1111111
222222222
test33333333333333 

44444444444444 
55555555555555 
[root@localhost ~]# sed '/test/{x;p;}' test  //x后模式空间为空行，p在屏幕上输出该空行，该行命令执行完毕又将空行打印到屏幕上
1111111
222222222


44444444444444 
55555555555555 
```

*sed 中 p 的作用是把模式空间复制到标准输出*

- 4.在匹配regex的所有行前面和下面都输出一个空行 

```shell
[root@localhost ~]# sed '/test/{x;p;x;G}' test
1111111
222222222

test33333333333333 

44444444444444 
55555555555555 
```



- 5.在文件的偶数行下面插入一个空行: n

```shell
[root@localhost ~]# sed 'n;G' test
1111111
222222222

test33333333333333 
44444444444444 

55555555555555 
666666

7777777
88888888

999999
```

 解释：        

**sed 中 n 的用法：将模式空间拷贝于标准输出。用输入的下一行替换模式空间。**      

执行 n 以后将第一行输出到标准输出以后，然后第二行进入模式空间，根据前面对 G 的解释，会在第二行后面插入一个空行，然后输出；再执行 n 将第三行输出到标准输出，然后第四行进入模式空间，并插入空行，依此类推  相应的：        

sed 'n;n;G' 表示在文件的第 3,6,9,12,... 行后面插入一个空行       

sed 'n;n;n;G' 表示在文件的第 4,8,12,16,... 行后面插入一个空行

sed 'n;d' 表示删除文件的偶数行  



- 6.N的使用: 略
- 7.将文件的行反序显示，相当于tac命令

```shell
[root@localhost ~]# cat test
1111111
222222222
test33333333333333 
44444444444444 
[root@localhost ~]#  sed -r '1!G;h;$!d' test
44444444444444 
test33333333333333 
222222222
1111111
```

1!G表示除了第一行以外，其余行都执行G命令；$!d表示除了最后一行以外，其余行都执行d命令。 



> sed中使用外部变量

```shell
[root@localhost ~]# var1=aaaaa
[root@localhost ~]# sed -r '3a $var1' test      //1. 错误，''是强引用
1111111
222222222
test33333333333333 
$var1
44444444444444 
[root@localhost ~]# sed -r "3a $var1" test
1111111
222222222
test33333333333333 
aaaaa
44444444444444 
[root@localhost ~]# sed -r "$a $var1" test      //2.错误
1111111
aaaa
222222222
aaaa
test33333333333333 
aaaa
44444444444444 
aaaa
[root@localhost ~]# sed -r '$a'"$var1" test     //3.分开写
1111111
222222222
test33333333333333 
44444444444444 
aaaaa
[root@localhost ~]# sed -r "\$a $var1" test    //4. 转义"$"符
1111111
222222222
test33333333333333 
44444444444444 
aaaaa
```

