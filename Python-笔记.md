```
创建一个类来表示一个人的信息。使用字典储存每个人的对象，把他们的名字作为键。
使用cPickle模块永久地把这些对象储存在你的硬盘上。使用字典内建的方法添加、删除和修改人员信息。
一旦你完成了这个程序，你就可以说是一个Python程序员了。
```

## 使用global声明全局变量
```python
global x, y, z
```

## 理解位操作
```python
5 << 3
11 >>1
```

## 打印文档字符串
```python
print fun.__doc__
```

## 获取命令行参数
```python
import sys
for i in sys.argv:
    print i
```

## 中文编码支持
```python
#方法一
# coding=utf-8

#方法二
#!/usr/bin/env python  
# -*- coding: utf-8 -*- 
```

## 使用pyqrcodec生成和读取二维码
```python
sudo apt-get install pyqrcodec
# 编码
python qrcode.py -e "http://www.python.org" ./a.png
# generated file: ./a.png

# 解码
python qrcode.py -d ./a.png
# get text: http://www.python.org
```

## 每个Python模块都有它的__name__，如果它是'__main__'，这说明这个模块被用户单独运行，我们可以进行相应的恰当操作。
```python
if __name__ == '__main__':
    print u'程序被单独执行'
else:
    print u'代码被导入到其他程序中使用'
```

## 可以使用内建的dir函数来列出模块定义的标识符。标识符有函数、类和变量。
```python
dir(sys)
dir()
```

## 使用元组
```python
age = 22
name = 'Swaroop'

print '%s is %d years old' % (name, age)
print 'Why is %s playing with that python?' % name
```

## 使用pickle和文件
```python
import cPickle as p
f = file('shopfile', 'w')
p.dump(['sldf', 'afasldfj', 'aldf'], f)
f.close()
f = file('shopfile')
new_list = p.load(f)
print new_list
```

## 使用列表综合
```python
listone = [2, 3, 4]
listtwo = [2*i for i in listone if i > 2]
```

## 当要使函数接收元组或字典形式的参数的时候，有一种特殊的方法，它分别使用*和**前缀。这种方法在函数需要获取可变数量的参数的时候特别有用。
```python
def powersum(power, *args):
    '''Return the sum of each argument raised to specified power.'''
    total = 0
    for i in args:
        total += pow(i, power)
    return total
# >>> powersum(2, 3, 4)
# 25
# >>> powersum(2, 10)
# 100
# 由于在args变量前有*前缀，所有多余的函数参数都会作为一个元组存储在args中。
# 如果使用的是**前缀，多余的参数则会被认为是一个字典的键/值对。
```

## 安装Python沙盒环境
```sh
sudo apt-get install python-setuptools  # 安装easy-install
sudo apt-get install python-virtualenv  # 安装virtualenv

mkdir envs
cd envs
virtualenv myenv_1  # 建立虚拟环境 myenv_1
source myenv_1/bin/activate  # 激活虚拟环境

# 以下操作在（myenv_1）中进行了
pip install django  # 安装django
pip install pil  # 在virtualenv环境中安装pil需要编译，需先安装build-essential和python-dev
```