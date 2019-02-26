如何以不区分大小写的方式排序查询集？
============================================================

.. image:: usertable2.png


无论何时我们想要尝试通过一些字符串来排序，排序总是通过字母顺序和基于大小写的。例如：

.. code-block:: python

    >>> User.objects.all().order_by('username').values_list('username', flat=True)
    <QuerySet ['Billy', 'John', 'Radha', 'Raghu', 'Ricky', 'Ritesh', 'johny', 'johny1', 'paul', 'rishab', 'sharukh', 'sohan', 'yash']>

如果我们想要不区分大小写的方式排序。我们可以这样做。

.. code-block:: python

    >>> from django.db.models.functions import Lower
    >>> User.objects.all().order_by(Lower('username')).values_list('username', flat=True)
    <QuerySet ['Billy', 'John', 'johny', 'johny1', 'paul', 'Radha', 'Raghu', 'Ricky', 'rishab', 'Ritesh', 'sharukh', 'sohan', 'yash']>


另外，我们可以用 :code:`Lower` 注释然后将其排序。

.. code-block:: python

    User.objects.annotate(
        uname=Lower('username')
    ).order_by('uname').values_list('username', flat=True)
