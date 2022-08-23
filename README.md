Django-Chain-PyMySQL
-------------

Easy to use PyMySQL in django.

对 PyMySQL 进行封装，增加链式操作，方便快捷进行 CURD 操作
> 注：基于 Chain-PyMySQL -> https://github.com/Tiacx/chain-pymysql/

<br>


文档目录
----

+ [一、安装说明（INSTALLATION）](#一安装说明installation)
+ [二、连接数据库（CONNECTION）](#二连接数据库connection)
+ [三、简单示例（DEMO）](#三简单示例demo)
+ [四、详细说明（DETAIL）](#四详细说明detail)
+ [五、请我喝奶茶（DONATION）](#五请我喝奶茶donation)

<br>

一、安装说明（INSTALLATION）
----

使用 PIP 安装 或 直接下载源码

* 全自动安装：`easy_install django-chain-pymysql` 或者 `pip install django-chain-pymysql` / `pip3 install django-chain-pymysql`
* 半自动安装：先下载 https://pypi.org/project/django-chain-pymysql/#files ，解压后运行 `python setup.py install`
* 手动安装：将 django-chain-pymysql 目录放置于当前目录或者 site-packages 目录
* 通过 `from django_chain_pymysql import imysql` 来引用

<br>

二、连接数据库（CONNECTION）
----

`自动读取 django 配置并连接数据库，无需手动设置`

注: django 官方文档: https://docs.djangoproject.com/en/4.1/topics/db/multi-db/

<br>

三、简单示例（DEMO）
----

增

```python
from django.http import HttpResponse
from django_chain_pymysql import imysql


def test(request):
    data = request.POST
    insert_id = imysql.table('table1').insert_one(data)

    sql = imysql.get_last_sql()

    return HttpResponse(sql + '<hr>' + str(insert_id))
```

删

```python
from django.http import HttpResponse
from django_chain_pymysql import imysql


def test(request):
    _id = request.GET.get('id')
    effected_rows = imysql.table('table1').delete({'id': _id})

    sql = imysql.get_last_sql()

    return HttpResponse(sql + '<hr>' + str(effected_rows))
```

改

```python
from django.http import HttpResponse
from django_chain_pymysql import imysql


def test1(request):
    _id = request.GET.get('id')
    _name = request.GET.get('name')
    effected_rows = imysql.table('table1').update({'id': _id}, {'name': _name})

    sql = imysql.get_last_sql()

    return HttpResponse(sql + '<hr>' + str(effected_rows))


def test2(request):
    data = request.POST
    _id = data.get('id')
    del data['id']
    effected_rows = imysql.table('table1').update({'id': _id}, data)

    sql = imysql.get_last_sql()

    return HttpResponse(sql + '<hr>' + str(effected_rows))
```

查

```python
import json
from django.http import HttpResponse
from django_chain_pymysql import imysql


def test1(request):
    _id = request.GET.get('id')
    info = imysql.table('table1').where({'id': _id}).one()

    sql = imysql.get_last_sql()

    return HttpResponse(sql + '<hr>' + json.dumps(info, ensure_ascii=False))


def test2(request):
    condition = dict()
    for k, v in request.GET.items():
        if k in ['id', 'name']:
            condition[f't1.{k}'] = v
        elif k in ['age']:
            condition[f't2.{k}'] = v

    where = imysql.gen_condition(condition)
    order_by = imysql.gen_order_by(request.GET.get('order'), request.GET.get('asc') == '1')
    page = int(request.GET.get('page', 1))
    size = int(request.GET.get('size', 10))
    limit = imysql.gen_limit(skip=(page-1)*size, limit=size)
    sql = f'SELECT t1.`name`, avg(t2.age) AS age FROM table1 t1 LEFT JOIN table2 t2 ON t1.id = t2.id WHERE {where} GROUP BY t1.`name` {order_by}{limit}'
    results = imysql.execute(sql, fetch=True)

    return HttpResponse(sql + '<hr>' + json.dumps(results, ensure_ascii=False))
```

事务、多数据库、及其他
> 注：请看详细说明~

<br>

四、详细说明（DETAIL）
----

详细说明请参考：chain-pymysql
> https://github.com/Tiacx/chain-pymysql/

<br>

五、请我喝奶茶（DONATION）
----

> 如果你觉得这个工具对你有帮助或启发，也可以请我喝☕️

![支付宝](alipay.jpg)
