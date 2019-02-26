如何在模型中包含自引用外键？
========================================================================

自引用外键被用于模型嵌套关系和递归关系的建立。他们的工作方式和一对多关系类似。但顾名思义，这个模型引用了自身。

自引用外键可以有两种方法实现。

.. code-block:: python


    class Employee(models.Model):
        manager = models.ForeignKey('self', on_delete=models.CASCADE)

    # 或者

    class Employee(models.Model):
        manager = models.ForeignKey("app.Employee", on_delete=models.CASCADE)

