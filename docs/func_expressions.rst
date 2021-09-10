如何在查询集中使用任意的数据库函数？
========================================================================

Django 有 :code:`Lower` ,  :code:`Coalesce` 和 :code:`Max` 等函数，但它并不支持所有的数据库函数，尤其是数据库特有的函数。


Django 提供了 :code:`Func` 函数，它可以允许你任意使用甚至Django没有提供的数据库函数。


Postgres 有个 :code:`fuzzystrmatch` 模块，它提供了几种确定相似性的函数。使用 :code:`create extension fuzzystrmatch` 在你的postgres数据库上安装这个插件。


我们将使用 :code:`levenshtein` 函数。先让我们创建一些Hero对象吧。

.. code-block:: python


    Hero.objects.create(name="Zeus", description="A greek God", benevolence_factor=80, category_id=12, origin_id=1)
    Hero.objects.create(name="ZeuX", description="A greek God", benevolence_factor=80, category_id=12, origin_id=1)
    Hero.objects.create(name="Xeus", description="A greek God", benevolence_factor=80, category_id=12, origin_id=1)
    Hero.objects.create(name="Poseidon", description="A greek God", benevolence_factor=80, category_id=12, origin_id=1)


我们想要找出名字和Zeus相似的 :code:`Hero` 对象。可以这样做：

.. code-block:: python

    from django.db.models import Func, F
    Hero.objects.annotate(like_zeus=Func(F('name'), function='levenshtein', template="%(function)s(%(expressions)s, 'Zeus')"))

:code:`like_zeus=Func(F('name'), function='levenshtein', template="%(function)s(%(expressions)s, 'Zeus')")` 接收两个允许数据库展示的参数，即 :code:`function` 和 :code:`template`。如果你需要重复使用这个函数，你可以和下面一样定义一个类方法。

.. code-block:: python

    class LevenshteinLikeZeus(Func):
        function='levenshtein'
        template="%(function)s(%(expressions)s, 'Zeus')"


使用 :code:`Hero.objects.annotate(like_zeus=LevenshteinLikeZeus(F("name")))`,
然后你就可以像下面这样筛选这个标注字段。

.. code-block:: python

    In [16]: Hero.objects.annotate(
        ...:         like_zeus=LevenshteinLikeZeus(F("name"))
        ...:     ).filter(
        ...:         like_zeus__lt=2
        ...:     )
        ...:
    Out[16]: <QuerySet [<Hero: Zeus>, <Hero: ZeuX>, <Hero: Xeus>]>
