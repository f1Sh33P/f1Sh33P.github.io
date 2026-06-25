## SQL

S(Sno, Sname, gender, birth, dept, phone, email, photo, graduated)
学号，姓名，性别，
C(Cno, Cname, credits)
E(Sno, Cno, grade)

1. 查询所有学生的姓名、性别和系别。

```sql
SELECT Sname, gender, dept
FROM S;
```

2. 查询所有女生的学号、姓名和系别。

```sql
SELECT 学号, 姓名, 系别
FROM 学生表
WHERE 性别 = "女";
```

3. 查询新闻系学生的姓名、性别、手机、电子邮件。

```sql
SELECT 姓名, 性别, 手机, 电子邮件
FROM 学生表
WHERE 系别 = "新闻";
```

4. 查询未毕业学生的姓名、手机，按姓名降序排列。

```sql
SELECT 姓名, 手机
FROM 学生表
WHERE 毕业否 = FALSE
ORDER BY 姓名 DESC;
```

5. 查询城市系和法律系学生的姓名、学号、系别。

```sql
SELECT 姓名, 学号, 系别
FROM 学生表
WHERE 系别 IN ("城市", "法律");
```

6. 查询周姓学生的姓名、学号、系别。

```sql
SELECT 姓名, 学号, 系别
FROM 学生表
WHERE 姓名 LIKE "周*";
```

7. 查询 18 岁以下的学生的姓名、性别、出生日期、系别、手机。

```sql
SELECT 姓名, 性别, 出生日期, 系别, 手机
FROM 学生表
WHERE DateDiff("yyyy", 出生日期, Date()) < 18;
```

8. 查询 1995 年至 1998 年出生学生的姓名、出生日期、手机。

```sql
SELECT 姓名, 出生日期, 手机
FROM 学生表
WHERE Year(出生日期) BETWEEN 1995 AND 1998;
```

9. 查询所有选课学生的姓名、课程名称、成绩。

```sql
SELECT 姓名, 课程名称, 成绩
FROM (学生表
    INNER JOIN 选课表 ON 选课表.学号 = 学生表.学号)
    INNER JOIN 课程表 ON 课程表.课程编号 = 选课表.课程编号;
```

10. 查询各系学生的系别、学号、姓名、课程名称、成绩。

```sql
SELECT 系别, 学生表.学号, 姓名, 课程名称, 成绩
FROM (学生表
    INNER JOIN 选课表 ON 选课表.学号 = 学生表.学号)
    INNER JOIN 课程表 ON 课程表.课程编号 = 选课表.课程编号
ORDER BY 系别;
```

11. 查询与「路飞」同年出生的所有学生的姓名、性别、系别，按姓名降序排列。

```sql
SELECT 姓名, 性别, 系别
FROM 学生表
WHERE Year(出生日期) = (
    SELECT Year(出生日期)
    FROM 学生表
    WHERE 姓名 = "路飞"
) AND 姓名 <> "路飞"
ORDER BY 姓名 DESC;
```

12. 查询至少两门成绩不及格的学生的姓名和系别。

```sql
SELECT 姓名, 系别
FROM 学生表
WHERE 学号 IN (
    SELECT 学号
    FROM 选课表
    WHERE 成绩 < 60
    GROUP BY 学号
    HAVING Count(*) >= 2
);
```

13. 查询没有不及格成绩的学生的姓名和系别。

```sql
SELECT 姓名, 系别
FROM 学生表
WHERE 学号 NOT IN (
    SELECT 学号
    FROM 选课表
    WHERE 成绩 < 60 OR 成绩 IS NULL
) AND 学号 IN (
        SELECT 学号
        FROM 选课表
    );
```

14. 查询没有选修课程的学生的学号和姓名。

```sql
SELECT 学号, 姓名
FROM 学生表
WHERE 学号 NOT IN (
    SELECT 学号
    FROM 选课表
);
```

15. 查询各系的学生人数。

```sql
SELECT
    系别,
    Count(*) AS 学生人数
FROM 学生表
GROUP BY 系别;
```

16. 查询每门课程的最高分、最低分。

```sql
SELECT
    课程名称,
    Max(成绩) AS 最高分,
    Min(成绩) AS 最低分
FROM
    课程表
    INNER JOIN 选课表 ON 选课表.课程编号 = 课程表.课程编号
GROUP BY
    课程名称;
```

17. 查询所有选课学生的学号、姓名、平均分和总分。

