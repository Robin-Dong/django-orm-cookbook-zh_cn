如何复制一个现有的模型对象？
========================================================================

Django中没有内建的方法用于复制模型实例，用所有字段的复制值来创建新的模型类是可行的。

如果将一个实例的 :code:`pk` 集设置为None保存实例，那这个实例用于在数据库中创建新记录。这意味着除 :code:`pk` 外的所有字段都被复制了。

.. code-block:: ipython

    In [2]: Hero.objects.all().count()
    Out[2]: 4

    In [3]: hero = Hero.objects.first()

    In [4]: hero.pk = None

    In [5]: hero.save()

    In [6]: Hero.objects.all().count()
    Out[6]: 5


