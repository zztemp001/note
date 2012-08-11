## （一）Model的定义
GAE 中 model 的定义和 django 类似
```python
from google.appengine.ext import db  
  
class Pet(db.Model):  
    name = db.StringProperty(required = True)  
    type = db.StringProperty(required = True, choices = set(['cat', 'dog', 'bird']))  
    birthday = db.DateProperty()  
    weight_in_pounds = db.IntegerProperty()  
    spayed_or_neutered = db.BooleanProperty()  
    owner = db.UserProperty()  
#注意，datastore对数据库的定义在 google.appengine.ext.db 中。
```

## （二）执行数据库查询的集中方式
* 调用 Model 的方法，主要是对数据进行批量的读取、写入、更新
* 调用实例，也就是 entity 的方法，主要是对该 entity 的个体进行读写更新
* 调用 db.GqlQuery, 可以跨 Model 进行查询；
* 调用 Model 或 Expando 的 gql() 方法，用于该 Model 的内部查询，可省略 ‘SELECT * FROM model_name' 部分

## （三）对单条entity的操作
1. 创建entity。每一条 entity 可以通过 model 来构建，通过 put() 方法来存储。
```python
#用 model 来构造一条 entity 时，需要把 required 的property 赋值  
pet = Pet(name = 'Fluffy',  
          type = 'cat',  
          owner = users.get_current_user())  
  
#非 required 的 property 可以之后再赋值  
pet.weight_in_pounds = 24  
  
# 只有 put() 了，才真正把数据在 datastore 中保存下来  
pet.put()  
#或者  
db.put(pet)  
```
2. 获得一条entity。可以通过 key 得到一条数据，也可以在结果集中通过get()方法得到其中的第一条数据。
```python
story = db.get(key)  # db 层面的 get() 方法  
story = Story.get(key) # Model 层面的 get() 方法  
story = resultes.get()  # 返回结果集的第一条 entity  
```
3. 更新一条 entity。 取得需要修改的 entity 实例，对实例的 property 进行修改，然后使用 put() 方法保存更新。
```python
story.title = 'New Riding House'  
story.price = 90  
story.put() 
```
4. 删除一条数据
```python
story.delete()  
```

## （四）对批量entity的处理
1. 获得数据结果集
```python
query = Story.get(key)   # 通过指定的一个 key 或 key 列表返回结果  
query = Story.all()   # 直接得到 Story 的全部记录  
query = Story.gql('WHERE title = :1', 'Riding Hood')  
query = Story.gql('WHERE title = :title', title = 'Riding Hood')  
query = db.GqlQuery('SELECT * FROM Story WHERE title = "Riding Hood"')  
query = db.GqlQuery('SELECT * FROM Story WHERE title = :1', 'Riding Hood')  
query = db.GqlQuery('SELECT * FROM Story WHERE title = :title', 'Riding Hood') 
```
2. 对数据结果集进行进一步筛选、排序
```python
query.filter('author = ', users.get_current_user())  
query.filter('pages >=', 500)  
query.order('-date')  
query.order('price')  
query.filter('author = ', users.get_current_user()).filter('pages >= ', 500).order('-date').order('price')  
```
3. 使用 fetch() 方法真正执行查询并返回结果，最多不超过 1000 条
```python
resultes = query.fetch(5)  # 得到 5 条记录，从第一条开始  
resultes = query.fetch(10, 30)  # 获取 10 条记录，从第二条开始  
```
4. 对结果进行处理
```python
for story in resultes:  
    print '%s, %s, %d' % (story.title, story.author, story.price)  
```
5. 或对结果集的数据进行批量更新
```python
for story in resultes:  
    story.publisher = 'xinhua publishing house'  
db.put(resultes)  
```
6. 或对结果集进行删除操作
```python
db.delete(resultes)  
```

## （五）datastore的一些有用操作
```python
# 得到 Model 的名字  
n = Story.kind()  
  
# 得到 Model 的 property 的字典  
pdict = Story.properties()  
  
# 得到一个实例 entity 的 key  
story.key()  # entity 在 put() 之前是不能 key() 的，否则会引起 NotSavedError 异常  
  
# 得到一个实例 entity 的动态 property 列表  
story.dynamic_properties()  # 如果是静态的 entity，则返回一个空的 list  
  
# 得到一个实例 entity 的 xml 格式数据  
story.to_xml()  # 使用 Atom 和 GData 标准  
  
# 返回得到数据的数量，是最后 fetch 的数量，不超过 1000  
resultes.count()  
  
# 通过 key 得到一个实例 entity 的程序信息  
story.key().app()  # entity 所在的 app 名称  
story.key().kind()  # entity 所在的 kind 名称，通常是定义它的 Model 名  
story.key().id()  
story.key().name()  
```