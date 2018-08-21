[����-Python SQLAlchemy ��ȷ��������ѯ](https://www.jianshu.com/writer#/notebooks/5859805/notes/32445248)
[github-Python SQLAlchemy ��ȷ��������ѯ](https://lyk2655.github.io/notes/Python SQLAlchemy ��ȷ��������ѯ.html)

��ʹ��Python�������ݿ�ʱ������ʹ��SQLAlchemy�⣺
SQLAlchemy ��python �������ݿ��һ���⡣�ܹ����� orm ӳ��ٷ��ĵ�?[sqlchemy](http://docs.sqlalchemy.org/en/rel_0_9/)
SQLAlchemy�����ü򵥵�Python���ԣ�Ϊ��Ч�͸����ܵ����ݿ������ƣ�ʵ������������ҵ���־�ģ�͡���SQLAlchemy�������ǣ�SQL���ݿ��������������Ҫ�ڶ��󼯺ϣ������󼯺ϵĳ�������Ҫ�ڱ����

## ��װ�ͳ�ʼ��
### pip��װ����
`pip install sqlalchemy`

����Ƿ�װ�ɹ�
```python
>>>import sqlalchemy
>>>sqlalchemy.__version__
'1.2.10'
>>>                                                                                                               
```
��ʾ�汾��1.2.10��������˵����װ�ɹ���

### ʹ��sqlalchemy�����ݿ����

#### ����Ԫ��Ϣ���󶨵�����
```
>>> from sqlalchemy import *
>>> from sqlalchemy.orm import *
>>> engine = create_engine('sqlalchemy.db', echo=True)  #��������
>>> metadata = MetaData(engine) # ��Ԫ��Ϣ
>>>
```

#### ������
```
>>> users_table = Table('users', metadata,
...     Column('id', Integer, primary_key=True),
...     Column('name', String(40)),
...     Column('email', String(120)))
>>> 
>>> users_table.create()
>>> users_table = Table('users', metadata, autoload=True)
```


#### ��������
```
>>>i = users_table.insert()
>>>i.execute(name='linyk3', email='linyk2655@126.com')
```
#### ����ORMӳ��
```
class User(db.Model):
    __tablename__ = "user_table"
    id = db.Column(db.Integer, primary_key=True, autoincrement=True)
    name = db.Column(db.String(40))
    email = db.Column(db.String(120))
    
    def __repr__(self):
        return '%s(%r, %r)' % (self.__class__.__name__, self.name, self.email)
```

#### �����Ự
```
>>> session = create_session()
>>> query = session.query(User)
>>> u = query.filter_by(name='linyk3').first()
```
����ж����ѯ���������Ҳ�ѯ������ȷ��
����ʹ�������ѯ��
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
������ʹ��filter_by,���ǲ�֧�ֱȽ������
```
filters = {'name': 'linyk3', 'email': 'linyk2655@126.com'}
User.query.filter_by(**filters).first()
```

filter��filter_by�Ĺ��ܸ�ǿ��֧�ֱȽ��������֧��or_��in_���﷨,��������������ֶ���Ҫ�жϣ��������䣬���ڵ�
```
filters = {
     User.name == 'linyk3',
     User.age > 25
}
User.query.filter(*filters).first()
```
�������飬������ dict {} �� list [] ���ǿ��Եġ�
```
filters = [
     User.name == 'linyk3',
     User.age > 25 ]
User.query.filter(*filters).first()
```

�����ѯ�����ж�����Ҳ�ȷ��ǰ���Ƿ񴫹��������ߴ�������ֵ�Ƿ�Ϊ�գ����Բ��ò�ѯ����ƴ����ʵ�֣���ʱlist.append()�����ͺ����ô���,��Ŀ���ӣ�
```
@admin.route("/query", methods=["GET"])
@admin.route("/query/<int:page>", methods=["GET"])
def clock_query(page=None):
    if page is None:
        page = 1
    # app.logger.info(json.dumps(request.args))
    # ƴ�Ӳ�ѯ����
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
 ������̨�ܽ��յ�����ͺ�����ˣ�
```
# �ĸ���ѯ��������
http://localhost:8080/admin/query?userId=linyk001&userName=linyk3&startDate=20180723&endDate=20180723
# ֻ��������ѯ����
http://localhost:8080/admin/query?userId=linyk001&userName=linyk3
# ���ĸ���ѯ����������������ΪNone�����Բ�Ӧ����Ϊ��ѯ����
http://localhost:8080/admin/query?userId=&userName=&startDate=20180723&endDate=20180723
```

�����ͷǳ�����ˣ���ѧpython��������ͦ�õĲ�ʵ�֣�����python���Ǻܷ���ģ�����Ŭ����
