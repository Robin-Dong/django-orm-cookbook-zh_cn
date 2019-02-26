如何给两个字段排序？
========================================================================

查询集的 :code:`order_by` 方法可以接收一个或多个属性名，也就允许你可以给两个或多个字段排序。

.. code-block:: python

    In [5]: from django.contrib.auth.models import User

    In [6]: User.objects.all().order_by("is_active", "-last_login", "first_name")
    Out[6]: <QuerySet [<User: Guido>, <User: shabda>, <User: Tim>]>