```sql
SELECT
    学生表.学号,
    姓名,
    Avg(成绩) AS 平均分,
    Sum(成绩) AS 总分
FROM
    学生表
    INNER JOIN 选课表 ON 选课表.学号 = 学生表.学号
GROUP BY
    学生表.学号,
    姓名;
```

18. 查询「大学计算机基础」课程成绩高于平均分的学生的姓名、成绩、系别。

```sql
SELECT 姓名, 成绩, 系别
FROM (学生表
    INNER JOIN 选课表 ON 选课表.学号 = 学生表.学号)
    INNER JOIN 课程表 ON 课程表.课程编号 = 选课表.课程编号
WHERE 课程名称 = "大学计算机基础"
    AND 成绩 > (
        SELECT Avg(成绩) AS 平均分
        FROM 课程表
            INNER JOIN 选课表 ON 选课表.课程编号 = 课程表.课程编号
        WHERE 课程名称 = "大学计算机基础"
    );
```

19. 完成学生表与选课表的左连接，并阐述其含义。

```sql
SELECT *
FROM 学生表
    LEFT OUTER JOIN 选课表 ON 学生表.学号 = 选课表.学号;
```

含义：保留学生表中的所有记录，将选课表的学号、课程编号和成绩添加到学生表中，未选课学生的选课表留空（NULL）。

20. (DDL) 创建英雄表。

```sql
CREATE TABLE 英雄表 (
    英雄编号 TEXT(100) PRIMARY KEY,
    姓名 TEXT(100),
    性别 TEXT(2),
    出生日期 DATETIME,
    电子邮箱 TEXT(100)
);
```

21. (DDL) 向英雄表中添加字段。

```sql
ALTER TABLE 英雄表
ADD COLUMN 是否坏人 BIT;
```

22. (DML) 向英雄表中添加记录。

```sql
INSERT INTO 英雄表
VALUES("1001", "杨过", "男", #1224-07-21#, "yangguo@cupl.cn", FALSE);
```

23. (DML) 更改杨过的电子邮箱。

```sql
UPDATE 英雄表
SET 电子邮箱 = "yangguo@cupl.edu.cn"
WHERE 姓名 = "杨过";
```

24. (DML) 删除英雄表中的记录。

```sql
DELETE FROM 英雄表
WHERE 姓名 = "杨过";
```

25. (DDL) 删除英雄表。

```sql
DROP TABLE 英雄表;
```

### Additional practice

26. 查询每个系年龄最大的学生的姓名、系别和出生日期。

```sql
-- Error
SELECT 姓名, 系别, 出生日期
FROM 学生表
WHERE 出生日期 IN (
    SELECT Min(出生日期)
    FROM 学生表
    GROUP BY 系别
);
-- Correct 1
SELECT 姓名, 系别, 出生日期
FROM 学生表
WHERE (系别, 出生日期) IN (
    SELECT 系别, Min(出生日期)
    FROM 学生表
    GROUP BY 系别
);
-- Correct 2: correlated subquery
SELECT 姓名, 系别, 出生日期
FROM 学生表 AS 外部表
WHERE 出生日期 = (
    SELECT Min(出生日期)
    FROM 学生表 AS 内部表
    WHERE 内部表.系别 = 外部表.系别
);
```

27. 查询成绩高于该门课程平均分的学生的学号、课程编号和成绩。

```sql
SELECT 学号, 课程编号, 成绩
FROM 选课表 AS 外部表
WHERE 成绩 > (
    SELECT Avg(成绩)
    FROM 选课表 AS 内部表
    WHERE 内部表.课程编号 = 外部表.课程编号
);
```

28. 查询所有课程的课程编号、课程名称以及选修该门课程的总人数。

```sql
SELECT
    课程表.课程编号,
    课程名称,
    Count(选课表.成绩) AS 总人数
FROM
    课程表
    LEFT JOIN 选课表 ON 选课表.课程编号 = 课程表.课程编号
GROUP BY
    课程表.课程编号,
    课程名称;
```

29. 查询选修了 3 门及以上课程的学生的学号以及他们的选课总数。

```sql
SELECT
    学号,
    Count(*) AS 选课总数
FROM 选课表
GROUP BY 学号
HAVING Count(*) >= 3;
```

30. 查询平均成绩大于等于 80 分的学生的学号、姓名以及平均成绩，按平均成绩从高到低排序。

```sql
SELECT
    选课表.学号,
    姓名,
    Avg(成绩) AS 平均成绩
FROM
    选课表
    INNER JOIN 学生表 ON 学生表.学号 = 选课表.学号
GROUP BY
    选课表.学号,
    姓名
HAVING
    Avg(成绩) >= 80
ORDER BY
    Avg(成绩) DESC;
```

