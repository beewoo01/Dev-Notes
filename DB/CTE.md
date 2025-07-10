# 계층형 질의 예시 (MySQL / MaraiDB)

| membr_id | manager_id |
|----------|-----------|
| 1000 | null |
|1001 | 1000 |
|1002 | 1001 |
|1003 | 1002 |
|1004 | 1003 |
|1005 | 1004 |
|1006 | 1005 |
|1007 | 1006 |


```sql
WITH RECURSIVE CTE(member_id, manager_id, lvl)
AS (
  SELECT member_id, manager_id, 0 AS lvl
  FROM MAMBER
  WHERE manager_id IS NULL
  UNION ALL
  SELECT a.member_id, a.manager_id, b.lvl + 1
  FROM MEMBER a
  JOIN CTE AS b
  ON a.manager_id = b.member_id
)

SELECT member_id, mamager_id, lvl
FROM CTE
ORDER BY member_id, lvl;
```

|member_id|manager_id|lvl|
|---------|----------|---|
|1000|null|0|
|1001|1000|1|
|1002|1001|2|
|1003|1002|3|
|1004|1003|3|
|1005|1004|1|
|1006|1005|2|
|1007|1006|3|



## 해석
WITH RECURSIVE : 재귀호출을 하겠다.
CTE : common table expression
아래에 있는 쿼리들을 이용해서 CTE라는 테이블을 만들겠다는 뜻
