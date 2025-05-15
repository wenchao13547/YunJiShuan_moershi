第一个shell脚本

cd /opt
touch helloword.sh
vim helloword.sh


编辑shell脚本内容

\#!/bin/bash

str="hello word!"

echo str

变量：

引用变量：

${变量名}

查看环境变量

env

查看当前shell临时变量

set

将临时变量变为环变量

export

```
[root@MissHou tmp]# export A=hello 
```

//临时将一个本地变量（临时变量）变成环境变量

永久生效：

```
vim /etc/profile 或者 ~/.bashrc

export A=hello
```

数组：

**array_name=(value1 value2 ... valuen)**

定义数组 数组名=(值1 值2 ...值n)

\# name=(tom mary natasha)

\# name[3]=jim

查看数组中某个元素值

\# echo ${name[0]}

tom

\# echo ${name[2]}

natasha

**内置变量**

**$?**：上一条命令执行后返回的状态，当返回状态值为0时表示执行正常，非0值表示执行异常或出错

**$$**：当前所在进程的进程号 echo $$ eg：kill -9 `echo $$` = exit 退出当前会话

**$!**：后台运行的最后一个进程号 （当前终端）

**!$** 

调用最后一条命令历史中的参数

**!!** 

调用最后一条命令历史

**$#：**脚本后面接的参数的个数

**$\*：**脚本后面所有参数，参数当成一个整体输出，每一个变量参数之间以空格隔开

**$@**: 脚本后面所有参数，参数是独立的，也是全部输出

**$0：**当前执行的进程/程序名 echo $0

**$1~$9** 位置参数变量

**${10}~${n}** 扩展位置参数变量 第10个位置变量必须用{}大括号括起来

$HOME/.bashrc 

当前用户的bash信息（aliase、umask等） （用户级）

$HOME/.bash_profile 当前用户的环境变量（用户级）

$HOME/.bash_logout 

每个用户退出当前shell时最后读取的文件 （用户级）

/etc/bashrc 使用bash shell用户全局变量 （系统级）

/etc/profile 

系统和每个用户的环境变量信息 （系统级）

用户登录系统读取相关文件的顺序：

/etc/profile——>$HOME/.bash_profile——>$HOME/.bashrc——>/etc/bashrc——>$HOME/.bash_logout

简单的用户明亮

**标准输入输出概念**

stdin 标准输入 是指：来自键盘输入或者其他命令的标准(错误)输出的数据。

stdout标准输出就是终端显示器。 shell 的所有输出（包括shell 中运行的程序和脚本）会被定向到标准输出。

设置密码：

```
echo "123" | passwd user01 --stdin # 设置：用户密码
```

**echo命令**

将指定内容输出到屏幕（标准输出）

常用选项 -n： 输出结果后面没有换行符

-e：启用转移符\ (需要输出特殊符号的时候：\n \t)

\n 换行

\t 横向制表符

**grep命令**

**语法：**

grep [选项]... PATTERN [FILE]...

**选项：**

-i ## 表示：忽略大小写

-E ## 表示：启用<扩展正则>

或者修改grep命令为：egrep



-P ## 表示：启用<Perl正则>

-r ## 表示：递归操作

-l ## 输出：包含<匹配内容>的<文件名>

-o ## 表示：仅输出匹配内容本身

-q ## 表示：不输出，静默操作，通常用于：条件判断

-v ## 表示：输出不匹配的行

-c ## 显示：统计有多少行

-n ##显示：匹配的行号

**正则表达式**

Bash通配符：是Bash Shell自带的

\* 表示：匹配任意的一个或多个字符

? 表示：匹配任意的一个字符

[] 表示：匹配[xyx]列表中任意的一个字符