31. 查询在 001、002、003 这三门课程中，最高分大于 90 分的课程编号和对应的最高分。

```sql
SELECT
    课程编号,
    Max(成绩) AS 最高分
FROM 选课表
WHERE 课程编号 IN ("001", "002", "003")
GROUP BY 课程编号
HAVING Max(成绩) > 90;
```

32. 查询选修了课程编号为 001 的所有学生的姓名和系别。

```sql
-- Method 1
SELECT 姓名, 系别
FROM 学生表
WHERE 学号 IN (
    SELECT 学号
    FROM 选课表
    WHERE 课程编号 = "001"
);
-- Method 2: correlated subquery
SELECT 姓名, 系别
FROM 学生表 AS 外部表
WHERE EXISTS (
    SELECT 1
    FROM 选课表 AS 内部表
    WHERE 内部表.学号 = 外部表.学号 AND 课程编号 = "001"
);
```

33. *查询选修了所有课程的学生姓名。

```sql
SELECT 姓名
FROM 学生表
WHERE NOT EXISTS (
    SELECT 1
    FROM 课程表
    WHERE NOT EXISTS (
        SELECT 1
        FROM 选课表
        WHERE 选课表.学号 = 学生表.学号 AND 选课表.课程编号 = 课程表.课程编号
    )
);
```

34. 查询至少有两门课程成绩都排在全校前三名的学生学号。

```sql
SELECT 学号
FROM 选课表 AS 外部表
WHERE 3 > (
    SELECT Count(*)
    FROM 选课表 AS 内部表
    WHERE 内部表.课程编号 = 外部表.课程编号 AND 内部表.成绩 > 外部表.成绩
)
GROUP BY 学号
HAVING Count(*) >= 2;
```

35. 学校决定对不及格门数最多的系进行警告，请写出一条 UPDATE 语句，将该系所有学生的「毕业否」字段修改为 FALSE。

```sql
UPDATE 学生表
SET 毕业否 = FALSE
WHERE 系别 = (
    SELECT TOP 1 系别
    FROM 学生表 INNER JOIN 选课表 ON 选课表.学号 = 学生表.学号
    WHERE 成绩 < 60
    GROUP BY 系别
    ORDER BY Count(*) DESC
);
```

36. 学校要排查一门课都没选过，或者选了课但所有课都旷考的边缘学生的学号和姓名。

```sql
-- Method 1
SELECT 学号, 姓名 FROM 学生表
WHERE NOT EXISTS (
    SELECT 1 FROM 选课表
    WHERE 选课表.学号 = 学生表.学号
) OR NOT EXISTS (
    SELECT 1 FROM 选课表
    WHERE 选课表.学号 = 学生表.学号 AND 成绩 IS NOT NULL
);
-- Method 2
SELECT 学号, 姓名 FROM 学生表
WHERE NOT EXISTS (
    SELECT 1 FROM 选课表
    WHERE 选课表.学号 = 学生表.学号 AND 成绩 IS NOT NULL
);
```

37. 查询没有选修过 001 和 002 这两门课程中任意一门的学生的学号和姓名。

```sql
-- Method 1
SELECT 学号, 姓名 FROM 学生表
WHERE 学号 NOT IN (
    SELECT DISTINCT 学号 FROM 选课表
    WHERE 课程编号 IN ("001", "002")
);
-- Method 2
SELECT 学号, 姓名 FROM 学生表
WHERE NOT EXISTS (
    SELECT 1 FROM 选课表
    WHERE 选课表.学号 = 学生表.学号
        AND 课程编号 IN ("001", "002")
);
```

38. *社交系统里有表 Users(user_id, username) 和表 Follows(user_id, follow_id)，查询拥有完全相同的关注列表的两个人的 user_id。

```sql
SELECT A.user_id AS user_A, B.user_id AS user_B
FROM Users AS A, Users AS B
WHERE A.user_id < B.user_id AND NOT EXISTS (
    SELECT * FROM Follows AS FA
    WHERE FA.user_id = A.user_id AND NOT EXISTS (
        SELECT * FROM Follows AS FB
        WHERE FB.user_id = B.user_id AND FB.follow_id = FA.follow_id
    )
) AND NOT EXISTS (
    SELECT * FROM Follows AS FB
    WHERE FB.user_id = B.user_id AND NOT EXISTS (
        SELECT * FROM Follows AS FA
        WHERE FA.user_id = A.user_id AND FA.follow_id = FB.follow_id
    )
);
```

