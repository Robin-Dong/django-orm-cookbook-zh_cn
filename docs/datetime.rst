如何将字符串转化为datetime并存入数据库？
============================================================

我们可以使用django中的很多方法来转化一个日期字符串并将其存入数据库。部分方法如下。

假设我们有一个日期字符串如“2018-03-11”，我们不能直接将其存入到我们的date字段，因此我们需要使用日期解析器或是python标准库。

    >>> user = User.objects.get(id=1)
    >>> date_str = "2018-03-11"
    >>> from django.utils.dateparse import parse_date // Way 1
    >>> temp_date = parse_date(date_str)
    >>> a1 = Article(headline="String converted to date", pub_date=temp_date, reporter=user)
    >>> a1.save()
    >>> a1.pub_date
    datetime.date(2018, 3, 11)
    >>> from datetime import datetime // Way 2
    >>> temp_date = datetime.strptime(date_str, "%Y-%m-%d").date()
    >>> a2 = Article(headline="String converted to date way 2", pub_date=temp_date, reporter=user)
    >>> a2.save()
    >>> a2.pub_date
    datetime.date(2018, 3, 11)


