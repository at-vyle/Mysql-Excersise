
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
