# Coffee-Sales-DA

## Table of Contents
- [About the Project](#about-the-project)
- [About the Dataset](#about-the-dataset)
  - [Contents](#contents)
- [pgAdmin 4](#pgAdmin-4)
  - [Table Configuration in pgAdmin 4](#table-configuration-in-pgAdmin-4)
- [Data Cleaning and Preprocessing](#data-cleaning-and-preprocessing)


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

1. Data Cleaning and Preprocessing 

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
   
   




