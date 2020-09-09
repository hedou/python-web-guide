.. _mysql:

=============
Mysql
=============


如何批量 kill 掉查询
=====================================================================

有时候需要批量 kill 掉查询进程，有几种方式，比如生成 sql 文件执行：

.. code-block:: sql

    mysql> select concat('KILL ',id,';') from information_schema.processlist
    where user='root' and time > 200 into outfile '/tmp/a.txt';

    mysql> source /tmp/a.txt;


直接生成 sql 语句 copy 下来复制到命令行也可以，或者连接成一行方便复制：

.. code-block:: sql

    mysql > select concat('KILL ',id,';') from information_schema.processlist where db='dbname';`

    mysql > select GROUP_CONCAT(stat SEPARATOR ' ') from (select concat('KILL ',id,';') as stat from information_schema.processlist where db='dbname') as stats;

也可以通过 python 脚本来完成：

.. code-block:: py

    # https://stackoverflow.com/questions/1903838/how-do-i-kill-all-the-processes-in-mysql-show-processlist

    import pymysql # pip install pymysql

    connection = pymysql.connect(host='',
                                 user='',
                                 db='',
                                 password='',
                                 cursorclass=pymysql.cursors.DictCursor)

    with connection.cursor() as cursor:
        cursor.execute('SHOW PROCESSLIST')
        for item in cursor.fetchall():
            if item.get('db') == 'dbname': # 过滤条件
                _id = item.get('Id')
                print('kill %s' % item)
                cursor.execute('kill %s', _id)
        connection.close()
