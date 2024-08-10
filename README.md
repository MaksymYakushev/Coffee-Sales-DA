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
І used this dataset for data analysis and visualization. This dataset was provided by a Ukrainian coffee shop in Odesa. It consists of data on coffee purchases, the time of purchase, price, and type of payment.

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
1. We are removing the `card` column. It contains data about the transaction number, which is not needed for my analysis.
   ```plaintext
    SELECT
   ```





