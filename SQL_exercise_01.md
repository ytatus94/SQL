# SQL exercise 01

### Consider the Sailors-Boats-Reserves DB described in the text.
 * s (sid, sname, rating, age)
 * b (bid, bname, color)
 * r (sid, bid, date)
 
 Write each of the following queries in SQL.
 
* Find the colors of boats reserved by Albert.
  * 這裡是不需要使用 `JOIN` 的另一種寫法 

```SQL
SELECT color
FROM b, s, r
WHERE b.bid = r.bid AND
      r.sid = s.sid AND
      s.sname = 'Albert';
```

* Find all sailor id’s of sailors who have a rating of at least 8 or reserved boat 103.
  * 因為 `UNION` 會去除 duplicate 的，且題目是要 `OR` 所以可以把 `rating >= 8` 的 query 和 `bid = 103` 的 query 分開寫，然後用 `UNION` 合併

```SQL
SELECT sid
FROM s
WHERE rating >= 8
UNION
SELECT sid
FROM r
WHERE bid = 103;
```

* Find the names of sailors who have not reserved a boat whose name contains the string “storm”. Order the names in ascending order.
  * 有 reserved a boat 就會有 bid，所以要看 r 或 b 表格。因為 r 有 sid 可以和 s 合併，所以看 r 就好
  * r 中存在的 sid 就是有 reserved a boat 的，只要 sid 不在 r 中就是沒有 reserved a boat 的
  * 題目應該是船的名字包含 strom 才對，所以 subquery 應該是 `SELECT sid FROM r, b WHERE r.bid = b.bid AND bname LIKE '%LIKE%'`

```SQL
SELECT sname
FROM s s1
WHERE sid NOT IN (
    SELECT sid
    FROM r, s
    WHERE r.sid = s.sid AND
          sname LIKE '%storm%'
)
ORDER BY s1.sname;
```

* Find the sailor id’s of sailors with age over 20 who have not reserved a boat whose name includes the string “thunder”.
  * 這邊是說 boat 的名字內含有 thunder (不是說 sailor 的名字有 thunder) 

```SQL
SELECT sid
FROM s
WHERE age > 20 AND sid NOT IN (
    SELECT sid
    FROM r, b
    WHERE r.bid = b.bid AND
          bname LIKE '%thunder%'
);
```

* Find the names of sailors who have reserved at least two boats.

```SQL
SELECT sname
FROM s, r r1, r r2
WHERE s.sid = r1.sid AND
      s.sid = r2.sid AND
      r1.bid <> r2.bid;
```

* Find the names of sailors who have reserved all boats.

```SQL
SELECT sname
FROM s
WHERE NOT EXISTS (
    SELECT *
    FROM b
    WHERE NOT EXISTS (
        SELECT *
        FROM r
        WHERE r.sid = s.sid AND
              r.bid = b.bid
    )
);
```

* Find the names of sailors who have reserved all boats whose name starts with “typhoon”.

```SQL
SELECT sname
FROM s
WHERE NOT EXISTS (
    SELECT *
    FROM b
    WHERE bname LIKE `typhoon%’ AND NOT EXISTS (
        SELECT *
        FROM r
        WHERE r.sid=s.sid AND
        r.bid=b.bid
    )
);
```

* Find the sailor id’s of sailors whose rating is better than some sailor called Bob.
  * 可能有很多 Bob，只要比其中一個 rating 高就好 

solution 1:

```SQL
SELECT s1.sid
FROM s s1, s s2
WHERE s1.rating > s2.rating AND
      s2.sname=`Bob’;
```

solution 2:

```SQL
SELECT sid
FROM s
WHERE rating > ANY (
    SELECT rating
    FROM s s2
    WHERE s2.sname=`Bob’
);
```

* Find the sailor id’s of sailors whose rating is better than every sailor called Bob.
  * 可能有很多 Bob，要比全部的 Bob rating 高

```SQL
SELECT sid
FROM s
WHERE rating > ALL (
    SELECT rating
    FROM s s2
    WHERE s2.sname=`Bob’
)
```

* Find the sailor id’s of sailors with the highest rating.

```SQL
SELECT sid
FROM s s1
WHERE s1.rating >= ALL (
    SELECT rating 
    FROM s
    )
```

* Find the name and age of the oldest sailor.

solution 1:

```SQL
SELECT s1.sname, s1.age
FROM s s1
WHERE NOT EXISTS (
    SELECT *
    FROM s s2
    WHERE s2.age > s1.age
    )
```

solution 2:

```SQL
SELECT s1.sname, s1.age
FROM s s1
WHERE s1.age >= ALL (
    SELECT age
    FROM s
    )
```

* Find the names of sailors who have reserved every boat reserved by those with a lower rating.

solution 1

```SQL
SELECT s1.sname
FROM s s1
WHERE NOT EXISTS (
    SELECT *
    FROM b, r, s s2
    WHERE s2.sid=r.sid AND 
          s2.bid=b.bid AND 
          s2.rating < s1.rating AND
          NOT EXISTS (
              SELECT *
              FROM r r2
              WHERE s1.sid=r2.sid AND
              r2.bid=b.bid
              )
    )
```

solution 2:

```SQL
SELECT s1.sname
FROM s s1
WHERE NOT EXISTS (
    (SELECT b1.bid
     FROM b b1, r, s s2
     WHERE s2.rating < s1.rating AND
           s2.sid=r.sid
    )
    EXCEPT (
        SELECT b2.bid
        FROM b b2, r r2
        WHERE r2.bid = b.bid AND
              r2.sid=s1.sid
        )
    )
```

* For each rating, find the average age of sailors at that level of rating.

```SQL
SELECT rating, AVG(age)
FROM s
GROUP BY rating
```

* For each boat which was reserved by at least 5 distinct sailors, find the boat id and the average age of sailors who reserved it.

solution 1:

```SQL
SELECT b.bid, AVG(age)
FROM b, r, s
WHERE b.bid=r.bid AND
      r.sid=s.sid
GROUP BY bid
HAVING 5< = COUNT(DISTINCT r.sid)
```

solution 2:

```SQL
SELECT b1.bid, AVG(s.age)
FROM r, s (
    SELECT bid
    FROM b, r r2
    WHERE b.bid = r2.bid
    GROUP BY bid
    HAVING 5<=COUNT(DISTINCT r2.sid)
    ) b1
WHERE b1.bid=r.bid AND
      r.sid=s.sid
GROUP BY b1.bid
```

* For each boat which was reserved by at least 5 sailors with age >= 40, find the boat id and the average age of such sailors.

```SQL
SELECT bid, AVG(age)
FROM b, r, s
WHERE s.age>=40 AND
      b.bid=r.bid AND
      s.sid=r.sid
GROUP BY bid
HAVING 5 <= COUNT(DISTINCT s.sid)
```

* For each boat which was reserved by at least 5 sailors with age >= 40, find the boat id and the average age of all sailors who reserved the boat.

```SQL
SELECT b1.bid, AVG(s.age)
FROM r, s (
    SELECT bid
    FROM b, r r2, s s2
    WHERE b.bid = r2.bid AND
          s2.sid = r.sid AND
          s.age >= 40
    GROUP BY bid
    HAVING 5 <= COUNT(DISTINCT r2.sid)
    ) b1
WHERE b1.bid=r.bid AND
      r.sid=s.sid
GROUP BY b1.bid
```