![img](file:///C:/Users/文超/OneDrive/桌面/云计算/imgs/基本正则.png)

扩展正则

![img](file:///C:/Users/文超/OneDrive/桌面/云计算/imgs/扩展正则.png)

**实用案例**

1.查看配置文件/chrony.conf 的有效信息（过滤注释和空白行）

cat /etc/chrony.conf | grep -vE "^$|^#"

2.ip a命令输出结果在过滤出ip地址

ip a | grep -oE "([0-9]{,3}\.){3}[0-9]{,3}/[0-9]{,2}"

3.查看系统所有的普通用户的信息 （uid大于1000）

grep -E ":[1-9][0-9]{3,}:" /etc/passwd

4.查找本地yum源中以pcre开头的软件包

yum list | grep "^pcre"

5.查找本机安装的所有以a开头的软件

rpm -qa | grep "^a"

6.查找安全日志secure中关于failed和error的失败和错误的信息

cat /var/log/secure | grep -iE "failed|error"

标准输入和重定向

**从标准输入中，读取数据，输入到指定文件中**

\# 覆盖式输入

cat > 4.txt << EOF

aaa

111

EOF

\# 添加式输入

cat >> 4.txt << EOF

aaa

222

**sort命令**

★ 命令选项：

**-g 按一般数值排序**

-n 按字符串数值排序

**-r 逆序/降序排序（默认是：升序）**

-f 忽略<字母大小写>

-b 忽略<前导的空白区域>

**-k 指定：字段编号.字段字符位置**

-o 将<排序结果>回写入<原文件>，例如：cat 1.txt

★ 举例：

cat 1.txt | sort -k 4 #按照第4个字段去排序

ps aux | sort -k 6 -g #第6个字段按数值大小去排序

ps -e -o pid,ppid,cmd | sort -k 2g -k 3 # 首先按照第2列的数字大小升序排序，然后再按照第3列的字符大小升序排序

ps -e -o pid,ppid,cmd | sort -k 2g -k 3r # 首先按照第2列的数字大小升序排序，然后再按照第3列的字符大小降序排序



**2、uniq命令**

作用：uniq 命令删除文件中的重复行。 uniq 命令读取由 InFile 参数指定的标准输入或文件。该命令首先比较相邻的行，然后除去第二

行和该行的后续副本。

★ 命令选项：

**-c 统计，在输出行前面加上每行在输入文件中出现的次数。**

-d 仅显示重复行。

-u 仅显示不重复的行。

★ 举例：

例：cat 3.txt | sort | uniq

选项 -c 统计重复的次数

例：cat 3.txt | sort | uniq -c

注意：

**只能对连续的行去重，因此去重前一般需要先排序**



**wc命令**

作用：统计指定文件中的字节数、字数、行数, 并将统计结果显示输出。

★ 命令选项：

\- c 统计字节数。

**- l 统计行数。**

\- w 统计字数，单词数。

★ 举例：

cat /etc/passwd | wc -l #统计当前的用户数量

wc -c 1.txt #统计文件的字节数



**、cut命令**

功能：将每个文件中每一行的匹配部分(按指定规则截取)打印到标准输出。

语法：cut OPTION... [FILE]... 

##### 常用命令：查看文件固定列的内容

```
cut -d ":" -f 1 /etc/passwd
```

★ 常用选项：

**-d, --delimiter=DELIM  指定隔符，需使用单引号 或 双引号**

※ 默认为：Tab制表符 ◀◀◀

※ 文件中的连续多个分隔符，只有第1个有效，剩余的视为普通字符

※ 只能和 -f选项 一起使用

**-f, --fields=LIST 按字段进行选择，承认分隔符，以分隔符区分字段**

※ 除非指定了-s选项，否则还会打印任何不包含分隔符的行

-s, --only-delimited 不打印没有包含分界符的行

**-c, --characters=LIST 按字符进行选择，不承认分隔符**

-b, --bytes=LIST 按字节进行选择，不承认分隔符

-n ※ 和 -b选项 一起使用:不分割<多字节字符>，如：汉字

※ 在<bash>中，是用<3个字节>来显示一个<汉字>

--complement 补全选中的<字节、字符、字段>

--output-delimiter=TRING 使用指定的字符串作为输出分界符，默认采用输入的分界符



**tr 命令**

作用：tr 命令可以对来自标准输入的字符进行替换、压缩和删除。

语法：**tr [选项] 字符集1 字符集2**

字符集1：指定要转换或者删除的原字符集。当执行转换操作时，必须使用参数“字符集2”指定转换的目标字符集。但执行删除操

作时，不需要参数“字符集2” 。

★ 常用选项

-s 替换连续出现的字符

-d 删除字符

★ 举例：

替换一个字符

[root@localhost ~]# echo "hello" | tr 'h' 'H'

Hello

替换连续出现的字符

[root@localhost ~]# echo "good" | tr -s 'o' 'o'

god

cat 4.txt | tr -d 0-9 #删除所有数字

cat 4.txt | tr -d a-z #删除所有小写字母



**tee 命令**

作用：主要被用来向standout(标准输出流，通常是命令执行窗口）输出的同时也将内容输出到文件

语法：**tee [选项]... [文件]...** 

★ 常用选项

**-a, --append内容追加到给定的文件而非覆盖**

★ 举例：

head -5 /etc/passwd | tee 1.txt #在标准输出同时保存到指定文件

tail -2 /etc/passwd | tee -a 1.txt #在标准输出同时追加保存到已有的文件

who | tee 2.txt 3.txt #在标准输出的同时保存到多个文件中



**watch 命令**

作用：watch命令可以实时全屏监控当前命令执行的动态变化结果。

语法：**watch [选项] 命令**

★ 常用选项

-n 每隔几秒刷新一次结果（默认是每隔2秒刷新一次) 

-d 高亮显示命令执行结果的变化

-t 隐藏全屏顶端的时间间隔，命令信息

★ 举例：

watch date #监视当前时间，默认2秒刷新一次

watch -n 3 date #监视当前时间，改为3秒刷新一次

watch -d date #高亮显示有变化的结果

watch -d "date;who" #监视多个命令，注意加引号



**sleep和wait 命令**

**sleep 命令**

功能：暂停一段时间，时间单位可以是 s m h d，默认是 s(秒)

★ 举例：

sleep 10s

**wait 命令**

功能:

▶ wait命令 是用来阻塞当前进程的执行，直至当前进程的指定子进程或者当前进程的所有子进程返回状态为0，执行结束之后，方

才继续执行。

▶ 使用 wait命令可以在bash脚本的多进程执行模式下，起到一些特殊控制的作用。

★ 举例：

\#!/bin/bash

sleep 10 &

sleep 5&

wait #等待10秒后，退出 所有子进程执行完成

\#!/bin/bash

sleep 10 &

sleep 5&

wait $! #$!表示上个子进程的进程号，wait等待指定子进程，等待5秒后，退出



**read 命令**

作用：用于脚本与用户的交互，可以将用户输入的值存入变量。

常用命令

read -p "请输入参数" num1

★ 常用选项

-p：设置提示信息

-e：用户输入的时候允许回退

-s：不显示输入的内容 （密码输入的时候使用）

-t：timeout 设置超时时限，单位：秒

★ 举例：

\#!/bin/bash

read -e -p "请输入你的用户名：" name

echo "你的用户名是：$name"

read -e -s -p "请输入你的密码：" passwd

echo "你的密码是：$passwd"



**timeout 命令**

作用： 控制command命令的执行时长

语法：**timeout [选项] N[s|m|d] command命令 [参数]...** 

N[s|m|d] 设置命令的执行时长为N，默认单位为s秒，还可以是m分钟、h小时、d天

★ 常用选项

-s, --signal=SIGNAL 设置终止command命令进程的信号名，默认为TERM信号

TERM/15 优雅的终止<command命令进程>（留有时间，非立即，非强制）

SIGKILL/9 强制的终止<command命令进程>（不留时间，立即执行，只能由管理员发出）

-k, --kill-after=N 设置当第一个终止信号发出N时长之后，如果command命令进程仍在运行，则执行SIGKILL强制终止。

★ 举例：

timeout 10 sleep 30 #10秒后终止sleep命令

timeout 5 ping 8.8.8.8 # 5秒后终止ping

timeout -s 9 5s ping 8.8.8.8 或者

timeout -s SIGKILL 5s ping 8.8.8.8 # 5秒后杀死进程

timeout -k 10 1m ping 8.8.8.8 #timeout命令等待ping命令运行一分钟后，发送终止信号。如果ping命令还在运行，将在十秒钟

后发送kill信号，强制杀掉该进程。



### shell流程控制

**let命令**

举例：

let n=(1+2)*3; echo $n # 计算结果 为 9

echo $((1+2)) #计算结果为3

**bc命令**

处理整数：

echo "(1+2)*(3+4)" | bc # 计算结果 = 21

echo "10/3" | bc # 计算结果 = 3 (不支持浮点小数)

处理小数：

echo "scale=2;10/3" | bc # 计算结果 = 3.33 ( 支持浮点小数)

##### test命令

**格式1：test 条件表达式**

```
test -e /tmp/passwd
```

**格式2：[ 条件表达式 ]** （记住左右都要有空格）

```
[ -x /tmp/test.sh ]
```

**1）文件判断**

格式：[ 操作符 文件或者目录 ]

常用的测试操作符：

-d：用于测试是否是目录

-e：测试目录或文件是否存在

-f：测试文件是否存在且为普通文件

-r：测试当前的用户是否有读的权限

-w：测试当前用户是否有写的权限

-x：测试当前用户是否具有可执行权限

-L：测试是否为符号链接

-b：测试是否为块设备文件

-c：测试是否为字符型特殊文件



sehll判断条件

**判断整数值大小**

**格式：[ 整数1 操作符 整数2 ]**

常用的测试操作符：

-eq：等于

-ne：不等于

-gt：大于

-lt：小于

-le：小于等于

-ge：大于等于

例如：

[ 2 -lt 4 ]

**判断字符串**

**格式：[ 字符串1 操作符 字符串2 ]**

**或者：[ 操作符 字符串 ]**

例如

[ -Z ""]

[ $PWD= "/root"]

常用的测试操作符：

-z STRING # 判断：字符串是否为空

-n STRING # 判断：字符串是否为非空

STRING1 = STRING2 # 判断：两个字符串内容是否相同

STRING1 != STRING2 # 判断：两个字符串内容是否不同



!:取反,-a：逻辑与,-o：逻辑或

[ $PWD= "/root" -o "abc" != "bcd" ]



**命令连接符（**;分号 和 && 和 ||**）**

**用于连接多条命令，并通过前面命令的执行结果，判断后面命令的执行**

![img](file:///C:/Users/文超/OneDrive/桌面/云计算/imgs/逻辑运算符.png)

分支语句

语法如下：

if[条件判断式1]

then

命令块1 # 当条件判断式1成立时

**elif** [条件判断式2]

then

命令块2 # 当条件判断式2成立时

…省略更多条件... 

else

命令块 # 当所有条件都不成立时执行

fi

例如：

```
#!/bin/bash
echo "这是成绩分析脚本"
read -e -p "请.输入你的成绩：" a
if [ $a -gt 0 -a $a -lt 60 ]
then
echo "不及格"
elif [ $a -ge 60 -a $a -lt 80 ]
then
echo "良好"
elif [ $a -ge 80 -a $a -lt 100 ]
then
echo "优秀"
elif [ $a -eq 100 ]
then
echo "满分真棒"
else
echo "分数输入错误：1-100"
fi
```

case用法：

**case** 变量值 in

模式1)

命令块 1

;;

模式2)

命令块 2

;;

...... 

*) # ”*“代表任意

