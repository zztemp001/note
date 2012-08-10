## （一）One to One 和 One to Many 的关系范例
```python
class User(db.Model):  
    name = db.StringProperty(required = True)  
      
class Email(db.Model):  
    user = db.ReferenceProperty(User, collection_name = 'emails')  
    addr = db.EmailProperty(required = True)  
    mail_type = db.StringProperty()  
      
ericsk = User(name = 'zhaoweiming')  
ericsk.put()  
  
mail_1 = Email(user = ericsk, addr = 'foo@bar.com', type = 'example')  
mail_1.put()  
  
mail_2 = Email(user = ericsk, addr = 'zhao@weiming.com', type = 'myself')  
mail_2.put()  
  
# 列出一个User的所有email  
for email in ericsk.emails:  
    print email.addr  
```

## （二）Many to Many 的关系范例
```python
class Post(db.Model):  
    title = db.StringProperty(required = True)  
    body = db.TextProperty(required = True)  
    post_at = db.DateTimeProperty(auto_now_add = True)  
    categories = db.ListProperty(db.Key)  
    # datastore 的每一个 entity （记录）都有一个唯一的 key  
    # 以上用 ListProperty() 来表示某篇文章究竟属于多少个类别  
      
class Category(db.Model):  
    name = db.StringProperty(required = True)  
    description = db.TextProperty()  
     
    @property  
    def posts(self):  
        return Post.gql('WHERE categories = :1', self.key())  
    # 为 Category 添加一个名为 posts 的 property, 用来根据 key 返回相应的 posts  

# 新建 entity （记录）  
cat_comp = Category(name = '计算机')  
cat_comp.put()  
cat_prog = Category(name = '编程')  
cat_prog.put()  
  
post_1 = Post(title = '使用 Python', body = '第一篇文章')  
post_1.categories.append(cat_comp.key())  
post_1.categories.append(cat_prog.key())  
post_1.put()  
# 这样，这篇文章同时归类在 '计算机' 和 '编程' 类别下  
  
# 取得文章所在的分类  
categories = db.get(post_1.categories)  
for cat in categories:  
    print '类别： %s' % cat.name  
      
# 取得分类下的文章  
for post in cat_comp.posts.order('-post_at'):  
    print '标题： %s' % post.title  
```

## （三）一个联系人的实际应用
```python
class Contact(db.Model):  
    name = db.StringProperty()  
    birth_day = db.DateProperty()  
    address = db.PostalAddressProperty()  
    company_title = db.StringProperty()  
    company_name = db.StringProperty()  
      
class PhoneNumber(db.Model):  
    contact = db.ReferenceProperty(Contact, collection_name = 'phone_numbers')  
    phone_type = db.StringProperty(choices = ('home', 'work', 'fax', 'mobile', 'other'))  
    number = db.PhoneNumberProperty()  
      
scott = Contact(name = 'Scott')  
scott.put()  
PhoneNumber(contact = scott, phone_type = 'home', number = '(650)555-2200').put()  
PhoneNumber(Contact = scott, phone_type = 'mobile', number = '(650)555-2201').put()  
  
for phone in scott.phone_numbers:  
    print '%s: %s' % (phone.phone_type, phone.number)  
      
scott.phone_numbers.filter('phone_type = ', 'home')  
      
scott.phone_numbers.filter('phone_type = ', 'home').get().delete()  
```