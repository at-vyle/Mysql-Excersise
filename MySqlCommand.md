
## 1. Tạo database

```
CREATE DATABASE `baitapmysql`
```

* Tạo bảng user
```
CREATE TABLE `user` (
  `id` int(11) NOT NULL PRIMARY KEY,
  `full_name` varchar(255) COLLATE utf8_unicode_ci NOT NULL,
  `email` varchar(255) COLLATE utf8_unicode_ci NOT NULL,
  `rank` tinyint(4) NOT NULL,
  `is_active` tinyint(1) NOT NULL,
  `created_at` timestamp NOT NULL DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP
) ENGINE=InnoDB DEFAULT CHARSET=utf8 COLLATE=utf8_unicode_ci;
```

* Taọ bảng comment

```
CREATE TABLE `comment` (
  `id` int(11) NOT NULL PRIMARY KEY,
  `target_table` varchar(20) COLLATE utf8_unicode_ci NOT NULL,
  `target_id` int(11) NOT NULL,
  `user_id` int(11) NOT NULL,
  `comment` text COLLATE utf8_unicode_ci NOT NULL,
  `created_at` timestamp NULL DEFAULT NULL,
  `updated_at` timestamp NULL DEFAULT NULL,
  FOREIGN KEY (`user_id`) REFERENCES `user`(`id`)
) ENGINE=InnoDB DEFAULT CHARSET=utf8 COLLATE=utf8_unicode_ci;
```

* Tạo bảng follow

```
CREATE TABLE `follow` (
  `id` int(11) NOT NULL PRIMARY KEY,
  `form_user_id` int(11) NOT NULL,
  `to_user_id` int(11) NOT NULL,
  `created_at` timestamp NULL DEFAULT NULL,
  FOREIGN KEY (`form_user_id`) REFERENCES `user`(`id`),
  FOREIGN KEY (`to_user_id`) REFERENCES `user`(`id`)
) ENGINE=InnoDB DEFAULT CHARSET=utf8 COLLATE=utf8_unicode_ci;
```

* Tạo bảng category

```
CREATE TABLE `category` (
  `id` int(11) NOT NULL PRIMARY KEY,
  `title` varchar(255) COLLATE utf8_unicode_ci NOT NULL,
  `description` varchar(255) COLLATE utf8_unicode_ci NOT NULL
) ENGINE=InnoDB DEFAULT CHARSET=utf8 COLLATE=utf8_unicode_ci;
```

* Tạo bảng news

```
CREATE TABLE `news` (
  `id` int(11) NOT NULL PRIMARY KEY,
  `category_id` int(11) NOT NULL,
  `title` varchar(255) COLLATE utf8_unicode_ci NOT NULL,
  `view` int(11) NOT NULL,
  `is_active` tinyint(1) DEFAULT NULL,
  `content` text COLLATE utf8_unicode_ci,
  `created_at` timestamp NULL DEFAULT NULL,
  `updated_at` timestamp NULL DEFAULT NULL,
  FOREIGN KEY (`category_id`) REFERENCES `category`(`id`)
) ENGINE=InnoDB DEFAULT CHARSET=utf8 COLLATE=utf8_unicode_ci;
```

* Tạo bảng blog

