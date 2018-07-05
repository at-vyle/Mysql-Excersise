## 3.	Xoá và sửa 1 dòng dữ liệu trong bất kỳ table nào
```
UPDATE `blog` SET `title` = 'blog 2b' WHERE `blog`.`id` = 10;
DELETE FROM `blog` WHERE `blog`.`id` = 10;
```