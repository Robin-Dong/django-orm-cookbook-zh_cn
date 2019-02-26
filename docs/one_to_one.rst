如何建立一对一的关系模型？
===============================================

当第一个表中的只有一条记录和关联表中的一条记录想对应时就会产生一对一的关系。

这里我们有一个例子，我们都知道每个人都只能有一个亲生父母，即父亲和母亲。

我们已有了auth用户模型，我们将添加一个新的UserParent模型，如下所示。

.. code-block:: python

    from django.contrib.auth.models import User

    class UserParent(models.Model):
        user = models.OneToOneField(
            User,
            on_delete=models.CASCADE,
            primary_key=True,
        )
        father_name = models.CharField(max_length=100)
        mother_name = models.CharField(max_length=100)

    >>> u1 = User.objects.get(first_name='Ritesh', last_name='Deshmukh')
    >>> u2 = User.objects.get(first_name='Sohan', last_name='Upadhyay')
    >>> p1 = UserParent(user=u1, father_name='Vilasrao Deshmukh', mother_name='Vaishali Deshmukh')
    >>> p1.save()
    >>> p1.user.first_name
    'Ritesh'
    >>> p2 = UserParent(user=u2, father_name='Mr R S Upadhyay', mother_name='Mrs S K Upadhyay')
    >>> p2.save()
    >>> p2.user.last_name
    'Upadhyay'


on_delete 方法被用于告诉Django如何处理依赖你删除模型实例的模型实例（如外键关系）。:code:`on_delete=models.CASCADE` 告诉Django执行级联删除效果，即，继续也把依赖模型删除。

    >>> u2.delete()

并将删除 :code:`UserParent` 的关联记录。
