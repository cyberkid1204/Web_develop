# Working log

## 5.18 项目启动

1.新建文件夹，git管理

```
mkdir adrian
cd adrian
git init
```

2.拉取项目

```
git clone  git@git.xyz10.com:chuhe/chuhe.git -b dev
```

3.安装pyenv用作版本控制

```
# https://zhuanlan.zhihu.com/p/36402791
brew install pyenv

# https://www.jianshu.com/p/c47c225e4bb5
> CFLAGS="-I$(brew --prefix zlib)/include" \
LDFLAGS="-L$(brew --prefix zlib)/lib" \
pyenv install -v 3.9.7
```

4.安装配置poetry

```
curl -sSL https://raw.githubusercontent.com/python-poetry/poetry/master/get-poetry.py | python -
```

5.配置依赖安装在项目目录

```
> poetry config virtualenvs.in-project true
> poetry config virtualenvs.path ".venv"
```

6.在项目下配置虚拟环境

```
cd .venv/bin
source activate
```

7.用poetry初始化项目依赖(若已经有项目结构则不需要)

```
# https://zhuanlan.zhihu.com/p/110721747
> poetry init
```

8.进入poetry环境

```
poetry shell
```

9.安装依赖包

```
poetry install
```

10.启动项目

```
uvicorn apps:app --reload
```



Ps: fastapi | kafka | rabbitMQ | docker | pytest





------



## 5.19

### 1.rabbitmq学习

项目路径：/Users/zhunanzhou/Desktop/Adrian/rabbitMQ



### 2.celery学习

``` text
生产者 ------------>  broker  ---------------> 消费者
										(rabbitMQ)
```



### 3.除合代码学习



### 4.Fastapi框架学习



### 5.python breakpoint(web调试)



### 6.git版本控制

```text
git init: 初始化仓库
工作区 ｜ 暂存区 ｜ 本地仓库
git diff [文件地址] 

git commit：提交至本地仓库

git push -u orgin [分支] ： 提交远程仓库，第一次提交命令

git branch 分支名：创建分支

git checkout 分支名

git merge 分支名： 把分支名合并到当前所在分支

git branch -d 分支名： 删除本地分支

git push 别名 --delete 分支名：删除远程分支

```



### 7.docker-compose



------

## 5.20

### 1.SQLAlchemy

#### 1.1 Difference between ORM and CORE

ORM- domin model (域模型)

CORE- Schema_Centric Model



#### 1.2 Installing

```python
pip instlling sqlalchemy
pip installing flask-sqlalchemy
```



#### 1.3 Initializing

How do I talk my database and what kind of language we're gonna speak? 

```python
import sqlalchemy
from sqlalchemy import create_engine

engine = create_engine('sqlite:///:memory:')
```



#### 1.4 Dinfing a table

```python
from sqlalchemy import Table, Column, Integer, String, MetaData, ForeignKey

metadata = MetaData()
actors = Table('actors', metadata,
              Column('id', Integer, primary_key = True),
              Column('name', Sring),
              Column('fullname', Srting),
              Column('bodycount', Integer))
roles = Table('roles',metadata,
             Column('id',Integer, primary_key = True),
             Column('actor_id', None, ForeignKey = ('actors.id')),
             Column('character_name', String, nullable = False))
```



#### 1.5 Create the table

```python
metadata.create_all(engine)
```



#### 1.6 Table objects

```python
actors.columns.items()

"""
[
('id',Column('id',Integer(),table=actors,primary_key=True...))
...
]
"""
```



#### 1.7 Opening a connection

```python
conn = engine.connect()
```



#### 1.8 Manipulating the data(DML)

Insert data(Single Insert)

```text
.insert().value()
```

Multiple Insert

```python
result = conn.execute(roles.insert(),[
{'data':value,'data_name':value},
{'data':value,'data_name':value},
{'data':value,'data_name':value},
{'data':value,'data_name':value},
])

result.rowcount

#4
```



### 2.Git version contorl (chuhe)

2.1 Checking current branch:

```python
git branch
```

<img src="/Users/zhunanzhou/Desktop/wookbook/assets/WX20220521-175601@2x.png"  />

