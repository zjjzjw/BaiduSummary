# dee-crowdtest-11201 
## 卡片
dee-crowdtest-11201 [需求] 【承包RD】【嬴彻】【支持需求】嬴彻API对账信息在dashboard展示

## dashboard简介
Dashboard的中文翻译是“仪表盘”，Dashboard沿袭了汽车仪表盘理念，在一个屏幕上有预设性地显示对用户关键的信息，并实时告知用户正在发生的情况。同时，Dashboard进一步结合计算机互动的功能显示和传输信息，突破了报表要考虑纸面打印输出的限制。在Dashboard页面中，用户不仅可以直接对数据和图表进行分析，还可以通过控件来控制数据的显示、计算、过滤和分组等等。

## dashboard重要性
无论是哪一种数据产品，仪表盘（Dashboard）都是最核心的功能。他是用户接触数据的第一个页面，相当于数据产品的门户，担负着提纲挈领，引导分析的重要职能，帮助用户能够快速判断业务情况，支持他们做出决策并行动。概括的来说Dashboard的作用可以分为三大类，监控、分析和概览

## dashboard场景
- 在监控场景中，Dashboard主要为用户集中提供便捷的关键指标实时监测，及时告知异常状态，并引导用户定位问题
- 在分析场景中，Dashboard主要通过数据图表，配合控件进行不同维度的数据分析。例如，用户可以通过时间筛选控件过滤图表上的数据范围等。
- 在复杂业务中，Dashboard还用于概览场景，集中呈现业务分散的重点信息，用户还可以通过提供的入口快速跳转至相关模块。

- 本题的场景是 在复杂业务中

## 问题dashboard
dashboard 的代码库在哪里
配置界面 http://pingce.baidu-int.com/dashboard/admin/admin
web http://pingce.baidu-int.com/dashboard/show/spanindex/19126/dashboard:10128

## 分割story
- 需求内容1 中字段对应的表名
- 需求内容2 dashboard 查询配置
- 嬴彻API对账详设理解




### 需求内容1  字段对应的表名
```
项目ID ：     已知  ct_mark_reconciliation_log template_id
需求ID ：     已知  难点ct_mark_crm_relation  crm_id
需求名称：     已知  难点ct_mark_crm_relation  crm_name
批次ID：      已知  ct_mark_reconciliation_log  batch
源批次ID：    已知  ct_mark_reconciliation_log  annotation_task_id
2d_bbox：     已知 ct_mark_reconciliation_log  element
3d_lidar_bbox：    已知 ct_mark_reconciliation_log  element
3d_radar_bbox：    已知 ct_mark_reconciliation_log  element
point：            已知 ct_mark_reconciliation_log  element
line：             已知 ct_mark_reconciliation_log  element
region：           已知 ct_mark_reconciliation_log  element
ground：           已知 ct_mark_reconciliation_log  element
对账状态：          已知 ct_mark_reconciliation_log status
送标时间：           未知  
送标数据量：         未知
交付时间：           客户拉取时间
交付数据量：         总帧数
交付总元素量：       总元素数 需要统计
moment_id：        未知
task_id：          annotation_task_id
2d_bbox_rect_1：   未知
2d_bbox_rect_2：   未知 
2d_bbox_rect_3：   未知 
region_1：         未知
region_2：         未知
segment：          未知
```




### 嬴彻API对账详设
【详细设计】api对账 2021-02-23
http://agroup.baidu.com/crowdtest-mark/md/article/3709959
总结 （项目背景，项目功能点，数据表设计，统计数据入库，接口设计，获取对账列表，获取批次对账详情，更新对账状态，线下测试，线上出现问题排查步骤）
ct_mark_reconciliation_log

api对账测试报告 2021-02-24
http://agroup.baidu.com/crowdtest-mark/md/article/3716442 
总结 （api对账测试报告，获取对账列表接口，获取对账详情，更新对账状态，分页筛选示例）

Api对账改进计划  2021-04-09
http://agroup.baidu.com/crowdtest-mark/md/article/3826685 
(对账数据表更新,对账表入库逻辑修改,每天推送前一天的对账记录,对账确认接口增加错误信息)


api对账详设 2021-04-15
http://agroup.baidu.com/crowdtest-mark/md/article/3848399
(项目背景,项目功能点,数据表设计,统计数据入库,接口设计,获取对账列表,获取批次对账详情,更新对账状态,线上出现问题排查步骤,)
ct_mark_reconciliation_log 核心表
ct_mark_api_project_auth   权限表


需求  
1、提供批次维度list接口，支持批次日期和对账状态查询功能；
2、提供批次维度detail接口，具体对账内容字段如下，默认包含批次已完成日期 + 对账状态；
3、提供批次维度update接口，只支持更新批次对账状态，暂定3种状态：todo，ok，not ok（0，1，2）














