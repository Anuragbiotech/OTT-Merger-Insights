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

3. **User Demographics**

What is the distribution of users by age group, city tier, and subscription plan for each platform?

```sql
SELECT	age_group,
	city_tier,
	subscription_plan,
	SUM(CASE WHEN source_db = 'Jotstart' THEN count_user ELSE 0 END) AS jotstar_content_type,
	SUM(CASE WHEN source_db = 'Liocinema' THEN count_user ELSE 0 END) AS liocinema_content_type
FROM	(
	SELECT	'Jotstart' AS source_db,
		age_group,
		city_tier,
		subscription_plan,
		COUNT(user_id) AS count_user
	FROM	jotstar_db.subscribers
	GROUP BY
		age_group, city_tier, subscription_plan

	UNION ALL

	SELECT	'Liocinema' AS source_db,
		age_group,
		city_tier,
		subscription_plan,
		COUNT(user_id) AS count_user
	FROM	liocinema_db.subscribers
        GROUP BY
		age_group, city_tier, subscription_plan
		) AS demographics
GROUP BY
	age_group, city_tier, subscription_plan
ORDER BY
	age_group, city_tier, subscription_plan
;
```
|age_group| city_tier| subscription_plan| jotstar_content_type| liocinema_content_type|
|---------|----------|------------------|---------------------|-----------------------|
|18-24	  |Tier 1    |     Basic	|          0	      |        5297           |
|18-24	  |Tier 1    |     Free	        |         916	      |        8126           |
|18-24	  |Tier 1    |     Premium	|        1773	      |        4577           |
|18-24	  |Tier 1    |     VIP	        |        1704	      |           0
|18-24	  |Tier 2    |     Basic	|           0	      |        9924
|18-24	  |Tier 2    |     Free	        |         689	      |       13806
|18-24	  |Tier 2    |     Premium	|         424	      |        3803
|18-24	  |Tier 2    |     VIP	        |        1166	      |           0
|18-24	  |Tier 3    |     Basic	|           0	      |        7951
|18-24	  |Tier 3    |     Free	        |         514	      |       23748
|18-24	  |Tier 3    |     Premium	|         108	      |        2581
|18-24	  |Tier 3    |     VIP	        |         382	      |           0
|25-34	  |Tier 1    |     Basic	|           0	      |        3611
|25-34	  |Tier 1    |     Free	        |        2259	      |        5251
|25-34	  |Tier 1    |     Premium	|        4537	      |        2866
|25-34	  |Tier 1    |     VIP	        |        4648	      |           0
|25-34	  |Tier 2    |     Basic	|           0	      |        6247
|25-34	  |Tier 2    |     Free	        |        1884	      |        9419
|25-34	  |Tier 2    |     Premium	|        1118	      |        2492
|25-34	  |Tier 2    |     VIP	        |        3038	      |           0
|25-34	  |Tier 3    |     Basic	|           0	      |        5254
|25-34	  |Tier 3    |     Free	        |        1315	      |       15339
|25-34	  |Tier 3    |     Premium	|         283	      |        1548
|25-34	  |Tier 3    |     VIP	        |         987	      |           0
|35-44	  |Tier 1    |     Basic	|           0	      |        2038
|35-44	  |Tier 1    |     Free	        |        1305	      |        3194
|35-44	  |Tier 1    |     Premium	|        2602	      |        1788
|35-44	  |Tier 1    |     VIP	        |        2501	      |           0
|35-44	  |Tier 2    |     Basic	|           0	      |        4249
|35-44	  |Tier 2    |     Free 	|         990	      |        5642
|35-44	  |Tier 2    |     Premium	|         656	      |        1665
|35-44	  |Tier 2    |     VIP	        |        1788	      |           0
|35-44	  |Tier 3    |     Basic	|           0	      |        3370
|35-44	  |Tier 3    |     Free	        |         716	      |        9622
|35-44	  |Tier 3    |     Premium	|         159	      |         992
|35-44	  |Tier 3    |     VIP	        |         557	      |           0
|45+	  |Tier 1    |     Basic	|           0	      |        1347
|45+	  |Tier 1    |     Free	        |         631	      |        1841
|45+	  |Tier 1    |     Premium	|        1266	      |        1075
|45+	  |Tier 1    |     VIP	        |        1309	      |           0
|45+	  |Tier 2    |     Basic	|           0	      |        2150
|45+	  |Tier 2    |     Free	        |         501	      |        3321
|45+	  |Tier 2    |     Premium	|         368	      |        1130
|45+	  |Tier 2    |     VIP	        |         802	      |           0
|45+	  |Tier 3    |     Basic	|           0	      |        1924
|45+	  |Tier 3    |     Free	        |         376	      |        5683
|45+	  |Tier 3    |     Premium	|          73	      |         575
|45+	  |Tier 3    |     VIP	        |         275	      |           0


