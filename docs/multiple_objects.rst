如何一次创建多个对象？
====================================


如果我们想要一次保存多个对象这里有些条件。假如我们想一次添加多个类别并且我们不想过多的请求数据库。我们可以使用 :code:`bulk_create` 来一次创建多个对象。

示例如下。

.. code-block:: :python

    >>> Category.objects.all().count()
    2
    >>> Category.objects.bulk_create(
        [Category(name="God"),
         Category(name="Demi God"),
         Category(name="Mortal")]
    )
    [<Category: God>, <Category: Demi God>, <Category: Mortal>]
    >>> Category.objects.all().count()
    5

:code:`bulk_create` 接收一个未保存对象的列表。