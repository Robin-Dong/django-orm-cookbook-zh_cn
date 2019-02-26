Django在对象创建和更新时，会引发哪些信号？
========================================================================

Django 提供了用于模型对象创建和删除周期的信号钩子。这些Django提供的信号是：

- :code:`pre_init`
- :code:`post_init`
- :code:`pre_save`
- :code:`post_save`
- :code:`pre_delete`
- :code:`post_delete`


在这其中最常用的信号是 :code:`pre_save` 和 :code:`post_save` 。我们来仔细看看他们。

信号 vs 重写.save()
-----------------------------------

既然信号和重写 :code:`.save` 有类似的作用，用哪一个是一个时常困惑的问题。这里给出了何时需要用哪一个。

- 如果你想其他人，如第三方应用，重写或定制对象的 :code:`save` 行为，你应该抛出你自己singnals。

- 如果你想要介入一个你无权控制更改的应用中的 :code:`save` 行为，你应该使用 :code:`post_save` 或 :code:`pre_save` 钩子信号。

如果你想定制化你自己的应用的 :code:`save` 行为，你可以重写 :code:`save` 方法。

让我们看一个 :code:`UserToken` 模型的例子。这是一个用于提供身份验证的类，并且应该随 :code:`User` 的创建而创建。

.. code-block:: python

    class UserToken(models.Model):
        token = models.CharField(max_length=64)

        # ...

