<!--
 * @Author: your Name
 * @Date: 2021-05-19 15:02:07
 * @LastEditors: zhangjiajia02
 * @LastEditTime: 2021-05-19 16:39:01
 * @Description: 
-->


CREATE TABLE `ct_mark_reconciliation_log` (
  `id` int(11) NOT NULL AUTO_INCREMENT,PRIMARY KEY (id),
  `template_id` int(11) NOT NULL COMMENT '项目id',
  `batch_id` int(11) NOT NULL COMMENT '批次id',
  `annotation_task_id` varchar(100) COLLATE utf8_unicode_ci NOT NULL COMMENT '源批次ID',
  
  `pull_date` datetime DEFAULT CURRENT_TIMESTAMP COMMENT '客户拉取时间',
  `info` text COLLATE utf8_unicode_ci COMMENT '批次结果文件处理信息',
  `elements` text COLLATE utf8_unicode_ci COMMENT '批次元素统计信息',
  `status` enum('todo','success','fail') COLLATE utf8_unicode_ci DEFAULT 'todo' COMMENT '对账状态',
  `created` datetime DEFAULT CURRENT_TIMESTAMP COMMENT '创建时间',
  `updated` datetime DEFAULT CURRENT_TIMESTAMP COMMENT '更新时间',
  `mark` text COLLATE utf8_unicode_ci COMMENT '备注',
  `valid` int(2) DEFAULT '1' COMMENT '1-有效，0-无效'
) ENGINE=InnoDB DEFAULT CHARSET=utf8 COLLATE=utf8_unicode_ci;



CREATE TABLE `ct_mark_reconciliation_dash_log` (
  <!-- 项目ID 、批次IDz、源批次IDz、ct_mark_reconciliation_log -->
  `id` int(11) NOT NULL AUTO_INCREMENT,PRIMARY KEY (id),
  `template_id` int(11) NOT NULL COMMENT '项目id',
  `batch_id` int(11) NOT NULL COMMENT '批次id',
  `annotation_task_id` varchar(100) COLLATE utf8_unicode_ci NOT NULL COMMENT '源批次ID',

<!-- 需求ID、需求名称、ct_mark_crm_relation -->
  `crm_id` int(11) NOT NULL COMMENT '需求id',
  `crm_name` varchar(100)  COMMENT '源批次ID',

  <!-- 送标时间、送标数据量、 created page_count  ct_mark_template_data_batch -->
  `tender_delivery_time` datetime DEFAULT CURRENT_TIMESTAMP COMMENT '送标时间'
  `tender_delivery_total` int(11) NOT NULL COMMENT '送标数据量',

  
 <!-- 交付时间、交付数据量、交付总元素量、对账状态 ct_mark_reconciliation_log-->
  `pull_date` datetime DEFAULT CURRENT_TIMESTAMP COMMENT '客户拉取时间又叫交付时间',
  `pull_page_total`int(11) NOT NULL COMMENT '交付总帧数',
  `pull_element_total`int(11) NOT NULL COMMENT '交付总元素数',
 `status` enum('todo','success','fail') COLLATE utf8_unicode_ci DEFAULT 'todo' COMMENT '对账状态',

<!--  
  moment_id、task_id、2d_bbox、2d_bbox_rect_1、2d_bbox_rect_2、2d_bbox_rect_3、3d_lidar_bbox、
        // 3d_radar_bbox、point、line、region、region_1、region_2、ground、segment  -->

  `moment_id` int(11) NOT NULL COMMENT '统计moment_id个数',
  `task_id`  int(11) NOT NULL COMMENT '统计task_id个数',
  `2d_bbox` int(11) NOT NULL COMMENT '统计task_id个数',
  `2d_bbox_rect_1` int(11) NOT NULL COMMENT '统计task_id个数',
  `2d_bbox_rect_2` int(11) NOT NULL COMMENT '统计task_id个数',
  `2d_bbox_rect_3` int(11) NOT NULL COMMENT '统计task_id个数',
  `3d_lidar_bbox` int(11) NOT NULL COMMENT '统计task_id个数',
  `3d_radar_bbox`  int(11) NOT NULL COMMENT '统计task_id个数',
  `point` int(11) NOT NULL COMMENT '统计task_id个数',
  `line` int(11) NOT NULL COMMENT '统计task_id个数',
  `region` int(11) NOT NULL COMMENT '统计task_id个数',
  `region_1` int(11) NOT NULL COMMENT '统计task_id个数',
  `region_2` int(11) NOT NULL COMMENT '统计task_id个数',
  `ground`  int(11) NOT NULL COMMENT '统计task_id个数',
  `segment` int(11) NOT NULL COMMENT '统计task_id个数',
  

 
  `created` datetime DEFAULT CURRENT_TIMESTAMP COMMENT '创建时间',
  `updated` datetime DEFAULT CURRENT_TIMESTAMP COMMENT '更新时间',
  `mark` text COLLATE utf8_unicode_ci COMMENT '备注',
  `valid` int(2) DEFAULT '1' COMMENT '1-有效，0-无效'
) ENGINE=InnoDB DEFAULT CHARSET=utf8 COLLATE=utf8_unicode_ci;

