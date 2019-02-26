如何使用UUID替代ID作为主键？
================================

不论何时我们创建任何新模型，都会附加着一个ID字段。ID字段的数据类型默认是整型。

为了设置ID字段为uuid，在Django 1.8之后增加了个新字段类型--UUIDField

示例：

.. code:: python

    import uuid
    from django.db import models

    class Event(models.Model):
        id = models.UUIDField(primary_key=True, default=uuid.uuid4, editable=False)
        details = models.TextField()
        years_ago = models.PositiveIntegerField()

    >>> eventobject = Event.objects.all()
    >>> eventobject.first().id
    '3cd2b4b0c36f43488a93b3bb72029f46'
