.. _mysql:

=============
Mysql
=============


数据库操作
=====================================================================

批量 kill 掉查询
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

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

删除大表
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

.. code-block:: sql

    # https://stackoverflow.com/questions/879327/quickest-way-to-delete-enormous-mysql-table
    CREATE TABLE new_foo LIKE foo;
    RENAME TABLE foo TO old_foo, new_foo TO foo;
    DROP TABLE old_foo;

统计表的大小并排序
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

.. code-block:: sql

    # https://stackoverflow.com/questions/9620198/how-to-get-the-sizes-of-the-tables-of-a-mysql-database
    SELECT
         table_schema as `Database`,
         table_name AS `Table`,
         round(((data_length + index_length) / 1024 / 1024), 2) `Size in MB`
    FROM information_schema.TABLES
    ORDER BY (data_length + index_length) DESC;

查看表信息
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

.. code-block:: sql

    mysql > show table status;
    mysql > show table status where Rows>100000;
