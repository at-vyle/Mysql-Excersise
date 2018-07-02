
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
 SET @id_rand = (SELECT id FROM category ORDER BY RAND() LIMIT 1)
(SELECT * FROM blog
WHERE category_id = @id_rand
LIMIT 5)
UNION
(SELECT * FROM news
WHERE category_id = @id_rand
LIMIT 5);
 ```
 ## 20.	Lấy blog và news có lượt view nhiều nhất
 ```
SELECT blog.title AS title, MAX(blog.view) AS view FROM blog
UNION
SELECT  news.title AS title, MAX(news.view) AS view FROM news
```
## 21.	Lấy blog được tạo trong 3 ngày gần nhất
```
SELECT * FROM blog WHERE created_at > CURRENT_DATE() - 3;
```
## 22.	Lấy danh sách user đã comment trong 2 blog mới nhất
```
SELECT * FROM user
WHERE id IN (
SELECT comment.* FROM comment WHERE comment.target_table ='blog' AND comment.target_id IN (
SELECT blog.id FROM blog ORDER BY blog.id LIMIT 0,2));
```
## 23. Lấy 2 blog, 2 news mà user có id = 1 đã comment
```
(SELECT blog.id, blog.category_id,blog.title, blog.view, blog.is_active, blog.content, blog.created_at, blog.updated_at FROM blog
WHERE id in (SELECT comment.target_id FROM comment WHERE comment.target_table ='blog' AND comment.user_id = 1)
LIMIT 2)
UNION
(SELECT * FROM news
WHERE id in (SELECT comment.target_id FROM comment WHERE comment.target_table ='news' AND comment.user_id = 1)
LIMIT 2)
```
## 24. Lấy 1 blog và 1 news có số lượng comment nhiều nhất
```
(SELECT title, COUNT(comment.id) FROM blog
INNER JOIN comment ON blog.id = comment.target_id WHERE comment.target_table = 'blog'
GROUP BY title
ORDER BY COUNT(comment.id)
LIMIT 1)
UNION
(SELECT title, COUNT(comment.id) FROM news
INNER JOIN comment ON news.id = comment.target_id WHERE comment.target_table = 'news'
GROUP BY blog.id
ORDER BY COUNT(comment.id)
LIMIT 1);
```
## 25. Lấy 5 blog và 5 news mới nhất đã active
```
(SELECT blog.title FROM blog
WHERE blog.is_active = 1
ORDER BY blog.id DESC
LIMIT 5)
UNION
(SELECT news.title FROM news
WHERE news.is_active = 1
ORDER BY news.id DESC
LIMIT 5);
```
## 26. Lấy nội dung comment trong blog và news của user id =1
```
SELECT comment FROM comment
WHERE user_id = 1;
```
## 27. Blog của user đang được user có id = 1 follow
```
SELECT * FROM blog WHERE blog.user_id IN (
	SELECT follow.to_user_id FROM follow WHERE follow.from_user_id = 1)
```
## 28. Lấy số lượng user đang follow user = 1
```
SELECT COUNT(follow.from_user_id) FROM follow WHERE follow.to_user_id = 1 GROUP BY follow.to_user_id;
```
## 29. Lấy số lượng user 1 đang follow
```
SELECT COUNT(follow.to_user_id) FROM follow WHERE follow.from_user_id = 1 GROUP BY follow.from_user_id;
```
## 30. Lấy 1 comment (id_comment, comment) mới nhất và thông tin của user đang được follow bởi user 1
```
SELECT comment.id, comment.comment, user.full_name FROM comment, user
WHERE comment.user_id IN (SELECT to_user_id FROM follow WHERE from_user_id = 1)
ORDER BY comment.created_at DESC
```
## 31. Hiển thị một chuổi "PHP Team " + ngày giờ hiện tại (Ex: PHP Team 2017-06-21 13:06:37)
```
SELECT CONCAT("PHP TEAM ", CURRENT_TIMESTAMP());
```
## 32. Tìm có tên(user.full_name) "Khiêu" và các thông tin trên blog của user này như: (blog.title, blog.view), title category(category) của blog này.
```
SELECT user.full_name, blog.title, blog.view, category.title FROM user
INNER JOIN blog ON user.id = blog.user_id
INNER JOIN category ON category.id = blog.category_id
WHERE user.fullname = "Khiêu";
```
## 33. Liệt kê email user các user có tên(user.full_name) có chứa ký tự "Khi" theo danh sách như output bên dưới.
```
SELECT GROUP_CONCAT(full_name SEPARATOR ';') AS fullname FROM user
WHERE full_name like "%Khi%";
```
 
