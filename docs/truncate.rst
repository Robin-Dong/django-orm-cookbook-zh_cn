如何使用Django ORM执行类似truncate操作？
==========================================================

SQL中truncate语句用于清空一张表为以后使用。
虽然Django并没有提供内建函数来清空一张表，但还是可以使用 :code:`delete()` 得到类似的结果。

例如：

.. code-block:: python

    >>> Category.objects.all().count()
    7
    >>> Category.objects.all().delete()
    (7, {'entity.Category': 7})
    >>> Category.objects.all().count()
    0


这个语句能起作用，但它使用SQL中 :code:`DELETE FROM ...` 语句。如果你有大量的数据，这将会非常慢。如果你想要激活 :code:`truncate` ，你可以给 :code:`Category` 添加一个 :code:`classmethod` 。


.. code-block:: python

    class Category(models.Model):
        # ...

        @classmethod
        def truncate(cls):
            with connection.cursor() as cursor:
                cursor.execute('TRUNCATE TABLE "{0}" CASCADE'.format(cls._meta.db_table))

然后你可以调用 :code:`Category.truncate()` 来实现真正的数据库truncate。
