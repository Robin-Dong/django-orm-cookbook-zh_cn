如何创建可与任何实体相关联的通用模型（如类别或评论）？
================================================================================

有模型如下：

.. code-block:: python

    class Category(models.Model):
        name = models.CharField(max_length=100)
        # ...

        class Meta:
            verbose_name_plural = "Categories"


    class Hero(models.Model):
        name = models.CharField(max_length=100)
        category = models.ForeignKey(Category, on_delete=models.CASCADE)
        # ...


    class Villain(models.Model):
        name = models.CharField(max_length=100)
        category = models.ForeignKey(Category, on_delete=models.CASCADE)
        # ...



可被应用的 :code:`Category` 是一个通用模型。你可能想要从任何的模型类应用类别到对象。你可以这样做：


.. code-block:: python

    from django.contrib.contenttypes.fields import GenericForeignKey
    from django.contrib.contenttypes.models import ContentType
    # ...

    class FlexCategory(models.Model):
        name = models.SlugField()
        content_type = models.ForeignKey(ContentType, on_delete=models.CASCADE)
        object_id = models.PositiveIntegerField()
        content_object = GenericForeignKey('content_type', 'object_id')


    class Hero(models.Model):
        name = models.CharField(max_length=100)
        flex_category = GenericRelation(FlexCategory, related_query_name='flex_category')
        # ...


    class Villain(models.Model):
        name = models.CharField(max_length=100)
        flex_category = GenericRelation(FlexCategory, related_query_name='flex_category')
        # ...



我们使用 :code:`ForeignKey` 和 :code:`PositiveIntegerField` 在 :code:`FlexCategory` 添加了 :code:`GenericForeignKey` ，然后在你想要类别化的模型上添加了 :code:`GenericRelation 。

在数据库上类似这样：

.. code-block

         Column      |         Type          |                             Modifiers
    -----------------+-----------------------+--------------------------------------------------------------------
     id              | integer               | not null default nextval('entities_flexcategory_id_seq'::regclass)
     name            | character varying(50) | not null
     object_id       | integer               | not null
     content_type_id | integer               | not null


你可以这样类别化 :code:`Hero`。


.. code-block:: python

    FlexCategory.objects.create(content_object=hero, name="mythic")

然后得到名为ghost的类别化后的 :code:`Hero`.

.. code-block:: python

    FlexCategory.objects.create(content_object=hero, name="ghost")


SQL语句如下

.. code-block:: sql


    SELECT "entities_hero"."name"
    FROM "entities_hero"
    INNER JOIN "entities_flexcategory" ON ("entities_hero"."id" = "entities_flexcategory"."object_id"
                                           AND ("entities_flexcategory"."content_type_id" = 8))
    WHERE "entities_flexcategory"."name" = ghost