默认命令序列

**esac**

例如：

```
#!/bin/bash
case $1 in
start)
/usr/bin/systemctl $1 httpd
/usr/bin/ps aux |grep httpd
echo "httpd start"
;;
stop)
/usr/bin/systemctl $1 httpd
/usr/bin/ps aux |grep httpd
echo "httpd stop"
;;
restart)
/usr/bin/systemctl $1 httpd
echo "httpd 服务正在重新启动中......"
;;
status)
/usr/bin/systemctl $1 httpd
;;
*)
echo "please input start|stop|restart|status"
esac
```



**for循环语句**

**1、语法格式**

**格式一**：算数条件判断for循环（c风格）

语法格式：

**for ((EXPR1; EXPR2; EXPR3)) ;**

**do**

**COMMANDS ;**

**done**

举例：

```
for ((i=1;i<=10;i++));
do
echo "这是第$i次循环"
done
```

**格式二**：遍历单词序列 for循环

**for 变量 in 单词序列 ;**

**do**

**COMMANDS;**

**done**

举例：

```shell
for i in 'cut -d ":"' -f 1 /etc/passwd ; do echo "/etc/passwd文件保存的用户有${i}"; done
```



while ((条件表达式]))

**do**

**命令块**

**done**

举例：

\#!/bin/bash

