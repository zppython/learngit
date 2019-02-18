# SQL 参考
***
#### SQL语句需要在以下网址格式化，数据库类型选MySQL
  [在线SQL格式化](http://tool.oschina.net/codeformat/sql)
***
* 转换性别（hive.default）
```
SELECT deviceid
	, CASE sex
		WHEN 'male' THEN 1
		WHEN 'female' THEN 0
		ELSE NULL
	END AS sex
FROM babies_view
```

* 转换设备（hive.default）
```
SELECT uid
	, CASE codename
		WHEN 'device' THEN 0
		WHEN 'redBoy' THEN 1
		WHEN 'ntt001w' THEN 2
		WHEN 'ntt001d' THEN 3
		WHEN 'ntt001c' THEN 4
		WHEN 'ntt001s' THEN 5
		WHEN 'nttp1s' THEN 6
		WHEN 'nttr1' THEN 7
		ELSE NULL
	END AS codename
FROM devices_view
```
*  时间精度至5分钟，宝宝年龄精度至3个月（hive.default）
` xxxx/xx/xx HH:(0,5..,55) -1,(0,3...144),-2`
```
SELECT log.date
	, CASE 
		WHEN date_diff('month', baby.birthday, log.date) <= 0
		AND date_diff('month', baby.birthday, log.date) >= -12 THEN -1
		WHEN date_diff('month', baby.birthday, log.date) <= 144
		AND date_diff('month', baby.birthday, log.date) > 0 THEN date_diff('month', baby.birthday, log.date) / 3 * 3
		ELSE -2
	END AS mon3
FROM (
	SELECT date_add('minute', MINUTE(ts) / 5 * 5, date_trunc('hour', ts)) AS date
		, deviceid
	FROM device_track_op_log
	WHERE dt = '2019-01-01'
		AND duration > 0
		AND duration < 7200
		AND date_trunc('day', ts) = DATE '2019-01-01'
) log
	INNER JOIN babies_view baby ON log.deviceid = baby.deviceid
	```


