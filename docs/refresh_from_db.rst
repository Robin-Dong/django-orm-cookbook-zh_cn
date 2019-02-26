如何从数据库中重载一个模型对象？
========================================================================

通过使用 :code:`refresh_from_db()` 方法，模型就可以从数据库中被从新加载。这在测试中非常有用。例如：

.. code:: python

    class TestORM(TestCase):
        def test_update_result(self):
            userobject = User.objects.create(username='testuser', first_name='Test', last_name='user')
            User.objects.filter(username='testuser').update(username='test1user')
            # At this point userobject.val is still testuser, but the value in the database
            # was updated to test1user. The object's updated value needs to be reloaded
            # from the database.
            userobject.refresh_from_db()
            self.assertEqual(userobject.username, 'test1user')
