## 4.	Select 10 blog mới nhất đã active
```
SELECT * FROM blog WHERE blog.is_active = 1 ORDER BY blog.created_at DESC LIMIT 10;
```