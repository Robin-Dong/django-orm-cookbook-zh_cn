如何筛选没有任何文件的FileField字段？
++++++++++++++++++++++++++++++++++++++++++++++

:code:`FileField` 或 :code:`ImageField` 存着文件或图片的路径。在数据库层面他们和 :code:`CharField` 是一样的。

所以我们可以按如下方式查找没有文件的FileField。

.. code-block:: python

    no_files_objects = MyModel.objects.filter(
        Q(file='')|Q(file=None)
    )
