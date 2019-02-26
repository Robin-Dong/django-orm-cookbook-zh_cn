如何选择同一查询集中的某些字段？
++++++++++++++++++++++++++++++++++++++++++++++++++

.. image:: usertable.png

:code:`auth_user` 模型中有一些字段，但有时，你不需要使用所有的字段，这样的话，我们可以只请求期望的字段。

Django提供了两种方法实现

- 查询集的 `values` 和 `values_list` 方法
-  `only` 方法


比如我们想要获取姓名以R开头的所有用户的 :code:`first_name` 和 :code:`last_name` 。为减少数据的工作量，你并不想要获取其他字段。

.. code-block:: python

    >>> User.objects.filter(
        first_name__startswith='R'
    ).values('first_name', 'last_name')
    <QuerySet [{'first_name': 'Ricky', 'last_name': 'Dayal'}, {'first_name': 'Ritesh', 'last_name': 'Deshmukh'}, {'first_name': 'Radha', 'last_name': 'George'}, {'first_name': 'Raghu', 'last_name': 'Khan'}, {'first_name': 'Rishabh', 'last_name': 'Deol'}]

你可以使用 :code:`str(queryset.query)` 验证生成的SQL语句：

.. code-block:: sql

    SELECT "auth_user"."first_name", "auth_user"."last_name"
    FROM "auth_user" WHERE "auth_user"."first_name"::text LIKE R%

输出会是字典元素的列表。

另外，你也可以使用：

.. code-block:: python

    >> queryset = User.objects.filter(
        first_name__startswith='R'
    ).only("first_name", "last_name")


:code:`str(queryset.query)` 生成的是：

.. code-block:: python

    SELECT "auth_user"."id", "auth_user"."first_name", "auth_user"."last_name"
    FROM "auth_user" WHERE "auth_user"."first_name"::text LIKE R%

:code:`only` 和 :code:`values` 唯一的不同是 :code:`only` 还会获取相应的 :code:`id`。