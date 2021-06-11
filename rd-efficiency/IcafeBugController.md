

```python
#!/usr/bin/env python
# -*- coding: utf-8 -*-
"""
Copyright (c) 2021 Baidu.com, Inc. All Rights Reserved
This module provide configure file management service in i18n environment.

Authors: dingwei(v_dingwei@baidu.com)
"""
from sqlalchemy import text

from util import support
from models.entity.bug_card import BugCard
from managers.icafe_manager import IcafeManager
from models.icafe_bug import IcafeBug
from controllers.icafe_base_controller import IcafeBaseController as Base
from util.global_log import logger

```

```python
class IcafeBugController(Base):
    """
        从icafe接口拉取BUG数据并写入MySQL
    """

    def __init__(self, engine):
        super(IcafeBugController, self).__init__(engine, 'bug')
        self.cards = []
        self.cursor_name = 'icafe_bug_last_modified_time'
        self.is_auto = False

    def read(self, last_modify_start, last_modify_end):
        """
        :param last_modify_start:最后修改时间下限，为None会取上次处理完成的游标
        :param last_modify_end:最后修改时间上限，为None会取当前时间
        :return:
        流程状态 in (开发中,待评估,开发中,已解决) AND
        """

        iql = "类型 in (Bug) AND 流程状态 not in (需求打回) AND 开发人员 is not empty"
        if last_modify_start is None:
            last_modify_start = self.get_cursor()
            self.is_auto = True
        iql = iql + ' AND 最后修改时间 > "%s"' % last_modify_start
        if last_modify_end is not None:
            iql = iql + ' AND 最后修改时间 < "%s"' % last_modify_end
        logger.info(iql)
        icafe_manager = IcafeManager()
        res = icafe_manager.query_icafe_cards_by_iql(self.space, iql)
        cards = []
        seq = []
        for card_obj in res['cards']:
            bug_card = BugCard(card_obj, []).to_dict()  # 这里不需要review信息
            self.match_relation(bug_card, 'phenomenon_desc')
            cards.append(bug_card)
            seq.append(bug_card['sequence'])
        logger.info("upsert num:" + str(len(seq)) + " upsert seq: " + str(seq))
        self.cards = cards

    def input(self):
        """写入数据库"""
        super(IcafeBugController, self).input(self.cards, IcafeBug)

    def sync_crm_name(self):
        """同步数据"""
        super(IcafeBugController, self).sync_crm_name(IcafeBug)

    def sync_crm_name(self):
        """同步数据"""
        super(IcafeBugMutation, self).sync_crm_name(IcafeBug)

    def search(self, args):
        sess = self.session
        select_params_dict = args
        pre_sql = 'select *, CONCAT(space_prefix_code, ":", sequence) AS seq from icafe_bug'
        cond = []
        for key, value in select_params_dict.items():
            if value == '':  # amis框架参数选择之后再叉掉会传空
                continue
            if key == 'last_modified_time_start':
                cond.append("last_modified_time >= '" + support.unix_timestamp(value) + "'")
            elif key == 'last_modified_time_end':
                cond.append("last_modified_time <= '" + support.unix_timestamp(value) + "'")
            elif key == 'create_date_range':
                time_range = support.unix_timerange(value)
                cond.append("created_time between '" + time_range[0] + "' and '" + time_range[1] + "'")
            elif key in ('bug_owner',):
                if isinstance(value, str):
                    cond.append(key + " = '" + value + "'")
                else:
                    cond.append(key + " = " + value)
        if len(cond) > 0:
            pre_sql += " where " + " and ".join(cond)
        pre_sql += " order by last_modified_time desc"
        bind_sql = text(pre_sql)
        resproxy = sess.execute(bind_sql, select_params_dict)
        return support.to_dict_list(resproxy.fetchall(), resproxy.keys())
```

##知识点

### from sqlalchemy import text
作用：封装sql字符串
```python
    from sqlalchemy import text
    result = db.execute(text('select * from table where id < :id and typeName=:type'), {'id': 2,'type':'USER_TABLE'})
```
如果不指定parameter的类型, 默认为字符串类型; 如果要传日期参数, 需要使用text()的bindparams参数来声明
```python
    from sqlalchemy import DateTime
    date_param=datetime.today()+timedelta(days=-1*10)
    sql="delete from caw_job_alarm_log  where alarm_time<:alarm_time_param"
    t=text(sql, bindparams=[bindparam('alarm_time_param', type_=DateTime, required=True)])
    db.execute(t,{"alarm_time_param": date_param})    

```
参数bindparam可以使用type_来指定参数的类型, 也可以使用 initial 值来指定参数类型
bindparam('alarm_time_param', type_=DateTime) #直接指定参数类型
bindparam('alarm_time_param', DateTime()) #使用初始值指定参数类型

如要转换查询的结果中的数据类型, 可以通过text()的参数typemap参数指定. 这点比mybatis还灵活,
```python
        t = text("SELECT id, name FROM users",
                typemap={
                    'id':Integer,
                    'name':Unicode
                }
        )
```


### from util import support
定义一些通用的辅助函数
```python
to_dict_list # 将session.execute的结果转换为字典列表
unix_timestamp #时间戳转本地时间
unix_timerange #将 1585670400,1619884799格式的时间范围转为本地时间列表
dict_only #未知
dict_merge # 合并数组
arr_column # 提取字典列表中某一列的值
date_range_d #  """把一个时间段按自然天切分"""
date_range_w # """把一个列表按自然周切分"""


```

### from controllers.icafe_base_controller import IcafeBaseController as Base



### from models.icafe_bug import IcafeBug

### logger.info(iql)

### search

```python
        sess = self.session
        select_params_dict = args
        pre_sql = 'select *, CONCAT(space_prefix_code, ":", sequence) AS seq from icafe_bug'
        bind_sql = text(pre_sql)
        resproxy = sess.execute(bind_sql, select_params_dict)
        return support.to_dict_list(resproxy.fetchall(), resproxy.keys())
```

