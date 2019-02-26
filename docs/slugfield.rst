如何使用Django的slug字段提高代码可读性？
========================================

slug 是url的一部分，它以用户可读的形式标识一个网页中的特殊页面。为了能让它工作，Django 为我们提供了slugfield字段。可以和下面一样使用它。我们已经有一个模型 :code:`Article` ，我们要添加一个slugfield使其具有有用户可读性。

.. code:: python

    from django.utils.text import slugify
    class Article(models.Model):
        headline = models.CharField(max_length=100)
        . . .
        slug = models.SlugField(unique=True)

        def save(self, *args, **kwargs):
            self.slug = slugify(self.headline)
            super(Article, self).save(*args, **kwargs)
        . . .

    >>> u1 = User.objects.get(id=1)
    >>> from datetime import date
    >>> a1 = Article.objects.create(headline="todays market report", pub_date=date(2018, 3, 6), reporter=u1)
    >>> a1.save()
    // slug here is auto-generated, we haven't created it in the above create method.
    >>> a1.slug
    'todays-market-report'

Slug字段如此有用是因为：
    - 它是人性化的（如/blog/1 而不是/1/)。
    - 在标题，头部和url中创建一致性是一种不错的SEO。

