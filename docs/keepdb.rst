如何通过在测试运行中重用数据库来加速测试？
================================================================

当我们执行命令 :code:`python manage.py test` ，一个新的数据库每次会被创建。如果我们没有太多迁移，这并没有什么问题。
但是当我们有许多迁移时，这将耗费大量的时间在测试运行时来重新创建数据库。为了避免这种清况，我们可以重用久数据库。

你可以通过添加 :code:`--keepdb` 标识到测试命令后以防止数据库被破坏。这将保存在测试过程中保存测试数据库。如果数据库不存在，它首先会被创建。如果有任何迁移在最后一次测试运行后被添加，为保证同步，这些迁移会被应用。

.. code-block:: bash

    $ python manage.py test --keepdb

