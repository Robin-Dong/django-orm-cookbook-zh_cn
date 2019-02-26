如何在保存时更新其他模型中的非规范化字段？
========================================================================

模型类如下所示：

.. code-block:: python

    class Category(models.Model):
        name = models.CharField(max_length=100)
        hero_count = models.PositiveIntegerField()
        villain_count = models.PositiveIntegerField()

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


当新对象被创建，你需要 :code:`hero_count` 和 :code:`villain_count` 字段被更新。

你可以做如下操作

.. code-block:: python

    class Hero(models.Model):
        # ...

        def save(self, *args, **kwargs):
            if not self.pk:
                Category.objects.filter(pk=self.category_id).update(hero_count=F('hero_count')+1)
            super().save(*args, **kwargs)


    class Villain(models.Model):
        # ...

        def save(self, *args, **kwargs):
            if not self.pk:
                Category.objects.filter(pk=self.category_id).update(villain_count=F('villain_count')+1)
            super().save(*args, **kwargs)



注意到我们并没有使用 :code:`self.category.hero_count += 1` ，因为 :code:`update` 会做一次数据库更新。

另一个方法是使用`signals`，你可以这样做：

.. code-block:: python

    from django.db.models.signals import pre_save
    from django.dispatch import receiver

    @receiver(pre_save, sender=Hero, dispatch_uid="update_hero_count")
    def update_hero_count(sender, **kwargs):
        hero = kwargs['instance']
        if hero.pk:
            Category.objects.filter(pk=hero.category_id).update(hero_count=F('hero_count')+1)

    @receiver(pre_save, sender=Villain, dispatch_uid="update_villain_count")
    def update_villain_count(sender, **kwargs):
        villain = kwargs['instance']
        if villain.pk:
            Category.objects.filter(pk=villain.category_id).update(villain_count=F('villain_count')+1)


信号 vs 重写 :code:`.save`
++++++++++++++++++++++++++++++++++++

既然 :code:`signals` 和 :code:`.save` 都可以被用保存行为，那什么时候你该用哪一个？

- 如果你的字段依赖模型是由你控制的，使用重写 :code:`.save` 。
- 如果你的字段依赖模型来自第三方应用，你不能控制，使用 :code:`signal`。

