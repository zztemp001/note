## （一）总体描述
* datastore 的数据库定义类似于django，都是在model中定义数据库结构；
* datastore本身就相当于一个数据库，和传统的数据库有所区别，datastore 使用分布式的架构管理巨量数据，程序中可以通过描述数据之间的关系（父子）和使用索引来优化数据库性能；
* kind，数据的归类，相当于数据库的 table，相当于一张数据表，datastore使用 Model 类名命名 Kind；
* entiry，数据条目，相当于数据库的record，每一条entity由若干个property（也就是通常所说的字段）组成；
* property，数据属性，相当于数据库的字段，不同的是，datastore 中的组成 entity 的 property是不固定的，由不固定的property组成的类就是expando类；
* key，由所在entity的路径信息、kind名称（也就是model名称）、用户定义的key_name（如有）或系统自动生成的ID三部分混合构成，是一条entity在datastore中唯一的身份标识，相当于传统数据库的id字段；
* entity group，是一些具有同样父节点、保存在同一个服务器节点的 entity 的组合，和kind 是两个大致相同的概念，都是对 entity 的一种归类，通常用于transaction操作；
```
1、一个 property 可以是对另一个 entity 的引用，通过这样可以建立起“one to many”或者“many to many”的关系；
2、datastore 支持在一个 transaction 中一次性执行多条命令，如果某一条命令出错了，就可以整个 roll back 到最初状态
```

## （二）property
* datastore 中 property 的类型在 google.appengine.ext.db 中定义，继承自 Property 类
* entity 可用的 property 包括：string, boolean, integer, float, datetime, date, time, list,property , stringlist, reference, selfreference, user, blob, text, category, link, email, geo, IM, phonenumber, postaladdress, rating
* 每一个property的共有属性包括：verbose_name, name, default, required, validator, choices
** verbose_name 和 django 中的类似，forms 使用其来构建 fields 的 label
** name, 字段在数据库的名称，如果不设置，则默认为该 property 的变量名
** required, 如设置为 True，则需在构建 entity 实例时必须初始化其值，除非已经制定了 default 属性
** validator, 用于常规校验以后的附加校验，是一个函数
** choices, 一个列表，该字段的值只能取该列表中的一个
```python
# StringProperty, 小于 500 个字节，可以用作索引和排序  
# TextProperty, 大于 500 个字节，不能用于索引，不能用于排序  
# 如果要保存为 unicode 编码，要显示指定：  
class MyModel(db.Model):  
    string = db.StringProperty()  
    text = db.TextProperty()  
obj = MyModel()  
obj.string = u'zhaoweiming'  
obj.string = unicode('zhaoweiming', 'latin-1')  
obj.string = 'zhaoweiming'  # 缺省使用 ascii 编码  
  
# string 可以用于筛选和索引  
resultes = db.GqlQuery('SELECT * FROM MyModel WHERE string = :1', u'zhaoweiming')  
  
# text 的值通过 db.Text 指定  
obj.text = db.Text(u'lost of kittens')  
obj.text = db.Text('lost of kittens', 'latin-1')  
obj.text = db.Text('lost of kittens')  # 缺省是 ascii 编码  
  
# text 可以保存长字符串  
obj.text = db.Text(open('slslsl.text').read(), 'utf-8')  
```
```python
# 二进制的长串数据使用 BlogProperty 来保存, 是 db.Blob 的实例  
class MyModel(db.Model):  
    blob = db.BlobProperty()  
obj = MyModel()  
obj.blob = db.Blob(open('image.png').read())  
```
```python
# ListProperty, 值就是一个标准的 list, 可以使用任意一种数据类型，但只能是一种  
# 其中，StringListProperty, 等同于 ListProperty(basestring)  
class MyModel(db.Model):  
    numbers = db.ListProperty(long)  
obj = MyModel()  
obj.numbers = [2,4,6,8,10]  
obj.numbers = ['hello', 'world']  # 错误，必须是指定的 long 类型  
# 可以通过 ListProperty 或者 StringListProperty 来筛选数据，意思是：list 中至少有一个元素符合条件numbers 包含 6 这个元素的所有 entity  
resultes = db.GqlQuery('SELECT * FROM MyModel WHERE numbers = 6')  
  
# ListProperty 不能用于两个 List 之间的对比  
  
# 为了解决字符编码问题，字符串数据使用 StringListProperty() 来存储  
# 如果是保存二进制数据，可以使用 ListProperty(db.Blob) 来存储  
```

## （三）key
datastore 每一条 entity 的 key 由三部分构成：
* entity 与其他 entity 的父子关系的路径
* entity 所在的 kind 名，也就是定义它的 model 名
* 如果用户定义了 key_name, 则使用该字符串，否则，则使用 datastore 自动生成的 ID

由于 key 的这种特殊性，让它在整个 datastore 中具有唯一的标识，因此，在引用或者识别一条 entity 时，应该以 key 为准，而不是系统提供的 ID
```
1、key 可以使用 entity.key() 方法得到
2、ID 可以通过 entity.key().id() 方法得到
3、可以手动指定 entity 的 key_name 属性，为一个 str，但由于用户提供的字符串有可能不符合要求，应该在保存前进行检查
4、key, key_name, ID 三个属性在该 entity 创建以后就不能再修改
```

## （四）entity group
* 在通过 transaction 对 datastore 进行操作时，只能同时对同一 entity group 的数据操作
* 如果一个 entity 在保存时没有指定 parent 属性，则该 entity 就是一个 root entity
* 一个 entity 的父节点信息链，就是该 entity 的 path（路径）
* 只有在使用 transaction 功能时，才考虑使用 entity group
* entity group 不能带来查询的效率大幅提高，但对于 entity 的创建和更新能带来不错的效果
* 把 entity 之间的关系规划好，有利于减小数据库的尺寸

## （五）datastore执行数据库命令的几种途径
* 调用 Model 的方法，主要是对数据进行批量的读取、写入、更新
* 调用实例，也就是 entity 的方法，主要是对该 entity 的个体进行读写更新
* 调用 db.GqlQuery, 可以跨 Model 进行查询；
* 调用 Model 或 Expando 的 gql() 方法，用于该 Model 的内部查询，可省略 ‘SELECT * FROM model_name' 部分

## Expando 类
* datastore 中的 entity 不一定都需要拥有相同的 property
* 从 db.Model 继承而来的 Model 具有固定的 property, 和一般的 Model 一样
* 从 db.Expando 继承而来的 Model 可以同时具有动态 property 和固定的 property，动态 property 在临时添加时是该 entity 具有
```python
# 定义一个 Expando 类：  
class Person(db.Expando):  
    first_name = db.StringProperty()  
    last_name = db.StringProperty()  
    hobbies = db.StringListProperty()  
  
# 创建一个 entity  
p = Person(first_name = 'Albert', last_name = 'Johnson')  
p.hobbies = ['chess', 'travel']  
  
# 创建 entity 的动态 property  
p.chess_elo_rating = 1350  
p.travel_countries_visited = ['Spain', 'Italy', 'USA', 'Brazil']  
p.travel_trip_count = 13  
  
# 以上动态的 property 不用校验，可以是 datastore 规定的所有类型，包括 None  
# 可以手动删除动态 property:  
del p.chess_elo_rating  
```