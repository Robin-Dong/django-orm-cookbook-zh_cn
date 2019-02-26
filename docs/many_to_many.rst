如何建立多对多的模型关系？
===============================================

多对多的模型关系指当一个数据表中的父级行包含第二表中的多个子行且反之亦然的表与表之间的关系。

为了更好的互动性，我们将聊聊twitter应用。仅仅使用一些字段和多对多关系，我们可以生成一个简单的twitter应用。

基本上我们的推特由三个基础构成，推文，粉丝，喜欢/不喜欢。

我们只要两个模型类就能让这一切工作。我们继承了Django的auth_user。

.. code-block:: python

    class User(AbstractUser):
        tweet = models.ManyToManyField(Tweet, blank=True)
        follower = models.ManyToManyField(settings.AUTH_USER_MODEL, blank=True)
        pass

    class Tweet(models.Model):
        tweet = models.TextField()
        favourite = models.ManyToManyField(settings.AUTH_USER_MODEL, blank=True, related_name='user_favourite')

        def __unicode__(self):
            return self.tweet

以上的模型能做些什么呢？

    1）用户可以关注或取关其他用户。

    2）用户可以他关注的用户的推文。

    3）用户可以喜欢/不喜欢其他人的推文。


使用ManyToManyfield可以完成的部分操作：

.. code-block:: 

    >>> t1 = Tweet(tweet="I am happy today")
    >>> t1.save()
    >>> t2 = Tweet(tweet="This is my second Tweet")
    >>> t2.save()
    >>> u1 = User(username='johny1', first_name='Johny', last_name='Smith', email='johny@example.com')
    >>> u1.save()
    >>> u2 = User(username='johny1', first_name='Johny', last_name='Smith', email='johny@example.com')
    >>> u2.save()
    >>> u3 = User(username='someuser', first_name='Some', last_name='User', email='some@example.com')
    >>> u3.save()


我们新建了一些推文和用户，到目前为止还没有涉及任何多对多关系的使用。我们继续在下一步将他们联系起来。

.. code-block:: 

    >>> u2.tweet.add(t1)
    >>> u2.save()
    >>> u2.tweet.add(t2)
    >>> u2.save()
    // User can follow other users.
    >>> u2.follow.add(u1)
    >>> u2.save()
    // Tweets are linked to the users. Users have folloewd each other. Now we can make users do favourite/unfavourite of the tweets.
    >>> t1.favourite.add(u1)
    >>> t1.save()
    >>> t1.favourite.add(u3)
    >>> t1.save()
    // For removing any users vote
    >>> t1.favourite.remove(u1)
    >>> t1.save()

工作实例可以在这个仓库找到: https://github.com/yashrastogi16/simpletwitter

