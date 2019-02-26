如何使用Django ORM找到排行第二的记录？
++++++++++++++++++++++++++++++++++++++++++++++++++++++++++

你可能会遇到这种情况--你想要根据年龄或薪资找到其中排在第二的用户（从大到小）。


虽然ORM提供了灵活的 :code:`first()`, :code:`last()` 查询集接口，但并没有任意第几项的查询接口。你可以使用切片来实现。

.. image:: usertable.png

我们可以使用切片操作来找到查询中第几个记录。

.. code-block:: python

    >>> user = User.objects.order_by('-last_login')[1] // Second Highest record w.r.t 'last_login'
    >>> user.first_name
    'Raghu'
    >>> user = User.objects.order_by('-last_login')[2] // Third Highest record w.r.t 'last_login'
    >>> user.first_name
    'Sohan'

:code:`User.objects.order_by('-last_login')[2]` 只是使用 :code:`LIMIT ... OFFSET` 从数据库中获取所需的对象。如果你查看生成的sql语句，你可以看到类似这样的代码：


.. code-block:: sql

    SELECT "auth_user"."id",
           "auth_user"."password",
           "auth_user"."last_login",
           "auth_user"."is_superuser",
           "auth_user"."username",
           "auth_user"."first_name",
           "auth_user"."last_name",
           "auth_user"."email",
           "auth_user"."is_staff",
           "auth_user"."is_active",
           "auth_user"."date_joined"
    FROM "auth_user"
    ORDER BY "auth_user"."last_login" DESC
    LIMIT 1
    OFFSET 2
