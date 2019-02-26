如何指定模型的表名？
===============================================

为了节省你的时间，Django自动从你的模型类和包含它的应用中为你生成了数据表的名字。模型数据表的名字是由模型的应用名(新建应用时的名字)和模型类的名字组成，中间由下划线连接。

我们有两个应用在我们的示例应用里，如 :code:`entities` 和 :code:`events` ，所以他们中的所有模型将有如同"app名前缀_模型名"这样的应用名

.. image:: db_table.png

从新命名我们可以使用 :code:`db_table` 参数：

.. code:: python

    class TempUser(models.Model):
        first_name = models.CharField(max_length=100)
        . . .
        class Meta:
            db_table = "temp_user"