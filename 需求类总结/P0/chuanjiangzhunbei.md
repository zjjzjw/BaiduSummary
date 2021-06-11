# Api对账串讲
## 背景
赢彻需要核对标注答案中各类元素的数量，之前都是通过excel表格手动核对，工作量大且容易出错，现开发接口自动对账。
## 卡片
https://console.cloud.baidu-int.com/devops/icafe/issue/dee-crowdtest-10082/show
## 项目功能点
生成结果文件时以批次为纬度将答案的统计信息入库
对外提供获取对账列表接口，支持根据项目id，日期筛选，支持分页
对外提供获取批次对账详情接口
对外提供更新对账状态接口


##  代码分析
### 生成结果文件入库逻辑
#### 代码结构
1、table ct_mark_reconciliation_log
2、ct_mark_reconciliation_log的model
3、MarkApiDomain curd
4、AfterExportCheck->checkTotalElements 入库

module-mark/commands/ApiPullLogSupportCommand.php




#### 代码逻辑充血
- 获取失败的对账记录
getReconciliationLogsByCondition 

- 如果all为空 取昨天 不为空 取非todo
getReconciliationLogs

- 获取数据量
getCount($filter) 

- 获取对账列表
 getList($filter, $limit = array()) 

- 检测赢彻所有的批次结果文件统计
actionCheckAllBatch()
step0 根据api账号获取所有项目
step1 获取赢彻所有批次
step2 获取客户拉取到的结果文件
step3 检测结果文件中的total_elements列和文件内容中的统计是否一致
step4 记录不一致的批次
step3 构建文件内容
step4 上传bos

根据结果文件统计元素量
checkResultElements






触发入库入口 
controller （apiOutController 确认数据已经导出接口，apiController确认对账接口）
service （ApiDataBatchService::confirmDataRet->saveConfirmLog）

写入回传记录 





service 


confirmDataRet
getDataRet

![图片](http://bos.bj.bce-internal.sdns.baidu.com/agroup-bos-bj/bj-ee42687b232490673b4129e11a45594df6fbbd9a)

```
		if(isset($data['processed_file_url']) && $ret['status'] !== 0) {
                $service = new ApiReconciliationService();
                $service->updateReconciliationByBatchId($id);
            }
```


```
		 /**
		     *  确认对账接口
		     *  参数：
		     *      "id":int，数据id,根据type决定，
		     *      "type": string, project_data: 对应template_data_batch_id
		     *      "info": {is_reconciliation:1, status : success | fail} is_reconciliation为固定值必传
		     * 方式：post
		     * 返回结果：
		     * {
		     *      "status":int, 0:失败，1：成功
		     *      "error":"状态描述", 错误信息：数据为空, 处理中，已失败等
		     * }
		     */
    ```








### 对账列表逻辑
#### 代码结构

 /api/getReconciliationList 
#### 代码逻辑
 
### 对账详情逻辑
#### 代码结构
/api/getReconciliationDetail
#### 代码逻辑

### 对账更新状态逻辑
#### 代码结构
/api/confirmConvertedFile

#### 代码逻辑


## 相关文档
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



## P0 需求分析
### 需求卡片
https://console.cloud.baidu-int.com/devops/icafe/issue/dee-crowdtest-11201/show

### P0功能点
1、现有对账逻辑 增加统计入库的元素数，并刷新数据
2、view表结构设计及详细设计
3、定时脚本  从对账表同步数据到view表中
4、配置dashboard 实现客户需求




##ApiReconciliationService 分析
### 哪些command 使用了 ApiReconciliationService
ApiPullLogSupportCommand command
getStatisticFromResult
      saveReconciliation ：根据文件内容获取统计信息
|path1|path2 | path3 |
|---|---|---|
| actionCheckReconciliation|getStatisticFromResult| 根据文件内容获取统计信息 | 
| actionCheckReconciliation  |  saveReconciliation| 保存写入 | 
| actionUpdateApiByPullLog | saveReconciliation |  保存写入| 

### 哪些接口使用了ApiReconciliationService

ApiOutController 
getReconciliationDetail ： 获取对账详情
getReconciliationList ： 获取对账列表

### ApiReconciliationService有哪些func

checkReconciliation 检测项目是否需要对账
getReconciliationList 获取对账列表
getReconciliationDetail  获取对账详情
updateReconciliationStatus 更新对账状态
updateReconciliation 更新对账状态
saveReconciliation 保存写入
updateReconciliationByBatchId  更新对账信息

getStatisticFromResult  根据文件内容获取统计信息
getElementsFromResult 根据结果文件统计元素量


### ApiReconciliationService

### MarkApiReconciliationDomain

- 检测赢彻所有的批次结果文件统计
actionCheckAllBatch()
step0 根据api账号获取所有项目
step1 获取赢彻所有批次
step2 获取客户拉取到的结果文件
step3 检测结果文件中的total_elements列和文件内容中的统计是否一致
step4 记录不一致的批次
step3 构建文件内容
step4 上传bos

根据结果文件统计元素量
checkResultElements


入库入口 apiOutController
confirmDataRet
getDataRet





