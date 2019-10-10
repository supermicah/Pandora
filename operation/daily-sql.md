## 查询金鹏需要的数据
select game_id,	sum(total_people_count),	sum(total_receive_gift_count),	sum(total_active_audience_count),	sum(total_pay_uid),	sum(total_pay_amount) from room_live_data_record where date_time = '2019-10' group by game_id;

## 查询触手主播在kk的收礼数据
room_id, sum(t2.gift_point) as point from oss_bi_kk_white_room t1, oss_bi_kk_gift_record t2
where pt_month = '2019-09' and t1.pt_day = t2.pt_day and t2.room_id = t1.room_id group by t1.room_id order by point ;

## 新增礼物
INSERT INTO `gift` (``,`name`, `icon`, `point`, `point_type`, `desc`, `corner_icon`, `order`, `state`, `created_time`, `updated_time`, `type`, `is_custom`, `group`)
VALUES
	('炫光流星雨', 'http://kascdn.kascend.com/jellyfish/gift/v4/14.png', 6666000, 0, '含海量人鱼宝藏 粉丝也有份喔~', '、', 950, 0, '2016-04-27 17:21:06', '2018-08-01 00:00:01', 1, 1, 1);

# 2018-01-01 ~ 2019-07-01

select pt_month, parms['appkey'], sum(parms['point']) as point from oss_bi_all_jellyfish_log where pt_day >= '2018-01-01' and pt_day < '2019-07-01' and log_type = 1 group by pt_month, parms['appkey']



# 认证信息查询
select uid, biz_no, state, photo, real_name, id_card_num, appkey, created_time from real_person_material where created_time > '2019-09-11' and biz_no = 'dfcc0c5ff20748bcbd1ac3cc7b11ac05' order by id desc;

# 查询小程序的关注数
select * from oss_bi_all_login_log where pt_day = '2019-08-03' and parms['isNew'] = 'true' and parms['appkey'] = 'CSAppletWX' limit 10;



# 获取每天游戏直播前500名的播放时长
select `date`, sum(duration)  from oss_bi_all_room_category_rank where pt_day >= '2019-04-01' and category_id = 0 and rank > 0 and rank <=500 group by `date`  order by `date` ;
# 获取每天游戏直播前100名的播放时长
select `date`, sum(duration)  from oss_bi_all_room_category_rank where pt_day >= '2019-04-01' and category_id = 0 and rank > 0 and rank <=100 group by `date`  order by `date` ;

# 获取每天游戏直播前1000名的播放时长
select `date`, sum(duration)  from oss_bi_all_room_category_rank where pt_day >= '2019-04-01' and category_id = 0 and rank > 0 and rank <=1000 group by `date`  ;


# 获取每天播放时长统计
select b.date, sum(b.gameDuration) as gameDuration, sum(b.showDuration) as showDuration, sum(b.otherDuration) as otherDuration from (
	select
		a.date,
		(case a.category_id when 0 then a.duration else 0 end) as gameDuration,
		(case a.category_id when -2 then a.duration else 0 end) as showDuration,
		(case a.category_id when -1 then a.duration else 0 end) as otherDuration
	from (
		select date, category_id , sum(duration) as duration from room_category_rank where date >= '2019-04-01' and category_id <=0 and rank > 0 group by date, category_id order by date , category_id
	) a
) b group by b.date;



## 获取每个房间的礼物等
select a.room_id, a.points, b.dailyAmount, b.contractAmount, b.otherAmount from (select * from whq_temp_0903 bb where bb.type = 1 ) a left join
(select aa.room_id, sum(aa.dailyAmount) as dailyAmount, sum(aa.contractAmount) as contractAmount, sum(aa.otherAmount) as otherAmount from
(select room_id, CASE WHEN type = 1 THEN amount ELSE 0 END AS dailyAmount, CASE WHEN type = 2 THEN amount ELSE 0 END AS contractAmount, CASE WHEN type in (12004, 12002, 12202, 12003, 22, 12011, 12211, 1000001, 1000002, 1000003) THEN amount ELSE 0 END AS otherAmount from oss_bi_all_finance_salary_record where pt_day >= '2019-01-01' and pt_day < '2019-09-01') aa group by aa.room_id ) b on a.room_id = b.room_id;



select a.room_id, a.points, b.dailyAmount, b.contractAmount, b.otherAmount from (select * from whq_temp_0903 bb where bb.type = 2 ) a left join
(select aa.room_id, sum(aa.dailyAmount) as dailyAmount, sum(aa.contractAmount) as contractAmount, sum(aa.otherAmount) as otherAmount from
(select room_id, CASE WHEN type = 1 THEN amount ELSE 0 END AS dailyAmount, CASE WHEN type = 2 THEN amount ELSE 0 END AS contractAmount, CASE WHEN type in (12004, 12002, 12202, 12003, 22, 12011, 12211, 1000001, 1000002, 1000003) THEN amount ELSE 0 END AS otherAmount from oss_bi_all_finance_salary_record where pt_day >= '2019-06-01' and pt_day < '2019-07-01') aa group by aa.room_id ) b on a.room_id = b.room_id;


select a.room_id, a.points, b.dailyAmount, b.contractAmount, b.otherAmount from (select * from whq_temp_0903 bb where bb.type = 3 ) a left join
(select aa.room_id, sum(aa.dailyAmount) as dailyAmount, sum(aa.contractAmount) as contractAmount, sum(aa.otherAmount) as otherAmount from
(select room_id, CASE WHEN type = 1 THEN amount ELSE 0 END AS dailyAmount, CASE WHEN type = 2 THEN amount ELSE 0 END AS contractAmount, CASE WHEN type in (12004, 12002, 12202, 12003, 22, 12011, 12211, 1000001, 1000002, 1000003) THEN amount ELSE 0 END AS otherAmount from oss_bi_all_finance_salary_record where pt_day >= '2019-07-01' and pt_day < '2019-08-01') aa group by aa.room_id ) b on a.room_id = b.room_id;


select a.room_id, a.points, b.dailyAmount, b.contractAmount, b.otherAmount from (select * from whq_temp_0903 bb where bb.type = 4 ) a left join
(select aa.room_id, sum(aa.dailyAmount) as dailyAmount, sum(aa.contractAmount) as contractAmount, sum(aa.otherAmount) as otherAmount from
(select room_id, CASE WHEN type = 1 THEN amount ELSE 0 END AS dailyAmount, CASE WHEN type = 2 THEN amount ELSE 0 END AS contractAmount, CASE WHEN type in (12004, 12002, 12202, 12003, 22, 12011, 12211, 1000001, 1000002, 1000003) THEN amount ELSE 0 END AS otherAmount from oss_bi_all_finance_salary_record where pt_day >= '2019-08-01' and pt_day < '2019-09-01') aa group by aa.room_id ) b on a.room_id = b.room_id;
