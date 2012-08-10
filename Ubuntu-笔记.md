## 运行shell脚本
```sh
sh filename  # 使用sh命令运行脚本

chmod +x filename
./filename  # 通过 chmod使文件本身可执行

sh -x filename  # 执行该脚本并显示所有变量的值
sh -n filename  # 不需要执行脚本只是检查语法
```

## shell脚本的基本用法
```sh
a="hello world"  #对变量赋值
num=2  #对变量赋值
echo $a  打印变量的内容
echo "this is the ${num}nd"  # 打印变量a的内容，可以使用花括号来告诉shell我们要打印的是num变量

# 变量$SHELL包含了登录shell的名称，这里和/bin/bash进行了比较。
if [ "$SHELL" = "/bin/bash" ]; then
　echo "your login shell is the bash (bourne again shell)"
else
　echo "your login shell is not bash but $SHELL"
fi
# 通常用" [ ] "来表示条件测试。注意这里的空格很重要。要确保方括号的空格。
# [ -f "somefile" ] ：判断是否是一个文件
# [ -x "/bin/ls" ] ：判断/bin/ls是否存在并有可执行权限
# [ -n "$var" ] ：判断$var变量是否有值
# [ "$a" = "$b" ] ：判断$a和$b是否相等

[ -f "/etc/shadow" ] && echo "This passwors"  # &&是快捷操作符，如果左边为真则执行右边的语句

ftype=`file "$1"`  # 特殊变量$1包含了传递给该程序的第一个参数值。
case "$ftype" in
"$1: Zip archive"*)
　　unzip "$1" ;;
"$1: gzip compressed"*)
　　gunzip "$1" ;;
"$1: bzip2 compressed"*)
　　bunzip2 "$1" ;;
*) echo "File $1 can not be uncompressed with smartzip";;
esac
# 自动解压bzip2, gzip 和zip 类型的压缩文件

echo "What is your favourite OS?"
select var in "Linux" "Gnu Hurd" "Free BSD" "Other"; do
　　　　break
done
echo "You have selected $var"
# 下面面是系统显示的结果：
What is your favourite OS?
1) Linux
2) Gnu Hurd
3) Free BSD
4) Other
#? 1
You have selected Linux

for var in A B C ; do
　 echo "var is $var"
done
```

## 一个经典的脚本示例+注释
```
这个脚本的作用就是从一个文件里读取一个需要管理的所有log文件，并且读入一个数组。
然后判断是否存在，如果不存在则忽略。
如果存在将判断大小是否超过设定大小，如果超过则备份。
```
```sh
#!/bin/bash   # 开头这里声明了使用bash
  
##author:ginge   # 注释以#开头，可以从一行的任意位置开始
  
echo "logfiles to manage:"  
logfiles=${1-./logfiles}   # 如果存在第一个命令参数则使用它对logfiles赋值，如果不存在则赋值为./logfiles
size=${2-100}   # 变量引用以$符号开始，严格点要使用${variable}形式
  
index=0;   
#read the log file into the array   
while read logfiles[$index]   
   do   
     echo "$index: ${logfiles[${index}]}"  
     (( index +=1 ))   
   done < $logfiles   文件读取，使用一个<符就行了
    
i=0;   
while [ $i -lt $index ]   
   do   
      logfile=${logfiles[$i]}   
  
      (( i += 1 ))   
  
      dir=${logfile%/*}   
      if [ -z $logfile ] || [ ! -f $logfile ];then   
        echo "$logfile doesn't exist. ignored."  
        continue;   
      fi   
  
      echo "handling logfile: $logfile"  
      echo "entering dir:$dir"  
      cd $dir   
  
      result="`find $logfile -size +${size}M`"  
      if [ ! -z $result ]; then   
        echo "backup $logfile..."  
         cp $logfile $logfile".`date '+%G%m%d'`"  
         : > $logfile   
      else   
         echo "it's not bigger than ${size} M"  
      fi   
        
   done   

# 执行命令
# `` 这个`是跟~同一个键的那个符号，假如这样一个语句
# Result=`date`
# 执行后echo $Result将是这样的Tue Jan 6 15:13:41 CST 2009
```

## 使用du命令查看文件、目录的大小
```sh
du -sh .
du -ch .
du -sh one.txt two.txt  #可以同时查看多个文件
```

## 使用lynx或w3m在命令行上网
```
sudo apt-get install lynx
sudo apt-get install w3m  #对中文支持很好
```

## 通过 autossh 和公钥认证实现自动翻wall
```
ssh-keygen  # 生成rsa密钥和公钥

ssh-copy-id -i ~/.ssh/id_rsa.pub user@server  # 将生成的公钥上传到服务器

# 通过ssh登录服务器，在服务器上修改/etc/ssh/sshd_config文件，取消以下2行的注释
PubkeyAuthentication yes
AuthorizedKeysFile .ssh/authorized_keys

sudo service ssh restart  # 在服务器上重启ssh服务

sudo apt-get install autossh  # 安装autossh软件
autossh -M 20000 -f -ND 7070 user@server  # 之后就可以通过autossh自动后台登录ssh并实现自动重连了

# 如果想实现开机自动链接，则只需将以上命令加到/etc/rc.local文件中
```

## 递归删除目录下所有子目录的同类文件/子目录
```
# ubuntu
$ find . -name *.pyc -type f -exec rm -f {} \;
$ find . -name .svn -type d -exec rm -rf {} \;

#dos，在项目平级目录中执行
xcopy project_dir project_dir_1 /s /i
del .\yeekaa\*.pyc /s /q /f
for /r .\yeekaa %a in (.) do @if exist "%a\.svn" rd /s /q "%a\.svn"
```

## 使用autossh翻墙并自动重新链接
```
$ sudo apt-get install autossh
$ autossh -qTfnN -D 127.0.0.1:1080 loginname@yourserver.com
$ sudo netstat -anp | grep 1080   # 如果找到1080端口，表示已正常链接
```

## 保持SSH长时间连接
```
# 如何让ssh连接服务器或者ssh tunnel保持连接呢？其实也很方便，只要在/etc/ssh/ssh_config文件里加两个参数就行了

TCPKeepAlive yes  #保持连接
ServerAliveInterval 300  #每过5分钟发一个数据包到服务器表示“我还活着”

# 如果你没有root权限，修改或者创建~/.ssh/ssh_config也是可以的
```

## 安装 Sun JDK 和 Pycharm
```sh
# 从http://java.sun.com中下载jdk：jdk-6u32-linux-i586.bin，存放到/usr/java目录

sudo chmod u+x /usr/java/jdk-6u32-linux-i586.bin  # 给执行权限

sudo /usr/java/jdk-6u23-linux-i586.bin  # 安装JDK

sudo gedit /etc/profile  # 设置环境变量，写入以下设置：
export JAVA_HOME=/usr/java/jdk1.6.0_32
export PATH=$JAVA_HOME/bin:$PATH

source /etc/profile  # 使上面设置的环境变量生效

# 通过以下命令设置默认的jdk：
sudo update-alternatives --install /usr/bin/java java /usr/java/jdk1.6.0_32/bin/java 300
sudo update-alternatives --install /usr/bin/javac javac /usr/java/jdk1.6.0_32/bin/javac 300
sudo update-alternatives --config java

java -version  # 查看当前jdk版本

tar xfz pycharm-*.tar.gz  # 解压pycharm压缩包
./pycharm/bin/pycharm.sh  # 运行pycharm
```

## 动态跟踪文件内容的变化
```sh
tail -f
```

## 在命令行中编辑生成文件
```sh
cat > hello.py << "EOF"
```