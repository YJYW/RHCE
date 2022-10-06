# shell脚本相关

```bash
$bash example.sh one two three four five six  //分别代表 $1 $2 $3 $4 $5 $6 $0为example

#!/bin/bash
echo "脚本名为$0"
echo "参数1 为$1"

```

判断用户参数

```bash
[ -f /etc/fstab] 
```

| 运算符 | 作用 | 返回值 |
| --- | --- | --- |
| -d | 测试文件是否为目录类型 | 1 为目录类型 |
| -e | 测试文件是否存在 | 使用&&echo “true” 当输出为true时则存在 |
| -f | 判断是否为一般文件 | 0为一般文件 |
| -r | 测试当前用户是否有权限读取 |  |
| -w | 测试当前用户是否有权限写入 |  |
| -x | 测试当前用户是否有权限执行 |  |

判断文件是否为一般文件 使用逻辑与 &&

```bash
[root@redhat ~ ]# [ -e /etc/passwd ] && echo "Exist"
Exist 
```

判断USER登录用户是否为非管理员用户  使用逻辑或 ||

```bash
[root@redhat ~ ] # echo $USER
root
[root@redhat ~ ] # [ $USER = root ] || echo "user"
[root@redhat ~ ] # su student
[student@redhat / ] # [ $USER = root ] || echo "user"
user
```

判断当前登录用户是否为管理员用户 使用逻辑非!

```bash
[root@redhat ~] # [ ! $USER = root ] || echo "adminuser" 
adminuser
```

```bash
[root@redhat ~ ] # [ ! $USER = root ] && echo "user" || echo "root"
```

| 运算符 | 作用 |
| --- | --- |
| -eq | 是否等于 |
| -ne | 是否不等于 |
| -gt | 是否大于 |
| -lt | 是否小于 |
| -le | 是否等于或小于 |
| -ge | 是否大于或等于 |

```bash
[root@redhat ~ ]# FreeMem=`free -m | grep Mem: | awk '{print $4}'`
[root@redhat ~ ]# [ $FreeMem -lt 1024 ] && echo "Insufficient Memory"  
```

| 字符串 | 作用 |
| --- | --- |
| = | 比较字符串内容是否相同 |
| ! = | 比较字符串内容是否相同 |
| -z | 判断字符串内容是否为空 |

```bash
[root@redhat]# [ -z $String ]
[root@redhat]# echo $?
0
```

```bash
[root@redhat ~ ]# [ $LANG != "en.US" ] && echo "not en_US"
not en_US
```

if条件语句

```bash
if 条件测试操作
	then 命令序列
fi
```

```bash
#! /bin/bash
DIR="/media/cdrom"
if [ ! -e $DIR ]
then
mkdir -p $DIR
fi
```

if..elif..else用法

用户输入一个数判断大小输出不同内容

```bash
#!/bin/bash
read -p "Enter you score(0-100):  " GRADE
if [ $GRADE -gt 85 ] && [ $GRADE -le 100 ] ; then
echo "$GRADE is EXcellent"
elif [ $GRADE -gt 70 ] && [ $GRADE -le 84 ] ; then
echo "$GRADE is PASS"
else
echo "$GRADE is Fail"
fi
```

for循环的用法

```bash
for 变量 in 数值列表
do 命令序列
done 
```

```bash
[root@redhat ~ ]# vim users.sh

andy
barry
carl
duke
eric
george
```

```bash
#!/bin/bash
read -p "Enter Users Password : " PASSWD
for UNAME in `cat users.txt`
do
id UNAME &> /dev/null
if [ $? -eq 0 ]
then
echo "Already exists"
else
useradd $UNAME &> /dev/null
echo "$PASSWD" | passwd --stdin $UNAME &> /dev/null 
if [ $? -eq 0 ]
then
echo "$UNAME , Create success"
else 
echo "$UNAME , Create failure"
fi
fi
done
```

输入密码后读取users.txt的账号信息自动创建 然后将多余信息转移到黑洞文件中 

检测主机是否在线情况

```bash
vim ipadds.txt
192.168.122.1
192.168.56.4
192.168.55.31
```

```bash
#!/bin/bash
HLIST=$(cat ~/ipadds.txt)
for IP in $HLIST
do
ping -c 3 -i 0.2 -w 3 $IP &> /dev/null
if [ $? -eq 0 ] ; then
echo "HOST $IP is On-line"
else
echo "Host $IP is off-line"
fi
done
```

while条件循环语句

```bash
while 条件测试操作
do
	命令序列
done
```

```bash
#!/bin/bash
PRICE=$(expr $RANDOM % 1000)
TIMES=0
echo "商品实际价格为0-999之间,猜猜看是多少？"
while true
do
read -p "请输入您猜测的价格数目:" INT
let TIMES++
if [ $INT -eq $PRICE ] ; then
echo "恭喜你答对了,实际价格是 $PRICE"
echo "您总共猜测了 $TIMES 次"
exit 0
elif [ $INT -ge $PRICE ] ; then
echo "太高了"
else
echo "太低了"
fi
done

```

case条件测试语句

```bash
case 变量值in
模式1)
命令序列1
;;
模式1)
命令序列2
;;
.......
*)
默认命令序列
esac
```

```bash
#!/bin/bash
read -p "请输入一个字符,并按Enter键确认: " KEY
case "$KEY" in
[a-z]|[A-Z])
echo "您输入的是 字母"
;;
[0-9])
echo "您输入的是 数字"
;;
*)
echo "您输入的是 空格、功能键或其他控制字符。"
esac
```

自动任务服务程序

```bash
at 时间   //一次性命令只执行一次
at > 服务命令
at -l
```

```bash
echo "服务命令" | at 时间 //作用与上相同
at -l
```

删除自动任务

```bash
atrm 自动任务编号
```

周期性任务

```bash
时间周期性设置                     任务内容设置
30 0 3 4 4,6                       run_command
分钟 小时 日 月 星期               命令
```

| 字段 | 说明 |
| --- | --- |
| 分 | 取值为0-59的整数 |
| 时 | 取值为0-23的任意整数 |
| 日 | 取值为1-31的任意整数 |
| 月 | 取值为1-12的任意整数 |
| 星期 | 取值为0-7的任意整数，其中0与7均为星期天 |
| 命令 | 要执行的命令或程序脚本 |

```bash
crontab -e 
/
25 3 * * 1,3,5 /usr/bin/tar -czuf backup.tar.gz /home/wwwroot
/
crontab -l
25 3 * * 1,3,5 /usr/bin/tar -czuf backup.tar.gz /home/wwwroot
```
