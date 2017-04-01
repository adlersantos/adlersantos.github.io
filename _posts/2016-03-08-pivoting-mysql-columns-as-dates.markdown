---
title:  "MySQL: Columns as Ordered Week Dates"
date:   2016-03-08
---

Let's say you have data containing some metrics and their values across an ordered set of dates in a week. Since most screens are longer horizontally than vertically, it's sometimes better to present data where one metric lies in a row and the dates lie in columns, rather than the usual way around.

The usual way we show tables is like this:

| date       | Visitors | Orders | Revenue | Metric4 | etc. |
|------------|----------|--------|---------|---------|------|
| 2016-02-28 | 1423     | 19     | 900     | ...     | ...  |
| 2016-02-29 | 1534     | 38     | 2037    | ...     | ...  |
| 2016-03-01 | 2645     | 57     | 5612    | ...     | ...  |
| ...        | ...      | ...    | ...     | ...     | ...  |

Because most screens are in landscape mode and because we read from left to right, there are times when it makes sense to pivot the table as follows:

| metric   | 2016-02-28 | 2016-02-29 | 2016-03-01 | ... |
|----------|------------|------------|------------|-----|
| Visitors | 1423       | 1534       | 2645       | ... |
| Orders   | 19         | 38         | 57         | ... |
| Revenue  | 900        | 2037       | 5612       | ... |
| Metric4  | ...        | ...        | ...        | ... |
| Metric5  | ...        | ...        | ...        | ... |
| etc.     | ...        | ...        | ...        | ... |

This may not be "tidy data" as defined by Hadley Wickham in his [excellent paper](https://www.jstatsoft.org/article/view/v059i10), but pivoting as such results in easier navigation/scrolling when you have more metrics than dates.

For example, let's say you have a weekly report with 7 consecutive dates from Monday to Sunday of that week, and 30 metrics. You've seen the 7-day trend on the first metric, and would like to see the trend on the 27th metric. All you need to do is scroll down to the 27th metric, and read as you would (from left to right), to see if there's any trend seen across that week.

Here's the full MySQL query for the pivot table above, where the rows are metrics and the columns comprise of an ordered set of dates in a week.

```
SELECT
    metric
    , SUM(CASE WHEN DAYOFWEEK(date) = 1 THEN value ELSE 0 END)
    , SUM(CASE WHEN DAYOFWEEK(date) = 2 THEN value ELSE 0 END)
    , SUM(CASE WHEN DAYOFWEEK(date) = 3 THEN value ELSE 0 END)
    , SUM(CASE WHEN DAYOFWEEK(date) = 4 THEN value ELSE 0 END)
    , SUM(CASE WHEN DAYOFWEEK(date) = 5 THEN value ELSE 0 END)
    , SUM(CASE WHEN DAYOFWEEK(date) = 6 THEN value ELSE 0 END)
    , SUM(CASE WHEN DAYOFWEEK(date) = 7 THEN value ELSE 0 END)
FROM
    (
        SELECT
            metrics.name AS 'metric'
            , metrics.rank AS 'rank'
            , this_week.date AS 'date'
            , (
                CASE metrics.name
                WHEN ''         THEN this_week.date
                WHEN 'Visitors' THEN traffic.visitors
                WHEN 'Orders'   THEN orders.num
                WHEN 'Revenue'  THEN orders.revenue
                WHEN 'Metric4'  THEN table.metric4
                WHEN 'Metric5'  THEN table.metric5
                ...
                END
            ) AS 'value'
        FROM
            (
                SELECT 1 AS rank, '' AS name
                UNION ALL
                SELECT 2 AS rank, 'Visitors' AS name
                UNION ALL
                SELECT 3 AS rank, 'Orders' AS name
                UNION ALL
                SELECT 4 AS rank, 'Revenue' AS name
                UNION ALL
                SELECT 5 AS rank, 'Metric4' AS name
                UNION ALL
                SELECT 6 AS rank, 'Metric5' AS name
            ) metrics
            LEFT JOIN (
                SELECT SUBDATE(CURDATE(), DAYOFWEEK(CURDATE()) - 1) AS 'date'
                UNION ALL
                SELECT SUBDATE(CURDATE(), DAYOFWEEK(CURDATE()) - 2) AS 'date'
                UNION ALL
                SELECT SUBDATE(CURDATE(), DAYOFWEEK(CURDATE()) - 3) AS 'date'
                UNION ALL
                SELECT SUBDATE(CURDATE(), DAYOFWEEK(CURDATE()) - 4) AS 'date'
                UNION ALL
                SELECT SUBDATE(CURDATE(), DAYOFWEEK(CURDATE()) - 5) AS 'date'
                UNION ALL
                SELECT SUBDATE(CURDATE(), DAYOFWEEK(CURDATE()) - 6) AS 'date'
                UNION ALL
                SELECT SUBDATE(CURDATE(), DAYOFWEEK(CURDATE()) - 7) AS 'date'
            ) this_week on 1 = 1
            LEFT JOIN (
                SELECT
                    SUM(visitors) AS 'visitors'
                FROM
                    some_table1
                GROUP BY
                    date
            ) traffic ON traffic.date = this_week.date
            LEFT JOIN (
                SELECT
                    COUNT(*) AS 'num'
                    SUM(amount_paid) AS 'revenue'
                FROM
                    some_table2
                GROUP BY
                    date
            ) orders ON orders.date = this_week.date
    ) dataframe
GROUP BY metric
ORDER BY rank
```

The `rank` variable (an integer) allows you to show the metrics in a specific order. A metric with a certain rank will be shown before another metric with a higher numbered rank.

Hope this helps!
