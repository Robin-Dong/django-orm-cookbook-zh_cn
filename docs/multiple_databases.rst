如果添加多个数据到Django应用？
=====================================

数据库配置相关的东西基本上在 :code:`settings.py` 中被完成。所以，要给Django应用添加多个数据库，我们需要把他们添加到 :code:`DATABASES` 的字典中。

.. code:: python

    DATABASE_ROUTERS = ['path.to.DemoRouter']
    DATABASE_APPS_MAPPING = {'user_data': 'users_db',
                            'customer_data':'customers_db'}

    DATABASES = {
        'default': {
            'ENGINE': 'django.db.backends.sqlite3',
            'NAME': os.path.join(BASE_DIR, 'db.sqlite3'),
        },
        'users_db': {
            'NAME': 'user_data',
            'ENGINE': 'django.db.backends.postgresql',
            'USER': 'postgres_user',
            'PASSWORD': 'password'
        },
        'customers_db': {
            'NAME': 'customer_data',
            'ENGINE': 'django.db.backends.mysql',
            'USER': 'mysql_cust',
            'PASSWORD': 'root'
        }
    }


对于多个数据库，我们最好聊聊 :code:`Database Router` 。默认的路由方案如果一个数据库未指明，所有的查询都会返回到默认数据库。 :code:`Database Router` 默认是 :code:`[]` 。

.. code:: python

    class DemoRouter:
        """
        A router to control all database operations on models in the
        user application.
        """
        def db_for_read(self, model, **hints):
            """
            Attempts to read user models go to users_db.
            """
            if model._meta.app_label == 'user_data':
                return 'users_db'
            return None

        def db_for_write(self, model, **hints):
            """
            Attempts to write user models go to users_db.
            """
            if model._meta.app_label == 'user_data':
                return 'users_db'
            return None

        def allow_relation(self, obj1, obj2, **hints):
            """
            Allow relations if a model in the user app is involved.
            """
            if obj1._meta.app_label == 'user_data' or \
               obj2._meta.app_label == 'user_data':
               return True
            return None

        def allow_migrate(self, db, app_label, model_name=None, **hints):
            """
            Make sure the auth app only appears in the 'users_db'
            database.
            """
            if app_label == 'user_data':
                return db == 'users_db'
            return None

各个模型将被修改为：

.. code:: python

    class User(models.Model):
        username = models.Charfield(ax_length=100)
        . . .
            class Meta:
            app_label = 'user_data'

    class Customer(models.Model):
        name = models.TextField(max_length=100)
        . . .
            class Meta:
            app_label = 'customer_data'

使用多个数据库是的有用的命令:

.. code:: bash

    $ ./manage.py migrate --database=users_db