n=1

while ((n<=10)); #满足条件则循环继续

do

read -e -p "请输入一个数字：" n

done



until ((条件表达式]))

**do**

**命令块**

**done**

举例：

\#!/bin/bash

n=11

until ((n<=10)); #满足条件则循环结束

do

read -e -p "请输入一个数字：" n

done



**exit** 退出整个程序

**break** 结束当前循环，或跳出本层循环

**continue** 忽略本次循环剩余的代码，直接进行下一次循环



##### 环境变量$RANDOM，它会在每次访问时生成一个随机数，随机数范围为0-32767

举例：

产生0~1之间的随机数：

echo $[$RANDOM%2]



函数的定义和使用：

```
#!/bin/bash
read -p "请输入第一个整数: " num1
read -p "请输入第二个整数: " num2
add() {
    local num1=$1
    local num2=$2
    local result=$((num1 + num2))
    echo $result
}
re=add num1 $num2
echo ${re}
```

**sed命令**

★ sed 与 awk 的区别：

sed 是一个基于行字

sed 是一个基于行字符流，执行整行处理的文本处理工具。

awk 是一个基于行字符流，精细到列处理的文本处理工具。

**sed 的优点**

(1) Sed 不会导致内存不足

Sed 只会缓存<一行的内容>在<模式空间>

