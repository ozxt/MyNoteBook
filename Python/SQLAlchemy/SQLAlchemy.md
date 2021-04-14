```python
from sqlalchemy import create_engine
from sqlalchemy.orm import scoped_session, sessionmaker
from sqlalchemy.ext.declarative import declarative_base

engine = create_engine('sqlite:////tmp/test.db', convert_unicode=True)
# create_engine('mysql+pymysql://username:password@localhost/db_name', convert_unicode=True)
db_session = scoped_session(sessionmaker(autocommit=False,
                                         autoflush=False,
                                         bind=engine))
Base = declarative_base()
Base.query = db_session.query_property()

def init_db():
    # 在这里导入定义模型所需要的所有模块，这样它们就会正确的注册在
    # 元数据上。否则你就必须在调用 init_db() 之前导入它们。
    import yourapplication.models  # 导入以Base的子类
    Base.metadata.create_all(bind=engine)  # 如果没有表，会创建表；已有表不会覆盖，修改表结构。
```

```python
from sqlalchemy import Column, Integer, String, Boolean, TIMESTAMP, TEXT, ForeignKey

class User(Base):
    __tablename__ = 'user'
    id = Column(Integer, primary_key=True, autoincrement=True)
    name = Column(String(20), unique=True, nullable=False)
    password = Column(TEXT, nullable=False)
    admin = Column(Boolean, default=False)

    def __init__(self, name=None, password=None, admin=False):
        self.name = name
        self.password = password
        self.admin = admin

    def __repr__(self):
        return '<User %r>' % (self.name)

class Post(Base):
    __tablename__ = 'post'
    id = Column(Integer, primary_key=True,autoincrement=True)
    author_id = Column(Integer, ForeignKey('user.id'), nullable=False)
    created = Column(TIMESTAMP, nullable=False, default=current_timestamp())
    latest_update = Column(TIMESTAMP, nullable=False, default=current_timestamp())
    title = Column(String(80), nullable=False)
    body = Column(TEXT, nullable=False)


    def __init__(self, title=None, body=None, author_id=None):
        self.title = title
        self.body = body
        self.author_id = author_id

    def __repr__(self):
        return '<Title %r>' % (self.title)
```

```pyth
# 增
p1 = Post(...)
p3 = Post(...)
p3 = Post(...)
db_session.add(p1) # 一次增加单个
db_session.add_all([p2,p3]) # 一次增加多个 
db.seesion.commit()	

# 查
filrter和filter_by的区别：filter_by只能进行等值查询 id =1（一个等号）; filter可以进行比较查询，id>2,id <=2, id==3（两个等号）.
Post.query.all() # 取全部
Post.query.one() # 取一个
Post.query.one_or_none() # 取一个，没有的话返回None
Post.query.filter(Post.id == 2)   .all/one/one_or_none()  # filter条件
Post.query.order_by(Post.created.desc()).all()  # 降序
# equal
query.filter(User.name == 'ed')
query.filter_by(name='')
# not equal
query.filter(User.name != 'ed')
# like 
query.filter(User.name.like('%ed%'))
# in
query.filter(User.name.in_(['ed', 'wendy', 'jack']))
# not in
query.filter(~User.name.in_(['ed', 'wendy', 'jack']))

# use and_()
from sqlalchemy import and_
query.filter(and_(User.name == 'ed', User.fullname == 'Ed Jones'))

# or send multiple expressions to .filter()
query.filter(User.name == 'ed', User.fullname == 'Ed Jones')

# or chain multiple filter()/filter_by() calls
query.filter(User.name == 'ed').filter(User.fullname == 'Ed Jones')

# or --Make sure you use or_() and not the Python or operator!
from sqlalchemy import or_
query.filter(or_(User.name == 'ed', User.name == 'wendy'))

# match
query.filter(User.name.match('wendy'))
# 删


# 改



```

