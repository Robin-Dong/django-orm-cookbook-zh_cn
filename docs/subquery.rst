如何在Django中使用子查询(内连接)？
=============================================

Django 允许使用SQL子查询，我们有一个 :code:`UserParent` 模型，其中和auth user有一对一的关系。我们要找到所有具有 :code:`UserParent` 字段的 :code:`UserParent` 。

.. code-block:: python

    >>> from django.db.models import Subquery
    >>> users = User.objects.all()
    >>> UserParent.objects.filter(user_id__in=Subquery(users.values('id')))
    <QuerySet [<UserParent: UserParent object (2)>, <UserParent: UserParent object (5)>, <UserParent: UserParent object (8)>]>

现在换点更复杂的，我们想要在每一个 :code:`Category` 找到最勇敢的 :code:`Hero`。

模型类可能如下所示：

.. code-block:: python

    class Category(models.Model):
        name = models.CharField(max_length=100)


    class Hero(models.Model):
        # ...
        name = models.CharField(max_length=100)
        category = models.ForeignKey(Category, on_delete=models.CASCADE)

        benevolence_factor = models.PositiveSmallIntegerField(
            help_text="How benevolent this hero is?",
            default=50
        )

你用以下方式找到最勇敢的英雄：

.. code-block:: python

    hero_qs = Hero.objects.filter(
        category=OuterRef("pk")
    ).order_by("-benevolence_factor")
    Category.objects.all().annotate(
        most_benevolent_hero=Subquery(
            hero_qs.values('name')[:1]
        )
    )

如果你查看生成的sql，你会看到：

.. code-block:: sql

    SELECT "entities_category"."id",
           "entities_category"."name",

      (SELECT U0."name"
       FROM "entities_hero" U0
       WHERE U0."category_id" = ("entities_category"."id")
       ORDER BY U0."benevolence_factor" DESC
       LIMIT 1) AS "most_benevolent_hero"
    FROM "entities_category"


我们来分解下请求集的逻辑。第一步是：

.. code-block:: python

    hero_qs = Hero.objects.filter(
        category=OuterRef("pk")
    ).order_by("-benevolence_factor")

我们正在给 :code:`Hero` 以倒序方式排序，并且使用 :code:`category=OuterRef("pk")` 来声明我们会在子查询中用到它。

然后我们使用 :code:`most_benevolent_hero=Subquery(hero_qs.values('name')[:1])` 以获得 :code:`Category` 查询集的子查询。 :code:`hero_qs.values('name')[:1]` 选取了子查询中的一个名称。


