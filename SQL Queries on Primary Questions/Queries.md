1. **Total Users & Growth Trends**

   What is the total number of users for LioCinema and Jotstar, and how do they compare in terms of growth trends (January–November 2024)?
```sql
-- Total Users for each OTT
SELECT	(SELECT	COUNT(user_id) FROM jotstar_db.subscribers) AS jotstar_total_users,
        (SELECT COUNT(user_id) FROM liocinema_db.subscribers) AS liocinema_total_users
;
```

  ![image](https://github.com/user-attachments/assets/3e3e73ef-db29-4c8e-9db0-ab4b25c31acc)
  

```sql
-- Growth Trends
SELECT  month,
        SUM(CASE WHEN source_db = 'Jotstar' THEN new_subscribers ELSE 0 END) AS jotstar_new_subscribers,
        SUM(CASE WHEN source_db = 'Liocinema' THEN new_subscribers ELSE 0 END) AS liocinema_new_subscribers
FROM    (
        -- Monthly growth for Jotstar
        SELECT  DATE_FORMAT(subscription_date, '%Y-%m-01') AS month,
                'Jotstar' AS source_db,
                COUNT(*) AS new_subscribers
        FROM    jotstar_db.subscribers
        WHERE   subscription_date >= "2024-01-01"
        AND     subscription_date <= "2024-11-30"
        GROUP BY
                month
				
        UNION ALL

        -- Monthly growth for Liocinema
        SELECT  DATE_FORMAT(subscription_date, '%Y-%m-01') AS month,
                'Liocinema' AS source_db,
                COUNT(*) AS new_subscribers
        FROM    liocinema_db.subscribers
        WHERE   subscription_date >= "2024-01-01"
        AND     subscription_date <= "2024-11-30"
        GROUP BY
                month
        ) AS growth -- Subquery
GROUP BY
        month
ORDER BY
        month
;
```
![image](https://github.com/user-attachments/assets/11785cd3-f83c-459e-b8a0-8586c9475917)


2. **Content Library Comparison**

What is the total number of contents available on LioCinema vs. Jotstar? How do they differ in terms of language and content type?

```sql
-- Total number of contents

SELECT  (SELECT	COUNT(content_id) FROM	jotstar_db.contents) AS jotstar_total_content,
        (SELECT COUNT(content_id)  FROM	liocinema_db.contents) AS liocinema_total_content
;
```

![image](https://github.com/user-attachments/assets/90f49a60-5568-4753-ab55-7f2b52783e7e)

```sql
-- Language
SELECT  language,
        SUM(CASE WHEN source_db = 'Jotstart' THEN count_content ELSE 0 END) AS jotstar_language,
        SUM(CASE WHEN source_db = 'Liocinema' THEN count_content ELSE 0 END) AS liocinema_language
FROM    (
        SELECT  'Jotstart' AS source_db,
                language,
                COUNT(content_id) AS count_content
        FROM    jotstar_db.contents
        GROUP BY
                language

        UNION ALL

        SELECT  'Liocinema' AS source_db,
                language,
                COUNT(content_id) AS count_content
        FROM    liocinema_db.contents
        GROUP BY
                language
        ) AS lang
GROUP BY
        language
ORDER BY
        language
;
```

![image](https://github.com/user-attachments/assets/a39a04ef-7ba8-4a66-8147-57807469f7f1)


```sql
-- Content Types
SELECT  content_type,
        SUM(CASE WHEN source_db = 'Jotstart' THEN count_content ELSE 0 END) AS jotstar_content_type,
        SUM(CASE WHEN source_db = 'Liocinema' THEN count_content ELSE 0 END) AS liocinema_content_type
FROM    (
        SELECT  'Jotstart' AS source_db,
                content_type,
                COUNT(content_id) AS count_content
        FROM    jotstar_db.contents
        GROUP BY
                content_type

        UNION ALL

        SELECT  'Liocinema' AS source_db,
                content_type,
                COUNT(content_id) AS count_content
        FROM    liocinema_db.contents
        GROUP BY
                content_type
        ) AS content
GROUP BY
        content_type
ORDER BY
        content_type
;
```

![image](https://github.com/user-attachments/assets/d5453dea-8211-4d69-a465-0ee0be6fd338)