```python
from sqlalchemy import create_engine
from sqlalchemy.orm import scoped_session, sessionmaker, relationship, backref
from sqlalchemy.ext.declarative import declarative_base
from sqlalchemy import Column, Integer, String, DateTime, TEXT, ForeignKey
from sqlalchemy import Table

SQLALCHEMY_DB_URI = "sqlite:///test_sqlalchemy_backrefda.db"

Base = declarative_base()
# engine = create_engine(SQLALCHEMY_DB_URI, convert_unicode=True, echo=True)
engine = create_engine(SQLALCHEMY_DB_URI, convert_unicode=True)
db_session = scoped_session(sessionmaker(autocommit=False,
                                         autoflush=False,
                                         bind=engine))
Base.query = db_session.query_property()

post_tag = Table('post_tag', Base.metadata,
        Column('post_id', Integer(), ForeignKey('post.id')),
        Column('tag_id', Integer(), ForeignKey('tag.id'))
    )


class Post(Base):
    __tablename__ = 'post'
    id = Column(Integer(), primary_key=True,autoincrement=True)
    tags = relationship(
        'Tag',
        secondary = post_tag,
        backref = backref('posts', lazy='dynamic'),
        lazy = 'dynamic'
    )  # 多对多关系中需要用secondary参数指定第三方表模型对象

    def __init__(self, title=None, body=None, author_id=None):
        self.title = title

    def __repr__(self):
        return '<Title %r>' % (self.id)


class Tag(Base):
    __tablename__ = "tag"
    id = Column(Integer(), primary_key=True,autoincrement=True)
    name = Column(String(30))

    def __init__(self, name, weight=0):
        self.name = name

    def __repr__(self):
        return "tagname:{}".format(self.name)

# 第三方表--上级员工
up_obj_table = Table('staff_up_obj',Base.metadata,
    Column('id',Integer(),primary_key=True, autoincrement=True),
    Column('up_staff_id', Integer(), ForeignKey('staff.id',ondelete='CASCADE')),
    Column('down_staff_id', Integer(), ForeignKey('staff.id',ondelete='CASCADE'))
)


class Staff(Base):
    __tablename__ = 'staff'
    id = Column(Integer(), primary_key=True, autoincrement=True)
    name = Column(String(30),nullable=False,unique=True)    # 员工名称
    up_staffs = relationship('Staff', secondary=up_obj_table,
        primaryjoin = id == up_obj_table.c.down_staff_id,  # 主连接关系 为 本表id字段对应第三方表的down_staff_id
        secondaryjoin = id == up_obj_table.c.up_staff_id,  # 副连接关系 为 本表id对应第三表的up_staff_id
        backref = backref('down_staffs', lazy='dynamic'),  # 自关联多对多需要用primaryjoin和secondaryjoin指定主副连接关系，查询逻辑是根据主连接关系对应的第三方表的字段查询
        lazy = "dynamic"
        )

    def __init__(self, name):
        self.name = name

    def __repr__(self):
        return '<Staff %s>' % self.name

Base.metadata.drop_all(bind=engine)
Base.metadata.create_all(bind=engine)

######################################################
s1 = Staff("staff_A")
s2 = Staff("staff_B")
s3 = Staff("staff_C")
s4 = Staff("staff_D")
s5 = Staff("staff_E")
s5.up_staffs = [s4,s3]
db_session.add_all([s1,s2,s3,s4,s5])
db_session.commit()

ss = Staff.query.all()
for s in ss:
    print(type(s))
    print(s, s.up_staffs.all(),s.down_staffs.all())

#######################################################

p1 = Post()
p2 = Post()
p3 = Post()
t1 = Tag('tA')
t2 = Tag('tB')
p1.tags = [t1,t2]
p2.tags = [t1]
db_session.add_all([p1,p2,p3])
db_session.commit()

ps = Post.query.all()
for p in ps:
    print(type(p.tags))  # lazy:dynamic: sqlalchemy.orm.dynamic.AppenderQuery
                        # lazy:默认select 'sqlalchemy.orm.collections.InstrumentedList
    print(p, p.tags.all())

print('------------')

ts = Tag.query.all()
for t in ts:
    print(type(t.posts))
    print(t, t.posts.all())

    
#
post.tags
```

