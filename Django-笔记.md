
## 一个Product的model，每个Product有数张相关的图片
```python
class Product(models.Model): 
    name = models.CharField(max_length = 50) 

class Photo(models.Model): 
    product = models.ForeignKey(Product) 
    image = models.models.ImageField(upload_to='photos') 

#这样两个model建立起了一对多的关系，一个Product有无数个Photo都行。
```

## settings.py 中的 TIME_ZONE设置
```
TIME_ZONE = 'Asia/Shanghai'
```