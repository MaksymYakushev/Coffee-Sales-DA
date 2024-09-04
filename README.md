# Coffee-Sales-DA

## Table of Contents
- [About the Project](#about-the-project)
- [About the Dataset](#about-the-dataset)
  - [Contents](#contents)
- [pgAdmin 4](#pgAdmin-4)
  - [Table Configuration in pgAdmin 4](#table-configuration-in-pgAdmin-4)
- [Data Cleaning and Preprocessing](#data-cleaning-and-preprocessing)
- [Data Analysis](#data-analysis)
  - [Determining the peak time](#1-determining-the-peak-time)
  - [Determining the popularity of orders for different types of coffee](#2-determining-the-popularity-of-orders-for-different-types-of-coffee) 


## About the Project

Hello, everyone! This project is related to data analysis and visualization, and that it's one of the projects that will help me pursue a career as a data analyst. So, I will be detailing what I am doing and why, starting from data cleaning to presenting the results. Let's go!

## About the Dataset 
І used this dataset for data analysis and visualization. This dataset was provided by a Ukrainian cafe in Odesa. It consists of data on coffee purchases, the time of purchase, price, and type of payment.

[Dataset](https://github.com/MaksymYakushev/Cooffe-Sales-DA/blob/main/Data/coffee_dataset.csv)

### Contents
- `date:` date of the transaction (YYYY-MM-DD)	
- `datetime:` the exact time of the transaction	(HH:MM:SS)
- `cash_type:` paymaent type (cash/ card)
- `card:` transaction number
- `money:` transaction amount	
- `coffee_name:` the name of the coffee

## pgAdmin 4

So, I chosed pgAdmin 4 for my data analysis project because it's a powerful, open-source management tool for PostgreSQL, one of the most advanced and widely-used relational databases. pgAdmin 4 provides an intuitive graphical interface that simplifies complex database operations, such as querying, managing database schemas, and performing administrative tasks. The program also supports advanced features like SQL query writing with syntax highlighting, graphical query plan visualization, and the ability to manage multiple database connections simultaneously. Additionally, pgAdmin 4 is highly customizable, allowing me to tailor the environment to my specific needs, which significantly enhances my productivity during data analysis. Its cross-platform compatibility and active development community ensure that it is a reliable tool that receives regular updates and improvements. Overall, pgAdmin 4 offers the perfect balance of functionality, ease of use, and flexibility for efficiently handling complex data analysis tasks.

### Table Configuration in pgAdmin 4

I created a database on my own server, which I named `coffee_db`. Then, I created a table called `coffee`, where I added all the columns with data and their data types.

| Column | Data type |
|---|---|
| date | date |
| datetime | timestamp without time zone |
| cash_type | text |
| card | text |
| money | numeric |
| coffee_name | text |

## Structure and Project Plan

1. Data Cleaning and Preprocessing [click](#data-cleaning-and-preprocessing)
2. Data Analysis [click](#data-analysis)

## Data Cleaning and Preprocessing 
1. I removed the `card` column. It contains data about the transaction number which is not needed for my analysis. I used command `DROP COLUMN` to delete a column. ʼ
   
   ```sql
    ALTER TABLE coffee
    DROP COLUMN card
   ```
2. I added a new column named `money_euro.` This column contains data on prices in Euros, where 1 Euro equals 44 Hryvnias. I used command `ADD COLUMN` to add a new one column with the data type of numeric.
   
   ```sql
    ALTER TABLE coffee
    ADD COLUMN money_euro NUMERIC
   ```
   I got a column with data which eqequals NULL. Next, I updated the column data using `UPDATE` command. Also, I used the `ROUND` function to round numbers to a specified number of decimal places.

   ```sql
   UPDATE coffee
   SET money_euro = ROUND(money / 44, 2);
   ```
3. Next I checked `date` and `datetime` columns. I used the functions `MIN` and `MAX`.

   ```sql
   SELECT 
	    MIN(date) AS MIN_date
	    , MAX(date) AS MAX_date
	    , MIN(datetime) AS MIN_datetime
	    , MAX(datetime) AS MAX_datetime
   FROM coffee
   ```
   Result:
   | | MIN_date | MAX_date | MIN_datetime | MAX_datetime |
   |---|---|---|---|---|
   | 1 | 2024-03-01 | 2024-07-17 | 07:33:06 | 22:55:37 |
   
   As a result I obtained accurate results. The cafe opens around 7 AM and closes at 11 PM.

4. Next I checked `cash_type`. I used function `COUNT` to count the number of rows.

   ```sql
   SELECT
        COUNT(*)
   FROM coffee
   ```
   I got 976. Next I counted `cash_type` the number of `cash_type` by grouping by `cash_type`.

    ```sql
   SELECT
	   cash_type
	   , COUNT(cash_type)
   FROM coffee
   GROUP BY 1;
   ```
   Result:
   
   | cash_type | count |
   |---|---|
   | 1 | cash | 89 |
   | 2 | card | 887 |
   
   I took the same number as the rows.

5. Next I checked `money`. 

    ```sql
   SELECT 
	   MIN(money)
	  , MAX(money)
   FROM coffee;
   ```
   Result:

   |  | min | max |
   |---|---|---|
   | 1 | 23.02 | 40 |
   
   I got good and adequate results.
    
   ```sql
   SELECT 
	   COUNT(money)
   FROM coffee
   WHERE money IS NULL
   ```
   Result:
   
   |  | count |
   |---|---|
   | 1 | 0 |
   
   And here I got 0 values which equals NULL.

6. Finnaly, I checked `coffee_name`.
   ```sql
   SELECT 
	   COUNT(coffee_name)
   FROM coffee
   WHERE coffee_name IS NULL
   ```
    Result:
   
   |  | count |
   |---|---|
   | 1 | 0 |
   
   I got 0 values which equals NULL.

## Data Analysis

### 1. Determining the peak time

1.1 The most peak time on weekdays
   
We know that people love coffee and always drink it—whether on their way to work, during work, at lunch with colleagues, in the evening after a long day, or simply while taking a walk. Therefore, I suggest dividing the café's working hours into four parts, which are as follows:
   
| Time: | Determining |
|---|---|
|07:00 - 09:59| Morning time |
|10:00 - 12:59| Lunch time |
|13:00 - 15:59| Dinner time |
|16:00 - 19:59| Evening time |
|20:00 - 23:00| Late hours |
   
And I also added a new column called  `day_of_week`. That column contains the day of week (Monday, Tuesday, ...):
```sql
ALTER TABLE coffee
ADD COLUMN day_of_week VARCHAR(15);

UPDATE coffee
SET day_of_week = TO_CHAR(date, 'FMDay')
```

a) The most profitable day
```sql
SELECT 
    day_of_week AS day
    , SUM(money) AS profit
FROM 
    coffee
GROUP BY 1
ORDER BY 2 DESC
```
  
Result:
   
| | day | profit |
|---|---|---|
| 1 | Tuesday | 5102.06|
| 2 | Thursday | 4885.32 |
| 3 | Sunday | 4849.86 |
| 4 | Friday | 4681.94 |
| 5 | Saturday | 4643.16 |
| 6 | Monday | 4541.08 |
| 7 | Wednesday | 4363.22|

**Visualization:**

![The Most Profitable Day Visualization](Images/1.1%20A%20(visualization).png)

The most profitable day is Tuesday 5102.06 and the least profitable day is Wednesday 4363.22. Tuesday is the most profitable day, it would be wise to focus on marketing campaigns and offers that sustain this trend. On Wednesday, additional promotions or discounts could be introduced to boost profits.
   
b) The most profitable hours
```sql
SELECT 
    day_of_week AS day
    , SUM(CASE 
	    WHEN EXTRACT(HOUR FROM datetime) BETWEEN 7 AND 9 
	        OR (EXTRACT(HOUR FROM datetime) = 9 AND EXTRACT(MINUTE FROM datetime) = 59) THEN money 
            ELSE 0 
          END) AS morning_time
    , SUM(CASE 
            WHEN EXTRACT(HOUR FROM datetime) BETWEEN 10 AND 12 
            	OR (EXTRACT(HOUR FROM datetime) = 12 AND EXTRACT(MINUTE FROM datetime) = 59) THEN money
            ELSE 0 
          END) AS lunch_time
    , SUM(CASE 
            WHEN EXTRACT(HOUR FROM datetime) BETWEEN 13 AND 15 
            	OR (EXTRACT(HOUR FROM datetime) = 15 AND EXTRACT(MINUTE FROM datetime) = 59) THEN money
            ELSE 0 
          END) AS dinner_time
    , SUM(CASE 
            WHEN EXTRACT(HOUR FROM datetime) BETWEEN 16 AND 19 
            	OR (EXTRACT(HOUR FROM datetime) = 19 AND EXTRACT(MINUTE FROM datetime) = 59) THEN money 
            ELSE 0 
          END) AS evening_time
    , SUM(CASE 
            WHEN EXTRACT(HOUR FROM datetime) BETWEEN 20 AND 23 
            	OR (EXTRACT(HOUR FROM datetime) = 23 AND EXTRACT(MINUTE FROM datetime) = 00) THEN money 
            ELSE 0 
          END) AS late_hours
FROM coffee
GROUP BY 1
```

Result:
   
| day | morning_time | lunch_time | dinner_time | evening_time | late_hours |
|----------|--------------|------------|-------------|--------------|------------|
| Tuesday | 393.46 | 1504.28 | 878.80 | 1724.96 | 600.56 |
| Saturday | 324.38 | 1456.32 | 1028.20 | 1421.68 | 412.58 |
| Friday | 291.76 | 1104.08 | 1060.84 | 1488.52 | 736.74 |
| Thursday | 370.82 | 1209.68 | 860.18 | 1626.28 | 818.36 |
| Wednesday | 319.88 | 1246.56 | 914.76 | 1306.92 | 575.10 |
| Sunday | 374.58 | 1487.80 | 840.08 | 1375.18 | 772.22 |
| Monday | 93.56 | 1589.32 | 942.00 | 1508.92 | 407.28 |

**Visualization:**

![The Most Profitable Hours Visualization](Images/1.1%20B%20(vizualization).png)

**General trends:**

**Highest activity.** On all days except Monday there is a peak in activity or consumption during lunch. On Monday this indicator is even higher than on other days.
**Significant consumption in the morning and evening.** Morning and evening times also show fairly high levels of consumption although they do not exceed the values characteristic of lunchtime.
**Lowest consumption during late hours.** On all days of the week the lowest consumption is recorded during late hours although these values vary significantly from day to day.

**Specific days:**

**Monday.** Significantly lower consumption in the morning (93.56) but a record high during lunch (1589.32). Evening and late hours have moderate values. 
**Saturday:** Consistently high consumption in the morning (324.38) and at lunchtime (1456.32) with a peak in the evening (1421.68). 
**Thursday:** Even distribution of activity with slight fluctuations with the evening activity being one of the highest (1626.28). Sunday: Similar to other days with high activity during lunch (1487.80) and significant consumption during late hours (772.22).

**Overall conclusion:**

The most active or resource-intensive periods occur during lunchtime while the lowest consumption is observed during late hours. Monday and Sunday stand out with activity levels compared to other days of the week.

1.2 The most peak time on weekends
   
a) The most profitable day

I modificated 1.1 (a) request and add `WHERE day_of_week IN ('Saturday', 'Sunday')` i.e added days of weekends
   
```sql
SELECT 
    day_of_week AS day
    , SUM(money) AS profit
FROM 
   coffee
WHERE day_of_week IN ('Saturday', 'Sunday')
GROUP BY 1
ORDER BY 2 DESC
```

Result:
   
| | day | profit |
|---|---|---|
| 1 | Sunday | 4849.86|
| 2 | Saturday | 4643.16 |

We can see that the café earns approximately the same amount on Saturday and Sunday.
   
b) The most profitable hours

I have also modificated 1.1 (b) request and add `WHERE day_of_week IN ('Saturday', 'Sunday')` i.e added days of weekends

Result:
   
| day | morning_time | lunch_time | dinner_time | evening_time | late_hours |
|----------|--------------|------------|-------------|--------------|------------|
| Saturday | 324.38 | 1456.32 | 1028.20 | 1421.68 | 412.58 |
| Sunday | 374.58 | 1487.80 | 840.08 | 1375.18 | 772.22 |

### 2. Determining the popularity of orders for different types of coffee

2.1 The most popular ordered coffee 

```sql
SELECT
    coffee_name AS type_of_coffee
    , COUNT(coffee_name) AS popularity
FROM coffee
GROUP BY 1
ORDER BY 2 DESC
```

Result:

| | type_of_coffee | popularity |
|---|---|---|
| 1 | Americano with Milk | 227 |
| 2 | Latte | 203 |
| 3 | Cappuccino | 177 |
| 4 | Americano | 145 |
| 5 | Cortado | 86 |
| 6 | Hot Chocolate | 71 |
| 7 | Espresso | 39 |
| 8 | Cocoa | 28 | 

**Visualization:**

<img src="Images/2.1%20(vizualization).png" width="600" height="450">

2.2 Determining the type of coffee purchased during peak time

a) The type of coffee with the highest revenue during peak time

```sql
SELECT 
    coffee_name AS type_of_coffee
    , SUM(CASE 
	    WHEN EXTRACT(HOUR FROM datetime) BETWEEN 7 AND 9 
	        OR (EXTRACT(HOUR FROM datetime) = 9 AND EXTRACT(MINUTE FROM datetime) = 59) THEN money
            ELSE 0 
          END) AS morning_time
    , SUM(CASE 
            WHEN EXTRACT(HOUR FROM datetime) BETWEEN 10 AND 12 
            	OR (EXTRACT(HOUR FROM datetime) = 12 AND EXTRACT(MINUTE FROM datetime) = 59) THEN money
            ELSE 0 
          END) AS lunch_time
    , SUM(CASE 
            WHEN EXTRACT(HOUR FROM datetime) BETWEEN 13 AND 15 
            	OR (EXTRACT(HOUR FROM datetime) = 15 AND EXTRACT(MINUTE FROM datetime) = 59) THEN money
            ELSE 0 
          END) AS dinner_time
    , SUM(CASE 
            WHEN EXTRACT(HOUR FROM datetime) BETWEEN 16 AND 19 
            	OR (EXTRACT(HOUR FROM datetime) = 19 AND EXTRACT(MINUTE FROM datetime) = 59) THEN money 
            ELSE 0 
          END) AS evening_time
    , SUM(CASE 
            WHEN EXTRACT(HOUR FROM datetime) BETWEEN 20 AND 23 
            	OR (EXTRACT(HOUR FROM datetime) = 23 AND EXTRACT(MINUTE FROM datetime) = 00) THEN money 
            ELSE 0 
          END) AS late_hours
FROM coffee
GROUP BY 1
ORDER BY 2 DESC
```

Result:

| type_of_coffee | morning_time | lunch_time | dinner_time | evening_time | late_hours |
|---|---|---|---|---|---|
| Latte | 600.88 | 2794.80 |1071.18 | 2380.54 | 848.94 |
| Americano with Milk | 485.86 | 2165.26 | 1554.72 | 1972.76 | 1278.62 |
| Cappuccino | 433.04 | 1406.70 | 1151.18 | 2584.48 | 1134.16 |
| Americano | 339.28 | 1376.36 | 1334.32 | 879.48 | 162.62 |
| Cortado | 246.38 | 747.96 | 571.26 | 712.70 | 167.52 |
| Cocoa | 39 | 266.00 | 146.96 | 424.36 | 189.88 |
| Espresso | 24	| 238.10 | 236.08 | 352.18 | 69.06 |
| Hot Chocolate | 0 | 602.86 | 459.16 | 1145.96 | 472.04 |

**Visualization:**

![The Most Profitable Hours Visualization](Images/2.2%20A%20(vizualization).png)

b) The type of coffee that sells the most (in quantity) during peak time

```sql
SELECT 
    coffee_name AS type_of_coffee
    , SUM(CASE 
	    WHEN EXTRACT(HOUR FROM datetime) BETWEEN 7 AND 9 
	        OR (EXTRACT(HOUR FROM datetime) = 9 AND EXTRACT(MINUTE FROM datetime) = 59) THEN 1
            ELSE 0 
          END) AS morning_time
    , SUM(CASE 
            WHEN EXTRACT(HOUR FROM datetime) BETWEEN 10 AND 12 
            	OR (EXTRACT(HOUR FROM datetime) = 12 AND EXTRACT(MINUTE FROM datetime) = 59) THEN 1
            ELSE 0 
          END) AS lunch_time
    , SUM(CASE 
            WHEN EXTRACT(HOUR FROM datetime) BETWEEN 13 AND 15 
            	OR (EXTRACT(HOUR FROM datetime) = 15 AND EXTRACT(MINUTE FROM datetime) = 59) THEN 1
            ELSE 0 
          END) AS dinner_time
    , SUM(CASE 
            WHEN EXTRACT(HOUR FROM datetime) BETWEEN 16 AND 19 
            	OR (EXTRACT(HOUR FROM datetime) = 19 AND EXTRACT(MINUTE FROM datetime) = 59) THEN 1 
            ELSE 0 
          END) AS evening_time
    , SUM(CASE 
            WHEN EXTRACT(HOUR FROM datetime) BETWEEN 20 AND 23 
            	OR (EXTRACT(HOUR FROM datetime) = 23 AND EXTRACT(MINUTE FROM datetime) = 00) THEN 1 
            ELSE 0 
          END) AS late_hours
FROM coffee
GROUP BY 1
ORDER BY 2 DESC
```

Result:

| type_of_coffee | morning_time | lunch_time | dinner_time | evening_time | late_hours |
|---|---|---|---|---|---|
| Latte | 16 | 73 | 28 | 63 | 23 |
| Americano with Milk | 15 | 66 | 47 | 60 | 39 |
| Americano | 12 | 49 | 47 | 31 | 6 |
| Cappuccino | 12 | 37 | 30 | 68 | 30 |
| Cortado | 9 | 26 | 20 | 25 | 6 |
| Cocoa | 1 | 7 | 4 | 11 | 5 |
| Espresso | 1	| 10 | 10 | 15 | 3 |
| Hot Chocolate | 0 | 16 | 12 | 30 | 13 |

**Visualization:**

![The Most Profitable Hours Visualization](Images/2.2%20B%20(vizualization).png)

2.3 Percentage distribution of different types of coffee

```sql
SELECT
    coffee_name AS type_of_coffee
    , CONCAT(ROUND(COUNT(coffee_name) * 100.0 / SUM(COUNT(coffee_name)) OVER(), 2), '%') AS percentage
FROM coffee
GROUP BY 1
```

Result:

| type_of_coffee | percentage |
|---|---|
| Americano with Milk| 23.26% |
| Cappuccino | 18.14% |
| Americano | 14.86% |
| Cortado | 8.81% |
| Latte | 20.80% |
| Hot Chocolate | 7.27% |
| Cocoa | 2.87% |
| Espresso | 4.00% |

**Visualization:**

<img src="Images/2.3%20(vizualization).png" width="600" height="450">


   


   
   