(2) Sed 是一个<非交互式>的<字符流编辑器>。

(3) Sed 不是在源输入上直接进行处理的，而是先将读入的行放到缓冲区中，对缓冲区中的内容进行处理，处理完毕后，直接将处理结果标准输出到屏幕上，而不会写回<源文件>。

语法：

```
**sed [option] '地址定位 + sed指令' filename**
```

**sed 的常用选项**

**-r** # 启用扩展正则表达式，但是，需要注意元字符的调用格式。

扩展的正则表达式中，如果元字符与sed专用字符发生冲突，则需使用【\转义符】。

**-e** # 执行多个script脚本，如：-e 'script01' -e 'script02' -e 'script03'

注意：多个script脚本是顺序执行的，前一个script脚本的行输出信息，是后一个script脚本的行输入信息。

前一个script脚本未匹配未执行，不会影响后一个script脚本的正常执行。

**-n** # 抑制sed命令的<标准输出

默认情况下，sed命令将标准输出：未处理的行数据和已处理的行结果。

**-i** # 将标准输出覆盖式回写到源文件，直接修改源文件。

▶ 注意：不要同时使用-n 和 -i选项，否则，源文件的内容将受到极大的破坏 。

选项 -n 静默输出，关闭模式空间的输出，不会输出未匹配到的行 一般与p指令(输出)结合使用。

输出单独行



**sed常用指令****（子命令动作）**

**p：**print，打印

**d：** delete，删除

**! :** 非 ，放在命令前面表示取反

**i：**在匹配行前面插入新行添加指定内容

**a：**在匹配行后面插入新行添加指定内容

**c：**用新内容直接替换匹配的整行

**s：**用新内容替换指定的旧文本内容

**y：**转换的命令，对应转换

**r：**将指定的文件内容添加到符合条件的行处 r后接文件路径

**w：**将地址指定的范围内的行另存至其他指定文件中 直接可以对文件操作



使用方式 1（p指定操作行）举例：

sed -n '1p' 1.txt # 输出第一行

sed -n '$p' 1.txt # 输出最后一行

▶输出范围行

sed -n '1,5p' 1.txt # 输出第一行到第五行

sed -n '1,+3p' 1.txt #输出从第1行起，往后数3行

▶输出间隔行

sed -n '1~2p' 1.txt # 输出从第一行开始，每数两行输出一次 （输出奇数行）

使用方式2（正则地址定位）举例：

语法： sed -选项 /正则表达式/ 文件

```
sed -n '/^root/,/^adm/p' 1.txt # 输出以root开头的行一直到以adm开头行之间的行
```

注意正则必须写在/ /（双斜杠）里面

sed常用命令：

**1）p：打印**

sed -n '1p' 1.txt # 输出第一行，p指令一般与-n选项一起使用

**2）d：删除**

sed '1d' 1.txt #删除第一行内容

**3）i：插入行**

sed '/^root/i\hello' /etc/passwd #root开头的行上面新开一行添加\以后的"hello"内容

**4）a：追加行**

sed '/^root/a\hello' /etc/passwd #root开头的行下面新开一行添加\以后的"hello"内容

用法3替换：

```
sed 's/root/ROOT/g' /etc/passwd # 将每行的全部root换成大写
```

