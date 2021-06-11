<!--
 * @Author: zhangjiajia02
 * @Date: 2021-05-19 14:37:23
 * @LastEditors: zhangjiajia02
 * @LastEditTime: 2021-05-19 15:08:28
 * @Description: domain curd 总结
-->

##  查询

```php
    /**
     * 构建过滤字符串
     * @param $filter
     * @return mixed
     */
    private function getFilterSqlParam($filter) {
        $bindParams = array();
        $sql = '';
        if (array_key_exists('template_id', $filter)) {
            $sql .= ' AND `template_id` = :template_id ';
            $bindParams[':template_id'] = $filter['template_id'];
        }
        if (array_key_exists('date', $filter)) {
            if(!empty($filter['date']['start'])) {
                $sql .= ' AND pull_date > :start ';
                $bindParams[':start'] = $filter['date']['start'];
            }
            if(!empty($filter['date']['end'])) {
                $sql .= ' AND pull_date < :end ';
                $bindParams[':end'] = $filter['date']['end'];
            }
        }
        if (array_key_exists('status', $filter) && $filter['status'] !== '') {
            $sql .= ' AND `status` = :status ';
            $bindParams[':status'] = $filter['status'];
        }

        return array(
            'bindParams' => $bindParams,
            'sql' => $sql,
        );
    }
```

## 新增
```php
  /**
     * 新增对账记录
     */
    public function create($data) {
        $ar = new MarkApiReconciliation();
        $attributes = $ar->getAttributes();
        foreach ((array)$data as $key => $val) {
            if ($key === 'id') {
                continue;
            }
            if (array_key_exists($key, $attributes)) {
                $ar->$key = $val;
            }
        }
        $ar->created = date('Y-m-d H:i:s');
        $ar->updated = date('Y-m-d H:i:s');
        return $ar->save();
    }
```

## 更新
```php
 /**
     * 更新对账数据
     */
    public function update($batchId, $params) {
        return MarkApiReconciliation::model()->updateAll(
            $params,
            'batch_id = :batch_id',
            array(':batch_id' => $batchId)
        );
    }
```
## 删除

## 其他
### 统计

```php
  /**
     * 获取数据量
     */
    public function getCount($filter) {
        $sql = "SELECT COUNT(*) FROM ct_mark_reconciliation_log WHERE 1";
        //根据筛选条件构建sql
        $filterParams = $this->getFilterSqlParam($filter);
        $sql .= $filterParams['sql'];
        $command = \MarkDbManager::getConnection()->createCommand($sql);
        $command->bindValues($filterParams['bindParams']);
        return intval($command->queryScalar());
    }
```


```php
<?php
/**
 * api对账dashboard展示
 * User: v_liujinxin01
 * Date: 2021/02/22
 * Time: 14:34
 */
namespace mark\models\domain\api;

use  mark\models\ar\api\MarkApiReconciliation;
use  mark\models\ar\api\MarkApiReconciliationDash;

\Yii::import("application.mark.models.ar.MarkActiveRecord");
\Yii::import("application.mark.managers.lib.util.SqlUtil");
\Yii::import("application.mark.managers.lib.db.MarkDbManager");
\Yii::import("application.mark.utils.CommonUtil");
class MarkApiReconciliationDashDomain
{

}

```
