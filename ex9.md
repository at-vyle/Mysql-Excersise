## 9.	Lấy số lượng comment của các blog
```
SELECT `target_table`, `target_id`, COUNT(`id`) as SL FROM comment 
WHERE `target_table`='blog'
GROUP BY `target_id`, `target_table`;
```