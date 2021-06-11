<!--
 * @Author: your Name
 * @Date: 2021-05-19 15:06:44
 * @LastEditors: zhangjiajia02
 * @LastEditTime: 2021-05-19 15:06:52
 * @Description: 
-->

```php
<?php
/**
 * api对账
 */

namespace mark\models\ar\api;

class MarkApiReconciliation extends \MarkActiveRecord {
    /**
     *
     * 采用单例模式来返回对象
     *
     * @param string $className
     * @return mixed
     */
    public static function model($className = __CLASS__){
        return parent::model($className);
    }


    /**
     * 表名
     * @return string
     */
    public function tableName(){
        return 'ct_mark_reconciliation_log';
    }
}
```
