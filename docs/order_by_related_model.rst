如何给关联模型(使用外键)的一个字段排序？
========================================================================

有两个模型类，:code:`Category` 和 :code:`Hero` 。

.. code-block:: python

    class Category(models.Model):
        name = models.CharField(max_length=100)


    class Hero(models.Model):
        # ...
        name = models.CharField(max_length=100)
        category = models.ForeignKey(Category, on_delete=models.CASCADE)

你如果想要通过类别和英雄名称来给每个类别下的英雄排序。你可以这样做：

.. code-block:: python

    Hero.objects.all().order_by(
        'category__name', 'name'
    )

注意 :code:`'category__name'` 中的双下划线。使用双下划线你可以给关联模型的一个字段排序。

查看SQL如下：

.. code-block:: sql

    SELECT "entities_hero"."id",
           "entities_hero"."name",
           -- more fields
    FROM "entities_hero"
    INNER JOIN "entities_category" ON ("entities_hero"."category_id" = "entities_category"."id")
    ORDER BY "entities_category"."name" ASC,
             "entities_hero"."name" ASC
