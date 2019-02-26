:code:`null=True` 和 :code:`blank=True` 的区别？
====================================================================

:code:`null` 和 :code:`blank` 的默认值都是 :code:`False` 。他们两者的值都在字段级别工作，即，我们是否要保持字段null还是blank。

:code:`null=True` 将会设置字段的值为空，即没有数据。这基本上用于数据库列值。

.. code:: python

    date = models.DateTimeField(null=True)

:code:`blank=True` 决定在表格中是否需要该字段。这包含了admin表单和你自己的表单。

.. code:: python

    title = models.CharField(blank=True) // title can be kept blank. In the database ("") will be stored.

:code:`null=True` :code:`blank=True` 这意味着这个字段在所有情况下是可选的。

    epic = models.ForeignKey(null=True, blank=True)
    // The exception is CharFields() and TextFields(), which in Django are never saved as NULL. Blank values are stored in the DB as an empty string ('').

但也有个特殊例子，但你需要接受一个 :code:`BooleanField` 的Null值时，请使用 :code:`NullBooleanField` 。