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