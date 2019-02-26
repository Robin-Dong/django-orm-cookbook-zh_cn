如何在Django ORM中使用聚合查询？
========================================

Django ORM中的聚合查询可以通过使用聚合函数完成，如 :code:`Max` , :code:`Min` , :code:`Avg` , :code:`Sum` 等。Django 查询能帮我们增删改查对象。但有时我们需要获取对象的一些汇总值。我们可以通过以下例子得到：

    >>> from django.db.models import Avg, Max, Min, Sum, Count
    >>> User.objects.all().aggregate(Avg('id'))
    {'id__avg': 7.571428571428571}
    >>> User.objects.all().aggregate(Max('id'))
    {'id__max': 15}
    >>> User.objects.all().aggregate(Min('id'))
    {'id__min': 1}
    >>> User.objects.all().aggregate(Sum('id'))
    {'id__sum': 106}