2.2 Updated code submitting process:

```python
git checkout develop  #switching the develop branch
git status # Showing files's codes which are changed
#git diff apps/services/work.py # compare difference between workspace and staging area
git add apps/services/work.py  # Adding workspace files to staging area
git commit -m "updated(work):目录树修复缩进问题" # Commit files in staging area to local repository
git push  # Push the files in local repository to remote repository
```





## 5.24

### 1.fastapi

- 路径参数
- 请求参数
- 查询参数&字符串校验
  - 导入 `Query`
  - 作为默认值、长度校验、正则校验（regex）
  - 查询参数列表/多个值
  - 别名参数（alias）
  - 弃用参数（deprecated）
- 路径参数&数值校验（Path）
- **请求体**
  - 多个参数（混合使用Path、Query和请求体参数）
  - 字段
  - 嵌套模型（List[str]）
- 响应模型：可以在任意的 *路径操作* 中使用 `response_model` 参数来声明用于响应的模型



#### Pydantic

- 创建数据模型
- 声明为模型

- 总结：
  - 将请求体作为 JSON 读取。
  - 转换为相应的类型（在需要时）。
  - 校验数据。
    - 如果数据无效，将返回一条清晰易读的错误信息，指出不正确数据的确切位置和内容。
  - 将接收的数据赋值到参数item中。
    - 由于你已经在函数中将它声明为 `Item` 类型，你还将获得对于所有属性及其类型的一切编辑器支持（代码补全等）。
  - 为你的模型生成 [JSON 模式](https://json-schema.org/) 定义，你还可以在其他任何对你的项目有意义的地方使用它们。
  - 这些模式将成为生成的 OpenAPI 模式的一部分，并且被自动化文档 UI 所使用。



#### SQLAlchemy

- model

![](/Users/zhunanzhou/Desktop/wookbook/assets/WX20220524-152043.png)

```python
async def receivers(
    db_session: Session,
    user_id: int,
    notice_type: str = 'SYSTEM'
):
    notice_query = db_session.query(Notice)\
        .filter(Notice.deleted_at == None)\
        .filter(Notice.notice_type == notice_type)
    if user_id:
        notice_query = notice_query.filter(Notice.user_id == user_id)

    # notice = db_session.query(Notice).filter(
    #     Notice.user_id == user_id,
    #     Notice.notice_type == notice_type,
    #     Notice.deleted_at == None
    # ).first()

    _user_ids: List[int] = db_session.query(NoticeList.user_id).filter(
        NoticeList.notice_type == notice_type,
        NoticeList.deleted_at == None
    ).all()

    notices: List[Notice] = notice_query.all()
    user_ids = [obj.user_id for obj in notices]
    notice_ids = [obj.notice_id for obj in notices]
    if not notice_ids:
        return get_error_response(400118)

    items = db_session.query(Account)\
        .join(NoticeReceiver, Account.user_id.in_(user_ids))\
        .filter(NoticeReceiver.deleted_at == None)\
        .filter(NoticeReceiver.notice_id.in_(notice_ids))\
        .filter(Account.deleted_at == None)\
        .filter(Account.is_active == True)\
        .filter(Account.user_id.in_(_user_ids))\
        .order_by(NoticeReceiver.receiver_id.desc())\
        .all()

    return GeneralResponse(code=0, data=items)
```









## 5.31 血缘分析需求

dayu job (job文件中是sql)

dws sql

sqllineage：

无法返回报错（后端）





## 6.2 智慧供应链

6.15 项目交付

6.5-6.15  出差





```sql
DELIMITER //
CREATE TRIGGER book_t1 BEFORE INSERT
ON books FOR EACH ROW
BEGIN
INSERT INTO t_log(t_log,t_log_type,t_log_time) VALUE(NEW.name, 'before insert', NOW());
END //
DELIMITER ;

DELIMITER //
CREATE TRIGGER book_t2 BEFORE INSERT
ON books FOR EACH ROW
BEGIN
INSERT INTO t_log(t_log,t_log_type,t_log_time) VALUE(NEW.name, 'after insert', NOW());
END //
DELIMITER ;

```

# -
