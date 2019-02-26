如何从查询集中找到独一无二的字段值？
========================================================================


.. image:: usertable2.png

你想要查找名字没有重复的用户，你可以这样做

.. code-block:: python

    distinct = User.objects.values(
        'first_name'
    ).annotate(
        name_count=Count('first_name')
    ).filter(name_count=1)
    records = User.objects.filter(first_name__in=[item['first_name'] for item in distinct])

这个和 :code:`User.objects.distinct("first_name").all()` 不同， :code:`User.objects.distinct("first_name").all()` 会获取遇到的不同的first_name时的第一条记录。(注意部分数据库如MySQL，distinct用法是 :code:`User.objects.value("first_name").distinct()`)
