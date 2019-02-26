如何从模型中有效地选取一个随机对象？
========================================================================

:code:`category` 模型类如下。

.. code-block:: python

    class Category(models.Model):
        name = models.CharField(max_length=100)

        class Meta:
            verbose_name_plural = "Categories"

        def __str__(self):
            return self.name


你想要获取一个随机的分类。我们来看下几种替代方法。


最直接的方法是你可以用随机的 :code:`order_by` 然后获取第一条记录。类似这样：

.. code-block:: python

    def get_random():
        return Category.objects.order_by("?").first()

注意：:code:`order_by('?')` 查询可能会性能很差，这取决于你在使用的数据库后端。

.. code-block:: sql

    INSERT INTO entities_category
                (name)
    (SELECT Md5(Random() :: text) AS descr
     FROM   generate_series(1, 1000000));

你不需要理解以上SQL的所有细节，它创建了一百万个数字并使用 :code:`md5-s` 来生成名称，然后将其插入到数据库中。

现在，你可以得到最大的id，生成一个范围在[1, max_id]中间的随机数，然后获取到数据而不需要给整张表排序（假设表中没有被删除的记录）。

.. code-block:: python

    In [1]: from django.db.models import Max

    In [2]: from entities.models import Category

    In [3]: import random

    In [4]: def get_random2():
       ...:     max_id = Category.objects.all().aggregate(max_id=Max("id"))['max_id']
       ...:     pk = random.randint(1, max_id)
       ...:     return Category.objects.get(pk=pk)
       ...:

    In [5]: get_random2()
    Out[5]: <Category: e2c3a10d3e9c46788833c4ece2a418e2>

    In [6]: get_random2()
    Out[6]: <Category: f164ad0c5bc8300b469d1c428a514cc1>

如果你的模型中有删除的纪录，你可稍微调整下函数，循环直到你获取到有效的 :code:`Category` 。

.. code-block:: python

    In [8]: def get_random3():
       ...:     max_id = Category.objects.all().aggregate(max_id=Max("id"))['max_id']
       ...:     while True:
       ...:         pk = random.randint(1, max_id)
       ...:         category = Category.objects.filter(pk=pk).first()
       ...:         if category:
       ...:             return category
       ...:

    In [9]: get_random3()
    Out[9]: <Category: 334aa9926bd65dc0f9dd4fc86ce42e75>

    In [10]: get_random3()
    Out[10]: <Category: 4092762909c2c034e90c3d2eb5a73447>


:code:`while True:` 循环会迅速返回，除非你的模型有大量的删除。我们使用 :code:`timeit` 来看看差别。

.. code-block:: python

    In [14]: timeit.timeit(get_random3, number=100)
    Out[14]: 0.20055226399563253

    In [15]: timeit.timeit(get_random, number=100)
    Out[15]: 56.92513192095794


:code:`get_random3` 大概比 :code:`get_random` 快了283倍。 :code:`get_random` 是最通用的方式，除非你更改了Django生成id的默认方式--自增整数，或者有过太多的删除记录， :code:`get_random3` 将会生效。

