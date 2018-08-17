[简书-Python SQLAlchemy 不确定条件查询](https://www.jianshu.com/writer#/notebooks/5859805/notes/32445248)
[github-Python SQLAlchemy 不确定条件查询](https://lyk2655.github.io/notes/Python SQLAlchemy 不确定条件查询.html)

在使用Python操作数据库时，可以使用SQLAlchemy库：
SQLAlchemy 是python 操作数据库的一个库。能够进行 orm 映射官方文档?[sqlchemy](http://docs.sqlalchemy.org/en/rel_0_9/)
SQLAlchemy“采用简单的Python语言，为高效和高性能的数据库访问设计，实现了完整的企业级持久模型”。SQLAlchemy的理念是，SQL数据库的量级和性能重要于对象集合；而对象集合的抽象又重要于表和行

## 安装和初始化
### pip安装命令
`pip install sqlalchemy`

检查是否安装成功
```python
>>>import sqlalchemy
>>>sqlalchemy.__version__
'1.2.10'
>>>                                                                                                               
```
显示版本号1.2.10，这样就说明安装成功了

### 使用sqlalchemy对数据库操作

#### 定义元信息，绑定到引擎
```
>>> from sqlalchemy import *
>>> from sqlalchemy.orm import *
>>> engine = create_engine('sqlalchemy.db', echo=True)  #定义引擎
>>> metadata = MetaData(engine) # 绑定元信息
>>>
```

#### 创建表
```
>>> users_table = Table('users', metadata,
...     Column('id', Integer, primary_key=True),
...     Column('name', String(40)),
...     Column('email', String(120)))
>>> 
>>> users_table.create()
>>> users_table = Table('users', metadata, autoload=True)
```


#### 插入数据
```
>>>i = users_table.insert()
>>>i.execute(name='linyk3', email='linyk2655@126.com')
```
#### 建立ORM映射
```
class User(db.Model):
    __tablename__ = "user_table"
    id = db.Column(db.Integer, primary_key=True, autoincrement=True)
    name = db.Column(db.String(40))
    email = db.Column(db.String(120))
    
    def __repr__(self):
        return '%s(%r, %r)' % (self.__class__.__name__, self.name, self.email)
```

#### 建立会话
```
>>> session = create_session()
>>> query = session.query(User)
>>> u = query.filter_by(name='linyk3').first()
```
如果有多个查询条件，并且查询条件不确定
可以使用下面查询：
```
def get_user(id=None, name=None, email=None, page=None):
    q = self.session.query(User)
    if id:
        q = q.filter(User.id == id)
    if name:
        q = q.filter(User.name == name)
	if email:
        q = q.filter(User.email == email)
    if page:
        q = q.limit(page * pageSize)
    return q.all()
```
还可以使用filter_by,但是不支持比较运算符
```
filters = {'name': 'linyk3', 'email': 'linyk2655@126.com'}
User.query.filter_by(**filters).first()
```

filter比filter_by的功能更强大，支持比较运算符，支持or_、in_等语法,如果表中有其他字段需要判断，例如年龄，日期等
```
filters = {
     User.name == 'linyk3',
     User.age > 25
}
User.query.filter(*filters).first()
```
经过试验，参数中 dict {} 和 list [] 都是可以的。
```
filters = [
     User.name == 'linyk3',
     User.age > 25 ]
User.query.filter(*filters).first()
```

如果查询条件有多个，且不确定前端是否传过来，或者传过来的值是否为空，可以采用查询条件拼接来实现，这时list.append()方法就很有用处了,项目例子：
```
@admin.route("/query", methods=["GET"])
@admin.route("/query/<int:page>", methods=["GET"])
def clock_query(page=None):
    if page is None:
        page = 1
    # app.logger.info(json.dumps(request.args))
    # 拼接查询条件
    param = []
    if ('userId' in request.args) and (request.args['userId']):
        userId = request.args['userId']
        param.append(LogInfo.userId == userId)
    if ('userName' in request.args) and (request.args['userName']):
        userName = request.args['userName']
        param.append(LogInfo.userName == userName)
    if ('startDate' in request.args) and (request.args['startDate']):
        startDate = request.args['startDate']
        param.append(db.cast(LogInfo.startTime, db.DATE) >= db.cast(startDate, db.Date))
    if ('endDate' in request.args) and (request.args['endDate']):
        endDate = request.args['endDate']
        param.append(db.cast(LogInfo.startTime, db.DATE) <= db.cast(endDate, db.Date))

    for item in param:
        app.logger.info(item)

    page_data = LogInfo.query.filter(*param).paginate(page=page, per_page=20)
    return render_template("loginfo.html", page_data=page_data)
```
 这样后台能接收的请求就很灵活了：
```
# 四个查询条件都有
http://localhost:8080/admin/query?userId=linyk001&userName=linyk3&startDate=20180723&endDate=20180723
# 只有两个查询条件
http://localhost:8080/admin/query?userId=linyk001&userName=linyk3
# 有四个查询条件，但其中两个为None，所以不应该作为查询条件
http://localhost:8080/admin/query?userId=&userName=&startDate=20180723&endDate=20180723
```

这样就非常灵活了，初学python，捣鼓了挺久的才实现，发现python还是很方便的，继续努力！
