如何确保只有一个对象被创建？
========================================================================

有时你想要确保每个模型只有一条记录被创建。这常被用来做应用程序配置存储，或作为访问共享资源的锁定机制。

让我们转换我们的 :code:`Origin` 为单例模式。

.. code-block:: python

    class Origin(models.Model):
        name = models.CharField(max_length=100)

        def save(self, *args, **kwargs):
            if self.__class__.objects.count():
                self.pk = self.__class__.objects.first().pk
            super().save(*args, **kwargs)

我们做了什么呢？我们重写了 :code:`save` 方法，并设置 :code:`pk` 为一个存在的值。这确保了当 :code:`create` 方法被调用的同时，如果有其他对象存在，就会抛出一个 :code:`IntegrityError` 。
