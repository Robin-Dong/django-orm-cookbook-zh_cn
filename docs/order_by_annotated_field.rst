如何给注解字段排序？
==========================================

有两个模型 :code:`Category` 和 :code:`Hero`：

.. code-block:: python

    class Category(models.Model):
        name = models.CharField(max_length=100)


    class Hero(models.Model):
        # ...
        name = models.CharField(max_length=100)
        category = models.ForeignKey(Category, on_delete=models.CASCADE)

如果想要获取按 :code:`Hero` 数量排序的 :code:`Category` 。你可以这样做：

.. code-block:: python

    Category.objects.annotate(
        hero_count=Count("hero")
    ).order_by(
        "-hero_count"
    )


