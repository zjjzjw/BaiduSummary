<!--
 * @Author: your Name
 * @Date: 2021-05-19 15:02:07
 * @LastEditors: zhangjiajia02
 * @LastEditTime: 2021-05-19 15:03:40
 * @Description: 
-->






CREATE TABLE `ct_mark_reconciliation_log` (
  `id` int(11) NOT NULL AUTO_INCREMENT,PRIMARY KEY (id),
  `template_id` int(11) NOT NULL COMMENT '项目id',
  `batch_id` int(11) NOT NULL COMMENT '批次id',
  `annotation_task_id` varchar(100) COLLATE utf8_unicode_ci NOT NULL COMMENT '批次id',
  `pull_date` datetime DEFAULT CURRENT_TIMESTAMP COMMENT '客户拉取时间',
  `info` text COLLATE utf8_unicode_ci COMMENT '批次结果文件处理信息',
  `elements` text COLLATE utf8_unicode_ci COMMENT '批次元素统计信息',
  `status` enum('todo','success','fail') COLLATE utf8_unicode_ci DEFAULT 'todo' COMMENT '对账状态',
  `created` datetime DEFAULT CURRENT_TIMESTAMP COMMENT '创建时间',
  `updated` datetime DEFAULT CURRENT_TIMESTAMP COMMENT '更新时间',
  `mark` text COLLATE utf8_unicode_ci COMMENT '备注',
  `valid` int(2) DEFAULT '1' COMMENT '1-有效，0-无效'
) ENGINE=InnoDB DEFAULT CHARSET=utf8 COLLATE=utf8_unicode_ci;




<!-- CREATE TABLE `template_tag_relation` (
  `id` int(11) NOT NULL AUTO_INCREMENT,PRIMARY KEY (id),
  `template_id` int NOT NULL DEFAULT '0',
  `template_name` varchar(100) NOT NULL COMMENT '项目名称',
  `tag_id` int DEFAULT '0',
  `created` datetime NOT NULL DEFAULT CURRENT_TIMESTAMP,
  `updated` timestamp NOT NULL DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP,
  PRIMARY KEY (`id`),
  KEY `template_id` (`template_id`),
) ENGINE=InnoDB AUTO_INCREMENT=200 DEFAULT CHARSET=utf8; -->