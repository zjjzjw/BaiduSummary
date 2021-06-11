```python
#!/usr/bin/env python
# -*- coding: utf-8 -*-
"""
Copyright (c) 2021 Baidu.com, Inc. All Rights Reserved
This module provide configure file management service in i18n environment.

Authors: dingwei(v_dingwei@baidu.com)
"""

from sqlalchemy.orm import sessionmaker
import re
import json
import requests
import os

from managers.cursor_manager import CursorManager
from util.global_log import logger


class IcafeBaseController(object):
    """
        从icafe接口拉取BUG数据并写入MySQL
    """

    def __init__(self, engine, mode):
        self.engine = engine
        Session = sessionmaker(bind=engine)
        self.session = Session()
        self.space = "dee-crowdtest"
        self.cursor_manager = CursorManager(engine, mode)
        self.is_auto = False
        self.cursor_name = ''  # 子类中要设置该值
        self.mode = mode

    def get_cursor(self):
        return self.cursor_manager.get(self.cursor_name, "2021-01-01 00:00:00")

    def set_cursor(self, cursor_value):
        return self.cursor_manager.set(self.cursor_name, cursor_value)

    def input(self, data, model):
        """
        写入数据库
        data: 要写入的数据，字典列表，字典键名与数据库字段相同
        model: 使用的orm模型
        """
        for item in data:
            self.upsert(item, model)
        """
        全部写入完成后再更新游标，如果有部分写入失败则程序异常退出，下次执行重新拉取数据
        下次游标的开始时间，取本次拉取数据的最大时间
        """
        # 没查到数据，或者手动执行脚本不更新游标
        if len(data) == 0 or not self.is_auto:
            return
        next_start = max([item['last_modified_time'] for item in data])
        self.set_cursor(next_start)

    def upsert(self, item, model):
        sess = self.session
        if self.mode == 'support':  # support时 相同seq会根据开发人员的个数拆成多条
            existed = sess.query(model).filter_by(
                space_prefix_code=item['space_prefix_code'], sequence=item['sequence'], developer=item['developer']
            ).first()
        else:
            existed = sess.query(model).filter_by(
                space_prefix_code=item['space_prefix_code'], sequence=item['sequence']
            ).first()
        if existed is None:  # insert
            sess.execute(model.__table__.insert(item))
        else:  # update
            for key, value in item.items():
                # 避免不必要的更新，注意orm的datetime格式不是str要转换下再比较
                if hasattr(existed, key) and str(getattr(existed, key)) != item[key]:
                    setattr(existed, key, item[key])
                    if key in ('relation_type', 'relation_ids'):
                        # 这两个值变动的时候，crm_ids需要清理，等其他脚本去重新查询名字
                        setattr(existed, 'crm_ids', '')

        sess.commit()

    def match_relation(self, card_dict, column_name):
        """
        匹配任务ID等
        card_dict 是字典，相当于引用传参，直接在上面修改即可
        """
        key_word_map = {
            '任务': 'task',
            '批次': 'batch',
            '项目': 'template',
        }
        for key_word, map_value in key_word_map.items():
            re_str = r"({}(id)?(:|：)?)([ ]*\d+(,|，))*([ ]*\d+)".format(key_word)
            pattern = re.compile(re_str, re.I)
            match = pattern.search(card_dict[column_name])
            if match:
                trim_str = match.group(1)
                card_dict['relation_type'] = map_value
                # 只保留数组和逗号，并同一把中文逗号换为英文的
                card_dict['relation_ids'] = match.group().lstrip(trim_str).replace('，', ',').replace(' ', '')
                return

    def sync_crm_name(self, model):
        sess = self.session
        # 查询表中relation_ids不为''， 且crm_ids为''
        cards = sess.query(model).filter(model.relation_ids != '', model.crm_ids == '')

        host = os.getenv("ATEST_HOST", 'http://zjj.atest.baidu.com')
        api = '/mark/interfaceOut/GetCrmInfo'
        url = host + api

        # 每张卡片请求一次
        for card in cards:
            relation_id_arr = card.relation_ids.split(',')
            query_date = {
                'query_type': card.relation_type,
                'query_ids': json.dumps(list(filter(lambda x: x.isdigit(), relation_id_arr))),
                't_uuid': card.id,
                "token": os.getenv("ATEST_TOKEN", 'test-best-alliance')
            }

            response = requests.post(url, data=query_date)
            if response.status_code != 200:
                logger.error('POST: ' + url + ' params: ' + json.dumps(query_date) + ' response: ' + response.text)
                continue

            logger.info('POST: ' + url + ' params: ' + json.dumps(query_date) + ' response: ' + response.text)

            res = json.loads(response.text)
            if 'code' in res and res['code'] == 0 and res['crm_ids']:
                setattr(card, 'crm_ids', ','.join([str(i) for i in res['crm_ids']]))
            sess.commit()  # 每张卡片更新一次，防止某张卡片中途出错影响其他卡片
```


知识点
