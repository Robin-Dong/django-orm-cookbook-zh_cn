如何基于字段值比较标准来筛选字符集？
==============================================================================

Django orm 让基于固定值的筛选查询更容易。
要获取所有的 :code:`first_name` 以字母 :code:`'R'` 开头的 :code:`User` 对象，
你可以使用 :code:`User.objects.filter(first_name__startswith='R')` 。



那如果你想比较first_name和last name怎么办呢？你可以使用F表达式。首先创建一些users：

.. code-block:: python

    In [27]: User.objects.create_user(email="shabda@example.com", username="shabda", first_name="Shabda", last_name="Raaj")
    Out[27]: <User: shabda>

    In [28]: User.objects.create_user(email="guido@example.com", username="Guido", first_name="Guido", last_name="Guido")
    Out[28]: <User: Guido>


现在你可以找到 :code:`first_name==last_name` 的用户了。

.. code-block:: python

    In [29]: User.objects.filter(last_name=F("first_name"))
    Out[29]: <QuerySet [<User: Guido>]>


:code:`F` 表达式也可以使用聚合函数来计算某些字段。如果我们想要first name 和last name字母都相同的用户怎么办？


你可以通过使用 :code:`Substr("first_name", 1, 1)` 设置首字母：

.. code-block:: python

    In [41]: User.objects.create_user(email="guido@example.com", username="Tim", first_name="Tim", last_name="Teters")
    Out[41]: <User: Tim>
    #...
    In [46]: User.objects.annotate(first=Substr("first_name", 1, 1), last=Substr("last_name", 1, 1)).filter(first=F("last"))
    Out[46]: <QuerySet [<User: Guido>, <User: Tim>]>

:code:`F` 表达式也可以和 :code:`__gt` ,  :code:`__lt` 以及其他表达式一起使用。

