如何使用Q对象进行复杂查询？
==================================================

在之前的章节里我们使用了 :code:`Q` 对象来做 :code:`OR` 、 :code:`AND` 和 :code:`NOT` 运算。:code:`Q` 对象给你在where查询上绝对的控制权。

如果你想要对查询条件使用 :code:`OR` 。

.. code-block:: python

    >>> from django.db.models import Q
    >>> queryset = User.objects.filter(
        Q(first_name__startswith='R') | Q(last_name__startswith='D')
    )
    >>> queryset
    <QuerySet [<User: Ricky>, <User: Ritesh>, <User: Radha>, <User: Raghu>, <User: rishab>]>

如果你想对你的查询条件使用 :code:`AND` 。

.. code-block:: ipython

    >>> queryset = User.objects.filter(
        Q(first_name__startswith='R') & Q(last_name__startswith='D')
    )
    >>> queryset
    <QuerySet [<User: Ricky>, <User: Ritesh>, <User: rishab>]>


如果你想要找到所有 :code:`first_name` 以R开始且 :code:`last_name` 不是以Z开始的用户。

.. code-block:: python

    >>> queryset = User.objects.filter(
        Q(first_name__startswith='R') & ~Q(last_name__startswith='Z')
    )


查看生成的查询语句，你会看到：

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
    WHERE ("auth_user"."first_name"::text LIKE R%
           AND NOT ("auth_user"."last_name"::text LIKE Z%))


你可以以更复杂的方式组合Q对象来生成更复杂的查询。
