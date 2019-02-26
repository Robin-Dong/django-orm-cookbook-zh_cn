如何查看Django ORM查询集的原生SQL？
++++++++++++++++++++++++++++++++++++++++++++++++++

有时你想要知道Django ORM是怎样执行我们的查询的或是我们所写的orm语句对应的SQL语句是什么。这很容易得到，你可以在queryset.query对象上使用 :code:`str` 方法得到SQL语句。

有一个模型类Event，你可以通过Event.objects.all()来获取所有的记录，然后再执行 :code:`str(queryset.query)`。

.. code-block:: python

    >>> queryset = Event.objects.all()
    >>> str(queryset.query)
    SELECT "events_event"."id", "events_event"."epic_id",
        "events_event"."details", "events_event"."years_ago"
        FROM "events_event"

.. image:: sql_query.png

示例 2

.. code-block:: python

    >>> queryset = Event.objects.filter(years_ago__gt=5)
    >>> str(queryset.query)
    SELECT "events_event"."id", "events_event"."epic_id", "events_event"."details",
    "events_event"."years_ago" FROM "events_event"
    WHERE "events_event"."years_ago" > 5

