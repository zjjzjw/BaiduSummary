<!--
 * @Author: your Name
 * @Date: 2021-06-04 10:18:32
 * @LastEditors: zhangjiajia02
 * @LastEditTime: 2021-06-04 12:45:03
 * @Description: 
-->
//  日期（已知）date	项目id（已知）template_id	项目名称（已知）template_name	批次ID（已知 batch_id）	批次名称（已知 batch_name）	任务ID（已知 mark_id）	任务名称（已知 mark_name）
        // 	题目类型(未知 question_type)	代理商名(未知)	公会名(未知)
        //  用户ID（已知 user_id）	用户名（已知 user_name）	答题数量（未知 ）	
        //  总语音长度(秒 totle_time)（已知）	有效语音长度(秒effective_time)（已知）	无效语音长度(秒)（invalid_time已知）

CREATE TABLE `ct_mark_statistic_task_user_audio_mark_day` (
  `id` int(11) NOT NULL AUTO_INCREMENT,PRIMARY KEY (id),
  `template_id` int(11) NOT NULL COMMENT '项目id',
  `template_name` varchar(100)  COMMENT '项目名称',
  `batch_id` int(11) NOT NULL COMMENT '批次id',
  `batch_name` varchar(100)  COMMENT '批次名称',
  `mark_id` int(11) NOT NULL COMMENT '任务id',
  `mark_name` varchar(100)  COMMENT '任务名称',
  `date` varchar(100)  COMMENT '日期',
  `question_type` varchar(100)  COMMENT '题目类型',
  `agent_name` varchar(100)  COMMENT '代理商名称',
  `guild_name` varchar(100)  COMMENT '公会名称',
  `user_id` int(11) NOT NULL COMMENT '用户ID',
  `user_name` varchar(100)  COMMENT '用户名称',
  `question_num` int(11) NOT NULL COMMENT '答题数量',
  `totle_time` int(11) NOT NULL COMMENT '总语音长度',
  `effective_time` int(11) NOT NULL COMMENT '有效语音长度',
  `invalid_time` int(11) NOT NULL COMMENT '无效语音长度',
  `mark` text COLLATE utf8_unicode_ci COMMENT '备注',
  `valid` int(2) DEFAULT '1' COMMENT '1-有效，0-无效',
  `created` datetime DEFAULT CURRENT_TIMESTAMP COMMENT '创建时间',
  `updated` datetime DEFAULT CURRENT_TIMESTAMP COMMENT '更新时间'
) ENGINE=InnoDB DEFAULT CHARSET=utf8 COLLATE=utf8_unicode_ci;
