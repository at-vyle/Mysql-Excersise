## 7.	Xoá tất cả comment của user = 2 trong blog = 5
```
DELETE FROM `comment` WHERE `comment`.`user_id` = 2 AND `comment`.`target_table` ='blog' AND `comment`.`target_id` = 5;
```