4. **Active vs. Inactive Users**

What percentage of LioCinema and Jotstar users are active vs. inactive? How do these rates vary by age group and subscription plan?

```sql
WITH user_activity AS (
SELECT	'Jotstar' AS source_db,
	'active' AS activity_status,
        ROUND(COUNT(CASE WHEN last_active_date IS NULL THEN 1 END) / COUNT(*) * 100, 2) AS percentage 
FROM	jotstar_db.subscribers

UNION ALL

SELECT	'Jotstar' AS source_db,
	'inactive' AS activity_status,
        ROUND(COUNT(CASE WHEN last_active_date IS NOT NULL THEN 1 END) / COUNT(*) * 100, 2)
FROM	jotstar_db.subscribers

UNION ALL

SELECT	'Liocinema' AS source_db,
	'active' AS activity_status,
        ROUND(COUNT(CASE WHEN last_active_date IS NULL THEN 1 END) / COUNT(*) * 100, 2)
FROM	liocinema_db.subscribers

UNION ALL

SELECT	'Liocinema' AS source_db,
	'inactive' AS activity_status,
        ROUND(COUNT(CASE WHEN last_active_date IS NOT NULL THEN 1 END) / COUNT(*) * 100, 2)
FROM	liocinema_db.subscribers
)
SELECT	MAX(CASE WHEN source_db = 'Jotstar' AND activity_status = 'active' THEN percentage END) AS jotstar_active_users,
	MAX(CASE WHEN source_db = 'Jotstar' AND activity_status = 'inactive' THEN percentage END) AS jotstar_inactive_users,
        MAX(CASE WHEN source_db = 'Liocinema' AND activity_status = 'active' THEN percentage END) AS liocinema_active_users,
        MAX(CASE WHEN source_db = 'Liocinema' AND activity_status = 'inactive' THEN percentage END) AS liocinema_inactive_users
FROM	user_activity
;
```

![image](https://github.com/user-attachments/assets/7c9e579d-0d31-4cbd-a275-6ec16523b611)


```sql
-- By age-group and subscription plan for jotstar

SELECT	age_group,
		subscription_plan,
		ROUND(COUNT(CASE WHEN last_active_date IS NULL THEN 1 END) / COUNT(*) * 100, 2) AS jotstar_active_users,
		ROUND(COUNT(CASE WHEN last_active_date IS NOT NULL THEN 1 END) / COUNT(*) * 100, 2) AS jotstar_inactive_users
FROM 	jotstar_db.subscribers
GROUP BY 
		age_group, subscription_plan
ORDER BY
		age_group, subscription_plan
;
```
![image](https://github.com/user-attachments/assets/29bf7275-2bcd-4463-b21c-10fca4b55d86)


```sql
-- By age-group and subscription plan for liocinema
SELECT	age_group,
		subscription_plan,
		ROUND(COUNT(CASE WHEN last_active_date IS NULL THEN 1 END) / COUNT(*) * 100, 2) AS liocinema_active_users,
		ROUND(COUNT(CASE WHEN last_active_date IS NOT NULL THEN 1 END) / COUNT(*) * 100, 2) AS liocinema_inactive_users
FROM 	liocinema_db.subscribers
GROUP BY 
		age_group, subscription_plan
ORDER BY
		age_group, subscription_plan
;
```
![image](https://github.com/user-attachments/assets/3c688c4f-a7a1-44a1-9234-569881438827)