<!-- 项目ID z、需求IDz、需求名称z、批次IDz、源批次IDz、
送标时间、送标数据量、
        // 交付时间 Z、交付数据量Z、交付总元素量Z、
        
        // moment_id、task_id、2d_bbox、2d_bbox_rect_1、2d_bbox_rect_2、2d_bbox_rect_3、3d_lidar_bbox、
        // 3d_radar_bbox、point、line、region、region_1、region_2、ground、segment、对账状态 -->




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




CREATE TABLE `ct_mark_reconciliation_dash_log` (
  `id` int(11) NOT NULL AUTO_INCREMENT,PRIMARY KEY (id),
  `template_id` int(11) NOT NULL COMMENT '项目id',
  `batch_id` int(11) NOT NULL COMMENT '批次id',
  `annotation_task_id` varchar(100) COLLATE utf8_unicode_ci NOT NULL COMMENT '源批次ID',
  `crm_id` int(11)  COMMENT '需求id',
  `crm_name` varchar(100)  COMMENT '源批次ID',
  `tender_delivery_time` datetime DEFAULT CURRENT_TIMESTAMP COMMENT '送标时间',
  `tender_delivery_total` int(11) NOT NULL COMMENT '送标数据量',
  `pull_date` datetime DEFAULT CURRENT_TIMESTAMP COMMENT '客户拉取时间又叫交付时间',
  `pull_page_total`int(11)  COMMENT '交付总帧数',
  `pull_element_total`int(11)  COMMENT '交付总元素数',
  `status` enum('todo','success','fail') COLLATE utf8_unicode_ci DEFAULT 'todo' COMMENT '对账状态',
  `moment_id` int(11)  COMMENT '统计moment_id个数',
  `task_id`  int(11) COMMENT '统计task_id个数',
  `2d_bbox` int(11)  COMMENT '统计2d_bbox个数',
  `2d_bbox_rect_1` int(11)  COMMENT '统计2d_bbox_rect_1个数',
  `2d_bbox_rect_2` int(11)  COMMENT '统计2d_bbox_rect_2个数',
  `2d_bbox_rect_3` int(11)  COMMENT '统计2d_bbox_rect_3个数',
  `3d_lidar_bbox` int(11) COMMENT '统计3d_lidar_bbox个数',
  `3d_radar_bbox`  int(11)  COMMENT '统计3d_radar_bbox个数',
  `point` int(11)  COMMENT '统计point个数',
  `line` int(11)  COMMENT '统计line个数',
  `region` int(11)  COMMENT '统计region个数',
  `region_1` int(11)  COMMENT '统计region_1个数',
  `region_2` int(11)  COMMENT '统计region_2个数',
  `ground`  int(11)  COMMENT '统计ground个数',
  `segment` int(11)  COMMENT '统计segment个数',
  `created` datetime DEFAULT CURRENT_TIMESTAMP COMMENT '创建时间',
  `updated` datetime DEFAULT CURRENT_TIMESTAMP COMMENT '更新时间',
  `mark` text COLLATE utf8_unicode_ci COMMENT '备注',
  `valid` int(2) DEFAULT '1' COMMENT '1-有效，0-无效'
) ENGINE=InnoDB DEFAULT CHARSET=utf8 COLLATE=utf8_unicode_ci;