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
   
   ```plaintext
   | | MIN_date | MAX_date | MIN_datetime | MAX_datetime |
   | 1 | 2024-03-01 | 2024-07-17 | 07:33:06 | 22:55:37 |
   ```
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
   
   ```plaintext
   | cash_type | count |
   | 1 | cash | 89 |
   | 2 | card | 887 |
   ```
   I took the same number as the rows.

5. Next I checked `money`. 

    ```sql
   SELECT 
	   MIN(money)
	  , MAX(money)
   FROM coffee;
   ```
   Result:
   
    ```plaintext
   |  | min | max |
   | 1 | 23.02 | 40 |
   ```
   I got good and adequate results.
    
   ```sql
   SELECT 
	   COUNT(money)
   FROM coffee
   WHERE money IS NULL
   ```
   Result:
   
    ```plaintext
   |  | count |
   | 1 | 0 |
   ```
   And here I got 0 values which equals NULL.

6. Finnaly, I checked `coffee_name`.
   ```sql
   SELECT 
	   COUNT(coffee_name)
   FROM coffee
   WHERE coffee_name IS NULL
   ```
    Result:
   
    ```plaintext
   |  | count |
   | 1 | 0 |
   ```
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
   
   b) The most profitable hours

   I have also modificated 1.1 (b) request and add `WHERE day_of_week IN ('Saturday', 'Sunday')` i.e added days of weekends

   Result:
   
   | day | morning_time | lunch_time | dinner_time | evening_time | late_hours |
   |----------|--------------|------------|-------------|--------------|------------|
   | Saturday | 324.38 | 1456.32 | 1028.20 | 1421.68 | 412.58 |
   | Sunday | 374.58 | 1487.80 | 840.08 | 1375.18 | 772.22 |

### 2. Determining the popularity of orders for different types of coffee

2.1 The most popular ordered coffee 

2.2 Determining the type of coffee purchased during peak time

2.3 Percentage distribution of different types of coffee
   


   
   




