如果以升序或降序方式给查询集排序？
=============================================================


给查询集排序可以通过 :code:`order_by` 方法来实现。我们需要传入待排序(升序或降序)的字段。查询语句如下：

.. code-block:: python

    >>> User.objects.all().order_by('date_joined') # For ascending
    <QuerySet [<User: yash>, <User: John>, <User: Ricky>, <User: sharukh>, <User: Ritesh>, <User: Billy>, <User: Radha>, <User: Raghu>, <User: rishab>, <User: johny>, <User: paul>, <User: johny1>, <User: alien>]>
    >>> User.objects.all().order_by('-date_joined') # For descending; Not '-' sign in order_by method
    <QuerySet [<User: alien>, <User: johny1>, <User: paul>, <User: johny>, <User: rishab>, <User: Raghu>, <User: Radha>, <User: Billy>, <User: Ritesh>, <User: sharukh>, <User: Ricky>, <User: John>, <User: yash>]>


你可以传入多个字段给 :code:`order_by` 方法。

.. code-block:: python

    User.objects.all().order_by('date_joined', '-last_login')

查看一下SQL语句：

.. code-block:: sql

    SELECT "auth_user"."id",
           -- More fields
           "auth_user"."date_joined"
    FROM "auth_user"
    ORDER BY "auth_user"."date_joined" ASC,
             "auth_user"."last_login" DESC
