# 周报生成工具
## 简要说明

通过调用icafe API生成周报需要的excel

代码位置：rd-efficiency/tool/commands/icafe.py

## 快速开始
1. python版本：3.0+
2. 安装依赖库pip install openpyxl
3. 获取icafe的虚拟用户名和密码，更新代码中的icafe_user和icafe_pwd变量, 链接如下：http://icafe.baidu.com/users/virtual
4. python commands/icafe.py --help
    icafe.py -r<rd> -s<start> -e<end> -o<owner> -m<mode>

5. 参数说明：
    
    -s --start = "2020-02-28 11:00:00", 卡片创建开始时间，精确到秒

    -e --end = "2020-03-06 10:59:59"，卡片创建结束时间，精确到秒

    -r --rd = yuhui05, 对应开发人员

    -o --owner = yuhui05, 对应开发负责人

    -m --mode = bug|support|apology, 枚举类型

6. 参考命令：（在tool目录下执行）
    - 生成project.xlsx，参考命令： python commands/icafe.py --start="2020-10-11 00:00:00" --end="2020-10-21 23:59:59" --mod=project
    - 生成support.xlsx，参考命令： python commands/icafe.py --start="2020-10-11 00:00:00" --end="2020-10-21 23:59:59" --mod=support
    - 生成bug.xlsx，参考命令：     python commands/icafe.py --start="2020-10-11 00:00:00" --end="2020-10-21 23:59:59" --mod=bug
    - 生成apology.xlsx，参考命令： python commands/icafe.py --start="2020-10-11 00:00:00" --end="2020-10-21 23:59:59" --mod=apology


# 研发数据统计工具
## 简要说明

根据commands/icafe.py生成的project/bug/support统计表，以及串讲技术分享表，生成研发数据统计表。

代码位置：rd-efficiency/tool/commands/rdsumAuto.py


## 遗留问题点

```


```


## 快速开始
1. python：3+
2. 安装依赖库 functools, pandas, datetime, chinese_calendar, numpy
3. 参数说明
    -s --start = "2020-07-01 00:00:00", 统计开始时间，精确到秒
    -e --end = "2020-09-30 23:59:59"，  统计结束时间，精确到秒

4. 参考命令：（在生成了上述project/bug/support统计表的情况下再使用）
    - 创建统计表，参考命令： python commands/rdsumAuto.py --start="2020-10-11 00:00:00" --end="2020-10-21 23:59:59"
    

# web项目本地搭建
- 1. 安装依赖：pip install flask flask-cors pymysql requests sqlalchemy cryptography APScheduler gevent
- 2. 进入web项目根目录tool   
- 3. 先运行脚本，会自动建表并导入数据 
   - python commands/import.py --mode=icafesupport
   - python commands/import.py --mode=icafebug
- 4. 启动web项目入口文件： python server.py
- 5. 访问127.0.0.1:5000/rd-eff
- 6. 部分功能会访问佳佳的个人测试环境接口 添加host：  10.157.6.55 zjj.atest.baidu.com
- 7. 定时脚本启动方法：python commands/job_console.py  该脚本会定时执行2以及同步crm_id（同步crm_id要先配置6）
- 8. 本地环境屏蔽了UUAP验证，所以不需要建立users表


# web项目目录结构说明
1. 根目录为tool
2. 入口文件为server.py
3. 整体结构为mvc模型， m:models v:templates c:controllers   
4. 所有与数据表关联的模型 或者是其他抽象出来的模型 放在models文件夹
5. 入口文件直接调用controller进行逻辑处理
6. managers文件夹放一些会被很多地方调用的公共服务，例如提供数据库引擎，管理游标等
