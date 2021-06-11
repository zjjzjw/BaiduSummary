# EngineManager
``` python
#!/usr/bin/env python
# -*- coding: utf-8 -*-
"""
Copyright (c) 2021 Baidu.com, Inc. All Rights Reserved
This module provide configure file management service in i18n environment.

Authors: dingwei(v_dingwei@baidu.com)
"""
import sys
import os

from sqlalchemy import create_engine # 数据库的操作方法
from configparser import RawConfigParser


class EngineManager(object):
    """
    获取各种连接引擎 如mysql
    """
    def __init__(self, engine_name):
        """
        :param engine_name: 引擎名称
        :return:
        """
        self.engine_name = engine_name

    def get(self):
        if self.engine_name == 'mysql':
            return self.get_mysql_engine()
        else:
            print("no such engine: " + self.engine_name)
            sys.exit("engine name error")

    def get_mysql_engine(self):
        mode = os.getenv("DEV_MODE", "local")
        if mode not in ('local', 'test'):  # 配置文件中只配置了local和test，其他环境需要取环境变量
            mode = 'test'  # 默认用test的配置填充
        conf_name = "mysql-" + mode
        cf = RawConfigParser()
        path = os.path.dirname(os.path.dirname(os.path.abspath(__file__)))
        cf.read(path + "/conf/app.conf", encoding="utf-8")
        engine = create_engine('mysql+pymysql://%s:%s@%s:%s/%s' % (
            os.getenv("SECURITY_DB_USERNAME", cf.get(conf_name, 'user')),
            os.getenv("SECURITY_DB_PASSWORD", cf.get(conf_name, 'password')),
            os.getenv("SECURITY_DB_HOST", cf.get(conf_name, 'host')),
            os.getenv("SECURITY_DB_PORT", cf.getint(conf_name, 'port')),
            os.getenv("SECURITY_DB_DATABASE", cf.get(conf_name, 'database')) 
        ),
           encoding=os.getenv("SECURITY_DB_ENCODING", cf.get(conf_name, 'encoding')),
           echo=os.getenv("SECURITY_DB_ECHO", cf.getboolean(conf_name, 'echo')),
           max_overflow=os.getenv("SECURITY_DB_MAX_OVERFLOW", cf.getint(conf_name, 'max_overflow')))
        
        return engine


```

## 知识点
### RawConfigParser 获取配置
```python
        conf_name = "mysql-" + mode
        cf = RawConfigParser()
        path = os.path.dirname(os.path.dirname(os.path.abspath(__file__)))
        cf.read(path + "/conf/app.conf", encoding="utf-8")

```
### create_engine 连接数据库
```python
        engine = create_engine('mysql+pymysql://%s:%s@%s:%s/%s' % (
            os.getenv("SECURITY_DB_USERNAME", cf.get(conf_name, 'user')),
            os.getenv("SECURITY_DB_PASSWORD", cf.get(conf_name, 'password')),
            os.getenv("SECURITY_DB_HOST", cf.get(conf_name, 'host')),
            os.getenv("SECURITY_DB_PORT", cf.getint(conf_name, 'port')),
            os.getenv("SECURITY_DB_DATABASE", cf.get(conf_name, 'database')) 
        ),
           encoding=os.getenv("SECURITY_DB_ENCODING", cf.get(conf_name, 'encoding')),
           echo=os.getenv("SECURITY_DB_ECHO", cf.getboolean(conf_name, 'echo')),
           max_overflow=os.getenv("SECURITY_DB_MAX_OVERFLOW", cf.getint(conf_name, 'max_overflow')))

```


