如何从相同或不同的模型类中联合两个查询集结果？
++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++

UNION 运算符被用于联合两个或多个查询的结果集。
查询集可以来自相同或不同的模型类。如果查询集来自不同的模型，需要保证字段和数据类型相匹配。

让我们继续使用 :code:`auth_user` 模型生成两组查询集：

.. code-block:: python

    >>> q1 = User.objects.filter(id__gte=5)
    >>> q1
    <QuerySet [<User: Ritesh>, <User: Billy>, <User: Radha>, <User: sohan>, <User: Raghu>, <User: rishab>]>
    >>> q2 = User.objects.filter(id__lte=9)
    >>> q2
    <QuerySet [<User: yash>, <User: John>, <User: Ricky>, <User: sharukh>, <User: Ritesh>, <User: Billy>, <User: Radha>, <User: sohan>, <User: Raghu>]>
    >>> q1.union(q2)
    <QuerySet [<User: yash>, <User: John>, <User: Ricky>, <User: sharukh>, <User: Ritesh>, <User: Billy>, <User: Radha>, <User: sohan>, <User: Raghu>, <User: rishab>]>
    >>> q2.union(q1)
    <QuerySet [<User: yash>, <User: John>, <User: Ricky>, <User: sharukh>, <User: Ritesh>, <User: Billy>, <User: Radha>, <User: sohan>, <User: Raghu>, <User: rishab>]>

现在试试这个：

.. code-block:: python

    >>> q3 = EventVillain.objects.all()
    >>> q3
    <QuerySet [<EventVillain: EventVillain object (1)>]>
    >>> q1.union(q3)
    django.db.utils.OperationalError: SELECTs to the left and right of UNION do not have the same number of result columns


union操作只能在查询集拥有相同的字段和数据类型前提下执行。因此我们最后一次union操作出现错误。你可以两个models上执行union，只要他们有相同字段或相同字段子集。

由于 :code:`Hero` 和 :code:`Villain` 两个都有 :code:`name` 和 :code:`name`，我们可以使用 :code:`values_list` 来限制所选字段，然后执行union操作。

.. code-block:: python

    Hero.objects.all().values_list(
        "name", "gender"
    ).union(
    Villain.objects.all().values_list(
        "name", "gender"
    ))

这会返回给你所有的 :code:`Hero` 和 :code:`Villain` 对象的名字和性别。