```
CREATE TABLE `blog` (
  `id` int(11) NOT NULL PRIMARY KEY,
  `category_id` int(11) NOT NULL,
  `user_id` int(11) NOT NULL,
  `title` varchar(255) COLLATE utf8_unicode_ci NOT NULL,
  `view` int(11) NOT NULL,
  `is_active` tinyint(1) DEFAULT NULL,
  `content` text COLLATE utf8_unicode_ci,
  `created_at` timestamp NULL DEFAULT NULL,
  `updated_at` timestamp NULL DEFAULT NULL,
  FOREIGN KEY (`category_id`) REFERENCES `category`(`id`),
  FOREIGN KEY (`user_id`) REFERENCES `user`(`id`)
) ENGINE=InnoDB DEFAULT CHARSET=utf8 COLLATE=utf8_unicode_ci;
```
## 2. Thêm 1 dòng dữ liệu trong bất kỳ table nào
```
INSERT INTO `user` (`full_name`, `email`, `rank`, `is_active`, `created_at`) VALUES
('Le Ba Vy', 'vy.le@asiantech.vn', 2, 1, '2018-06-17 17:00:00');
```
## 3.	Xoá và sửa 1 dòng dữ liệu trong bất kỳ table nào
```
UPDATE `blog` SET `title` = 'blog 2b' WHERE `blog`.`id` = 10;
DELETE FROM `blog` WHERE `blog`.`id` = 10;
```
## 4.	Select 10 blog mới nhất đã active
```
SELECT * FROM `comment` WHERE `is_active` = 1 ORDER BY `id` DESC LIMIT 10;
```
## 5.	Lấy 5 blog từ blog thứ 10
```
SELECT * FROM `blog` ORDER BY `id` DESC LIMIT 9, 5;
```
## 6.	Set is_active = 0 của user có id = 3 trong bảng user
```
UPDATE `user` SET `is_active` = 0 WHERE `user`.`id` = 3;
```
## 7.	Xoá tất cả comment của user = 2 trong blog = 5
```
DELETE FROM `comment` WHERE `comment`.`user_id` = 2 AND `comment`.`target_table` ='blog' AND 	`comment`.`target_id` = 5;
```
## 8.	Lấy 3 blog bất kỳ (random)
```
SELECT * FROM `blog` ORDER BY RAND() LIMIT 3;
```
## 9.	Lấy số lượng comment của các blog
```
SELECT `target_table`, `target_id`, COUNT(`id`) as SL FROM comment 
WHERE `target_tabl`e='blog'
GROUP BY `target_id`, `target_table`;
```
## 10.	Lấy Category có tồn tại blog hoặc news đã active (không được lặp lại category)
```
SELECT * FROM category
WHERE category.id IN (SELECT blog.category_id FROM blog WHERE blog.is_active =1)
		OR category.id IN (SELECT news.category_id FROM news WHERE news.is_active =1) ;
```
## 11.	Lấy tổng lượt view của từng category thông qua blog và news
```
SELECT view.id, SUM(view.ViewCat) FROM 
(
	SELECT news.category_id AS id, SUM(news.view) AS viewCat FROM news GROUP BY news.id
   UNION
   SELECT blog.category_id AS id, SUM(blog.view)  AS viewCat FROM blog GROUP BY blog.id
) AS view GROUP BY view.id
```
## 12.	Lấy blog được tạo bởi user mà user này không có bất kỳ comment ở blog
```
SELECT blog.*
FROM blog
INNER JOIN comment ON blog.id = comment.target_id
WHERE comment.target_table = 'blog'
  AND blog.user_id != comment.user_id;
```
## 13.	Lấy 5 blog mới nhất và số lượng comment cho từng blog
```
SELECT blog.id, COUNT(comment.id) AS CM
FROM blog
INNER JOIN comment ON blog.id = comment.target_id
WHERE comment.target_table = 'blog' GROUP BY blog.id ORDER BY blog.id DESC LIMIT 5
```
## 14.	Lấy 3 User comment đầu tiên trong 5 blogs mới nhất
```
```
## 15.	Update rank user = 2 khi tổng số lượng comment của user > 20
```
UPDATE user SET user.rank = 2 WHERE user.id IN 
(
	SELECT cm.id FROM 
	(
		SELECT comment.user_id AS id, COUNT(comment.user_id) AS sumCMT FROM comment GROUP BY comment.user_id
	) AS cm WHERE cm.sumCMT > 1 
)
 ```
 ## 16.	Xoá comment mà nội dung comment có từ "fuck" hoặc "phức"
 ```
 DELETE FROM comment WHERE comment.comment LIKE '%fuck%' OR '%phức%'
 ```
 ## 17.	Select 10 blog mới nhất được tạo bởi các user active
 ```
 SELECT blog.* FROM blog INNER JOIN user ON user.id = blog.user_id WHERE user.is_active = 1 ORDER BY blog.id DESC
 ```
 ## 18.	Lấy số lượng Blog active của user có id là 1,2,4
 ```
 SELECT blog.user_id, COUNT(blog.id) FROM blog WHERE blog.is_active = 1 AND blog.user_id IN (1,2,4) GROUP BY blog.user_id
 ```
 ## 19.	Lấy 5 blog và 5 news của 1 category bất kỳ
 ```
 ```
 ## 20.	Lấy blog và news có lượt view nhiều nhất
 ```
SELECT blog.title AS title, MAX(blog.view) AS view FROM blog
UNION
SELECT  news.title AS title, MAX(news.view) AS view FROM news
```
### 21.	Lấy blog được tạo trong 3 ngày gần nhất

 
