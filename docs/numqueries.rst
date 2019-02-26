如何断言一个使用了固定查询数量的函数？
========================================================================

我们可以在测试中使用 :code:`assertNumQueries()` 方法来给查询集计数。

.. code:: python

    def test_number_of_queries(self):
        User.objects.create(username='testuser1', first_name='Test', last_name='user1')
        # Above ORM create will run only one query.
        self.assertNumQueries(1)
        User.objects.filter(username='testuser').update(username='test1user')
        # One more query added.
        self.assertNumQueries(2)
