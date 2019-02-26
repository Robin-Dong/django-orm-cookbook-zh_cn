如何建立一对多的关系模型？
===============================================

在关系型数据库，当一个表的父级记录引用了多个其他的子记录时, 会产生一对多关系。在这种关系中，父级记录不要求有子记录。因此一对多关系允许零个，单个或多个子记录。

定义一对多的关系，使用 `ForeignKey` ：

.. code-block:: python

    class Article(models.Model):
        headline = models.CharField(max_length=100)
        pub_date = models.DateField()
        reporter = models.ForeignKey(User, on_delete=models.CASCADE, related_name='reporter')

        def __str__(self):
            return self.headline

        class Meta:
            ordering = ('headline',)

    >>> u1 = User(username='johny1', first_name='Johny', last_name='Smith', email='johny@example.com')
    >>> u1.save()
    >>> u2 = User(username='alien', first_name='Alien', last_name='Mars', email='alien@example.com')
    >>> u2.save()
    >>> from datetime import date
    >>> a1 = Article(headline="This is a test", pub_date=date(2018, 3, 6), reporter=u1)
    >>> a1.save()
    >>> a1.reporter.id
    13
    >>> a1.reporter
    <User: johny1>

如果你想要在保存对象前使用它，你会得到ValueError错误：

    >>> u3 = User(username='someuser', first_name='Some', last_name='User', email='some@example.com')
    >>> Article.objects.create(headline="This is a test", pub_date=date(2018, 3, 7), reporter=u1)
    Traceback (most recent call last):
    ...
    ValueError: save() prohibited to prevent data loss due to unsaved related object 'reporter'.
    >>> Article.objects.create(headline="This is a test", pub_date=date(2018, 3, 7), reporter=u1)
    >>> Article.objects.filter(reporter=u1)
    <QuerySet [<Article: This is a test>, <Article: This is a test>]>

以上请求集展示了有多篇 :code:`Articles` 的用户U1，也就是一对多。

