# SQL Case Study: Banking System

This repository contains a case study for a **Banking** **System** using SQL. The study includes table creation scripts, a variety of SQL queries, and solutions to common **banking-related use cases**.  

---
## Tools Used  
- **Database Management System:** PostgreSQL  
- **Development Environment:** pgAdmin

---
## Table Creation Scripts  

The following tables are created as part of this case study:  
- **`Products`**: Stores product details like Savings Account, Current Account, etc.  
- **`Employees`**: Stores employee details.  
- **Customers**: Stores customer details.  
- **`Accounts`**: Stores account details including balance, status, and opening date.  
- **`Transactions`**: Tracks all account transactions.  
- **`Customer_Accounts`**: Links customers, accounts, and products.  

You can find the schema definition in the [`insert script`](SQL-Basics-Exploring-Bannking-Dataset/insert_script.txt) file.

---

## Questions and Solutions

### 1) Fetch the transaction id, date and amount of all debit transactions.

```sql
SELECT transaction_id
, transaction_date
, transaction_amount
FROM transactions
WHERE credit_debit_flag = 'D';
```

### 2) Fetch male employees who earn more than 5000 salary.

```sql
SELECT * FROM employees
WHERE gender = 'M' AND salary > 5000;
```

### 3) Fetch employees whose name starts with J or whose salary is greater than or equal to 7000.

```sql
SELECT * FROM employees
WHERE emp_name LIKE 'J%' OR salary >= 7000;
```

### 4) Fetch accounts with balance in between 1000 to 3000

```sql
SELECT * FROM accounts
WHERE balance BETWEEN 1000 AND 3000;
```

### 5) Using SQL, find out if a given number is even or odd ? (Given numbers are 432, 77)
```sql

SELECT
	CASE 
		WHEN 77%2 = 0 THEN 'EVEN' 
		ELSE 'ODD' 
	END AS odd_even_flag;
```

### 6) Find customers who did not provide a phone no.

``` sql
SELECT * FROM customers 
WHERE phone IS NULL;
```

### 7) Find all the different products purchased by the customers.

```sql
SELECT DISTINCT prod_id
FROM Customer_Accounts;
```

### 8) Sort all the active accounts based on highest balance and based on the earliest opening dasqlte.

```sql
SELECT * FROM accounts
WHERE account_status = 'Active'
ORDER BY balance DESC, date_of_opening;
```

### 9) Fetch the oldest 5 transactions.-

```sql
SELECT * FROM transactions
ORDER BY transaction_date LIMIT 5;
```

### 10) Find customers who are either from Bangalore/Chennai and their phone number is available OR those who were born before 1990.

```sql
SELECT * FROM customers
WHERE (address IN ('Bangalore', 'Chennai') AND phone IS NOT NULL)
OR TO_CHAR(dob,'yyyy') < '1990';
```

### 11) Find total no of transactions in Feb 2024.

```sql
SELECT COUNT(*)
FROM transactions
WHERE DATE_PART('year',transaction_date) = '2024' 
AND DATE_PART('month',transaction_date) = '2';

SELECT COUNT(1) FROM Transactions 
WHERE TO_CHAR(transaction_date,'Mon') = 'Feb';
```


### 12) How total no of products purchased by customer "Satya Sharma".

```sql
SELECT COUNT(*)
FROM customers c
JOIN customer_accounts ca
ON ca.customer_id = c.customer_id
WHERE c.first_name||' '||c.last_name = 'Satya Sharma';
```

### 13) Display the full names of all employees and customers. Display the results in TWO SEPARATE COLUMNS for Employees & Customers

```sql
SELECT emp_name, first_name||' '||last_name AS customer_name
FROM employees,customers;

-- If you need the results in ONE COLUMN
SELECT emp_name FROM employees
UNION ALL 
SELECT first_name||' '||last_name FROM customers;
```

### 14) Categorize accounts based on their balance.
> [Below 1k is Low balance, between 1k to 2k is average balance, above 2k is high balance]

```sql
SELECT *,
	CASE 
		WHEN balance < 1000 THEN 'Low balance'
		WHEN balance BETWEEN 1000 AND 2000 THEN 'Average balance'
		ELSE 'High Balance'
	END AS category
FROM accounts;
```

### 15) Find the total balance of all savings account.

```sql
SELECT SUM(a.balance) AS total_balance
FROM accounts a
JOIN customer_accounts ca
ON ca.account_no = a.account_no
JOIN products p
ON p.prod_id= ca.prod_id
WHERE p.prod_name = 'Savings Account';
```

### 16) Display the total account balance in all the current and savings account.

```sql
SELECT p.prod_name, SUM(a.balance) AS total_balance
FROM accounts a
JOIN customer_accounts ca
ON ca.account_no = a.account_no
JOIN products p
ON p.prod_id= ca.prod_id
WHERE p.prod_name IN ('Savings Account', 'Current Account')
GROUP BY p.prod_name
```
---
## Connect with Me
👋 Hey Data Nerds! Let’s connect, query, and grow together!
  - [LinkedIn](https://www.linkedin.com/in/lmahial/)
