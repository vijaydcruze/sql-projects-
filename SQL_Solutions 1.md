# SQL Solutions — Complete Set

**Scenarios covered:** Banking (Q1–40) • Railway Reservation (Q41–50) • Employee Management (Q1–15) • Customers & Orders (Q16–30) • Students (Q31–45)

**Dialect:** MySQL (queries also work on MariaDB; most work unchanged on PostgreSQL/SQLite except `AUTO_INCREMENT` / `LIMIT` syntax is already standard here).

> **How to use:** Run the schema + sample data for a scenario first, then run each query independently. Every query below is a standalone answer to that question.

---

## Schema Recap

### Banking

```sql
CREATE TABLE customers (
    customer_id INT PRIMARY KEY AUTO_INCREMENT,
    name VARCHAR(50),
    city VARCHAR(50)
);

CREATE TABLE accounts (
    account_id INT PRIMARY KEY AUTO_INCREMENT,
    customer_id INT,
    account_type VARCHAR(20),
    balance DECIMAL(10,2),
    FOREIGN KEY (customer_id) REFERENCES customers(customer_id)
);

INSERT INTO customers (name, city) VALUES
('Janani','Chennai'), ('Arun','Coimbatore'),
('Priya','Madurai'),  ('Karthik','Salem');

INSERT INTO accounts (customer_id, account_type, balance) VALUES
(1,'Savings',50000), (1,'Current',20000), (2,'Savings',30000),
(3,'Savings',15000), (4,'Current',40000);
```

### Railway Reservation

```sql
CREATE TABLE trains (
    train_id INT PRIMARY KEY AUTO_INCREMENT,
    train_name VARCHAR(50),
    source VARCHAR(50),
    destination VARCHAR(50)
);

CREATE TABLE bookings (
    booking_id INT PRIMARY KEY AUTO_INCREMENT,
    train_id INT,
    passenger_name VARCHAR(50),
    fare DECIMAL(10,2),
    status VARCHAR(20),
    FOREIGN KEY (train_id) REFERENCES trains(train_id)
);

INSERT INTO trains (train_name, source, destination) VALUES
('Express1','Chennai','Madurai'),
('Express2','Coimbatore','Salem'),
('Express3','Madurai','Chennai');

INSERT INTO bookings (train_id, passenger_name, fare, status) VALUES
(1,'Janani',500,'Confirmed'), (1,'Arun',500,'Waiting'),
(2,'Priya',300,'Confirmed'),  (3,'Karthik',450,'Cancelled'),
(2,'Meena',300,'Confirmed');
```

### Employee / Customers-Orders / Students

```sql
CREATE TABLE Employee (
    emp_id INT PRIMARY KEY, emp_name VARCHAR(50),
    department VARCHAR(30), salary DECIMAL(10,2),
    city VARCHAR(30), joining_date DATE
);

INSERT INTO Employee VALUES
(101,'John','IT',60000,'Chennai','2022-01-15'),
(102,'David','HR',45000,'Bangalore','2021-03-10'),
(103,'Smith','IT',70000,'Chennai','2020-07-12'),
(104,'Mary','Finance',55000,'Mumbai','2023-01-20'),
(105,'James','HR',48000,'Delhi','2022-05-05'),
(106,'Linda','Finance',65000,'Mumbai','2021-08-18');

CREATE TABLE Customers (
    customer_id INT PRIMARY KEY,
    customer_name VARCHAR(50), city VARCHAR(30)
);

CREATE TABLE Orders (
    order_id INT PRIMARY KEY, customer_id INT,
    amount DECIMAL(10,2), order_date DATE,
    FOREIGN KEY (customer_id) REFERENCES Customers(customer_id)
);

CREATE TABLE Students (
    student_id INT PRIMARY KEY, student_name VARCHAR(50),
    department VARCHAR(30), marks INT
);
```

> Note: No sample rows were given for `Customers`/`Orders`/`Students`, so those queries are written in correct general form — they return rows once you insert data.

---

# PART A — BANKING SCENARIO

## Basic + WHERE + Operators (1–15)

**1. Retrieve all accounts with balance greater than 20,000.**
```sql
SELECT * FROM accounts
WHERE balance > 20000;
```

**2. Find customers who live in Chennai.**
```sql
SELECT * FROM customers
WHERE city = 'Chennai';
```

**3. Display accounts with balance between 20,000 and 50,000.**
```sql
SELECT * FROM accounts
WHERE balance BETWEEN 20000 AND 50000;
```
*`BETWEEN` is inclusive of both ends.*

**4. Find customers whose names start with 'J'.**
```sql
SELECT * FROM customers
WHERE name LIKE 'J%';
```

**5. Retrieve accounts of type 'Savings' or 'Current'.**
```sql
SELECT * FROM accounts
WHERE account_type = 'Savings' OR account_type = 'Current';
-- Same as: WHERE account_type IN ('Savings', 'Current')
```

**6. Display accounts that are not 'Savings'.**
```sql
SELECT * FROM accounts
WHERE account_type <> 'Savings';
-- Also valid: account_type != 'Savings'  /  NOT account_type = 'Savings'
```

**7. Find customers whose names contain the letter 'a'.**
```sql
SELECT * FROM customers
WHERE name LIKE '%a%';
```
*MySQL's default collation is case-insensitive, so this also matches 'A'.*

**8. Retrieve accounts with balance less than or equal to 30,000.**
```sql
SELECT * FROM accounts
WHERE balance <= 30000;
```

**9. Find customers who are not from Madurai.**
```sql
SELECT * FROM customers
WHERE city <> 'Madurai';
```

**10. Display accounts where balance is not between 10,000 and 40,000.**
```sql
SELECT * FROM accounts
WHERE balance NOT BETWEEN 10000 AND 40000;
```

**11. Retrieve customers whose names end with 'i'.**
```sql
SELECT * FROM customers
WHERE name LIKE '%i';
```

**12. Find accounts with balance equal to 50,000.**
```sql
SELECT * FROM accounts
WHERE balance = 50000;
```

**13. Display customers whose city is either Chennai or Salem.**
```sql
SELECT * FROM customers
WHERE city IN ('Chennai', 'Salem');
```

**14. Find accounts with balance greater than 10,000 and less than 40,000.**
```sql
SELECT * FROM accounts
WHERE balance > 10000 AND balance < 40000;
```

**15. Retrieve accounts where account type is not in ('Current').**
```sql
SELECT * FROM accounts
WHERE account_type NOT IN ('Current');
```

## ORDER BY (16–18)

**16. Display all accounts sorted by balance in descending order.**
```sql
SELECT * FROM accounts
ORDER BY balance DESC;
```

**17. List customers sorted alphabetically by name.**
```sql
SELECT * FROM customers
ORDER BY name ASC;
```

**18. Display accounts sorted by account type and then by balance (descending).**
```sql
SELECT * FROM accounts
ORDER BY account_type ASC, balance DESC;
```

## Aggregate Functions (19–23)

**19. Find the total balance of all accounts.**
```sql
SELECT SUM(balance) AS total_balance FROM accounts;
```
*Result on sample data: 155000.00*

**20. Calculate the average balance of accounts.**
```sql
SELECT AVG(balance) AS average_balance FROM accounts;
```
*Result: 31000.00*

**21. Find the maximum account balance.**
```sql
SELECT MAX(balance) AS maximum_balance FROM accounts;
```
*Result: 50000.00*

**22. Find the minimum account balance.**
```sql
SELECT MIN(balance) AS minimum_balance FROM accounts;
```
*Result: 15000.00*

**23. Count the total number of customers.**
```sql
SELECT COUNT(*) AS total_customers FROM customers;
```
*Result: 4*

## GROUP BY + HAVING (24–28)

**24. Find total balance grouped by account type.**
```sql
SELECT account_type, SUM(balance) AS total_balance
FROM accounts
GROUP BY account_type;
```
*Savings = 95000, Current = 60000*

**25. Find average balance for each account type.**
```sql
SELECT account_type, AVG(balance) AS average_balance
FROM accounts
GROUP BY account_type;
```
*Savings ≈ 31666.67, Current = 30000.00*

**26. Display account types having average balance greater than 20,000.**
```sql
SELECT account_type, AVG(balance) AS average_balance
FROM accounts
GROUP BY account_type
HAVING AVG(balance) > 20000;
```
*Both Savings and Current qualify. `HAVING` filters groups — `WHERE` cannot be used with aggregates.*

**27. Count number of accounts for each customer.**
```sql
SELECT customer_id, COUNT(*) AS account_count
FROM accounts
GROUP BY customer_id;
```

**28. Display customers having more than one account.**
```sql
SELECT customer_id, COUNT(*) AS account_count
FROM accounts
GROUP BY customer_id
HAVING COUNT(*) > 1;
```
*Result: customer_id 1 (Janani) — 2 accounts.*

## JOINS (29–35)

**29. Retrieve customer names along with their account balances.**
```sql
SELECT c.name, a.balance
FROM customers c
INNER JOIN accounts a ON c.customer_id = a.customer_id;
```

**30. Display all customers and their accounts (including customers without accounts).**
```sql
SELECT c.customer_id, c.name, c.city,
       a.account_id, a.account_type, a.balance
FROM customers c
LEFT JOIN accounts a ON c.customer_id = a.customer_id;
```
*`LEFT JOIN` keeps every customer; account columns are `NULL` if they have no account.*

**31. Display all accounts and corresponding customer details.**
```sql
SELECT a.account_id, a.account_type, a.balance,
       c.customer_id, c.name, c.city
FROM accounts a
LEFT JOIN customers c ON a.customer_id = c.customer_id;
-- Equivalent: customers RIGHT JOIN accounts
```

**32. Retrieve customer names and account types where balance is greater than 20,000.**
```sql
SELECT c.name, a.account_type
FROM customers c
INNER JOIN accounts a ON c.customer_id = a.customer_id
WHERE a.balance > 20000;
```

**33. List customers with their total balance using JOIN.**
```sql
SELECT c.customer_id, c.name, SUM(a.balance) AS total_balance
FROM customers c
INNER JOIN accounts a ON c.customer_id = a.customer_id
GROUP BY c.customer_id, c.name;
```
*Janani = 70000, Arun = 30000, Priya = 15000, Karthik = 40000*

**34. Display customer names and balances sorted by balance.**
```sql
SELECT c.name, a.balance
FROM customers c
INNER JOIN accounts a ON c.customer_id = a.customer_id
ORDER BY a.balance ASC;
```
*Add `DESC` if highest-first is required.*

**35. Count number of accounts for each city using JOIN.**
```sql
SELECT c.city, COUNT(a.account_id) AS account_count
FROM customers c
INNER JOIN accounts a ON c.customer_id = a.customer_id
GROUP BY c.city;
```
*Chennai = 2, Coimbatore = 1, Madurai = 1, Salem = 1. Use `LEFT JOIN` + `COUNT(a.account_id)` to also show cities with 0 accounts.*

## SUBQUERIES (36–40)

**36. Find accounts with balance greater than average balance.**
```sql
SELECT * FROM accounts
WHERE balance > (SELECT AVG(balance) FROM accounts);
```
*Average = 31000, so accounts of 50000 and 40000 qualify.*

**37. Retrieve customers who have accounts.**
```sql
SELECT * FROM customers
WHERE customer_id IN (SELECT customer_id FROM accounts);
-- Equivalent with EXISTS:
-- SELECT * FROM customers c
-- WHERE EXISTS (SELECT 1 FROM accounts a WHERE a.customer_id = c.customer_id);
```
*All 4 customers on sample data.*

**38. Find customers who do not have any accounts.**
```sql
SELECT * FROM customers
WHERE customer_id NOT IN (SELECT customer_id FROM accounts);
-- Safer if NULLs are possible: use NOT EXISTS (same pattern as Q37)
```
*Empty result on sample data — every customer has an account.*

**39. Display account(s) with the maximum balance.**
```sql
SELECT * FROM accounts
WHERE balance = (SELECT MAX(balance) FROM accounts);
```
*Account 1 — Janani, Savings, 50000. Using `=` (not `>`) correctly returns ties too.*

**40. Find customers whose total balance is greater than 40,000.**
```sql
SELECT * FROM customers
WHERE customer_id IN (
    SELECT customer_id
    FROM accounts
    GROUP BY customer_id
    HAVING SUM(balance) > 40000
);
```
*Only Janani (70000). Karthik has exactly 40000, so he is not included. Correlated alternative:*
```sql
-- SELECT * FROM customers c
-- WHERE (SELECT SUM(balance) FROM accounts a
--        WHERE a.customer_id = c.customer_id) > 40000;
```

---

# PART B — RAILWAY RESERVATION SCENARIO

## Basic + WHERE + Operators (41–45)

**41. Retrieve all bookings with fare greater than 400.**
```sql
SELECT * FROM bookings
WHERE fare > 400;
```

**42. Find bookings where status is not 'Confirmed'.**
```sql
SELECT * FROM bookings
WHERE status <> 'Confirmed';
```
*Arun (Waiting), Karthik (Cancelled)*

**43. Display trains starting from Chennai.**
```sql
SELECT * FROM trains
WHERE source = 'Chennai';
```
*Express1*

**44. Retrieve bookings with fare between 300 and 500.**
```sql
SELECT * FROM bookings
WHERE fare BETWEEN 300 AND 500;
```
*All 5 bookings — fares are 300–500.*

**45. Find passengers whose names start with 'A'.**
```sql
SELECT * FROM bookings
WHERE passenger_name LIKE 'A%';
```
*Arun*

## JOINS + GROUP BY + ORDER BY (46–48)

**46. Retrieve train names along with passenger names.**
```sql
SELECT t.train_name, b.passenger_name
FROM trains t
INNER JOIN bookings b ON t.train_id = b.train_id;
```

**47. Count number of bookings for each train.**
```sql
SELECT t.train_name, COUNT(b.booking_id) AS booking_count
FROM trains t
LEFT JOIN bookings b ON t.train_id = b.train_id
GROUP BY t.train_id, t.train_name;
```
*Express1 = 2, Express2 = 2, Express3 = 1. (`LEFT JOIN` also shows trains with 0 bookings; simple `GROUP BY train_id` on `bookings` is the textbook short form.)*

**48. Display train names and total fare collected for each train.**
```sql
SELECT t.train_name, SUM(b.fare) AS total_fare
FROM trains t
INNER JOIN bookings b ON t.train_id = b.train_id
GROUP BY t.train_id, t.train_name;
```
*Express1 = 1000, Express2 = 600, Express3 = 450. If only money actually collected should count, add `WHERE b.status = 'Confirmed'` before `GROUP BY` (then Express1 = 500, Express3 drops out / shows NULL with LEFT JOIN).*

## SUBQUERIES (49–50)

**49. Find bookings with fare equal to the highest fare.**
```sql
SELECT * FROM bookings
WHERE fare = (SELECT MAX(fare) FROM bookings);
```
*Janani and Arun — both 500.*

**50. Retrieve trains that have more than one booking.**
```sql
SELECT * FROM trains
WHERE train_id IN (
    SELECT train_id
    FROM bookings
    GROUP BY train_id
    HAVING COUNT(*) > 1
);
```
*Express1 and Express2.*

---

# PART C — EMPLOYEE TABLE (1–15)

Table: `Employee(emp_id, emp_name, department, salary, city, joining_date)`

**1. Find the total number of employees in each department.**
```sql
SELECT department, COUNT(*) AS employee_count
FROM Employee
GROUP BY department;
```
*IT = 2, HR = 2, Finance = 2*

**2. Find the average salary of employees in each department.**
```sql
SELECT department, AVG(salary) AS average_salary
FROM Employee
GROUP BY department;
```
*IT = 65000, HR = 46500, Finance = 60000*

**3. Display departments having more than one employee.**
```sql
SELECT department, COUNT(*) AS employee_count
FROM Employee
GROUP BY department
HAVING COUNT(*) > 1;
```
*All three departments on sample data.*

**4. Find the highest salary in each department.**
```sql
SELECT department, MAX(salary) AS highest_salary
FROM Employee
GROUP BY department;
```
*IT = 70000, HR = 48000, Finance = 65000*

**5. Find the lowest salary in each department.**
```sql
SELECT department, MIN(salary) AS lowest_salary
FROM Employee
GROUP BY department;
```
*IT = 60000, HR = 45000, Finance = 55000*

**6. Find departments whose average salary is greater than 50,000.**
```sql
SELECT department, AVG(salary) AS average_salary
FROM Employee
GROUP BY department
HAVING AVG(salary) > 50000;
```
*IT and Finance*

**7. Calculate the total salary expenditure for each department.**
```sql
SELECT department, SUM(salary) AS total_salary
FROM Employee
GROUP BY department;
```
*IT = 130000, HR = 93000, Finance = 120000*

**8. Display all employees sorted by salary in descending order.**
```sql
SELECT * FROM Employee
ORDER BY salary DESC;
```

**9. Display employees sorted first by department and then by salary in descending order.**
```sql
SELECT * FROM Employee
ORDER BY department ASC, salary DESC;
```

**10. Find cities that have more than one employee.**
```sql
SELECT city, COUNT(*) AS employee_count
FROM Employee
GROUP BY city
HAVING COUNT(*) > 1;
```
*Chennai (2), Mumbai (2)*

**11. Find the total salary paid in each city.**
```sql
SELECT city, SUM(salary) AS total_salary
FROM Employee
GROUP BY city;
```
*Chennai = 130000, Bangalore = 45000, Mumbai = 120000, Delhi = 48000*

**12. Display departments ordered by total salary expenditure from highest to lowest.**
```sql
SELECT department, SUM(salary) AS total_salary
FROM Employee
GROUP BY department
ORDER BY total_salary DESC;
```
*IT → Finance → HR*

**13. Find the number of employees in each department whose salary is greater than 50,000.**
```sql
SELECT department, COUNT(*) AS employee_count
FROM Employee
WHERE salary > 50000
GROUP BY department;
```
*IT = 2, Finance = 2. HR has none, so it does not appear (filter happens **before** grouping — that's why `WHERE`, not `HAVING`, is used here).*
*To show HR = 0 as well: `SELECT department, SUM(salary > 50000) ... GROUP BY department` (MySQL) or `COUNT(CASE WHEN salary > 50000 THEN 1 END)` (portable).*

**14. Find the difference between the highest and lowest salary in each department.**
```sql
SELECT department, MAX(salary) - MIN(salary) AS salary_difference
FROM Employee
GROUP BY department;
```
*IT = 10000, HR = 3000, Finance = 10000*

**15. Display the top 3 highest-paid employees.**
```sql
SELECT * FROM Employee
ORDER BY salary DESC
LIMIT 3;
```
*Smith (70000), Linda (65000), John (60000)*

---

# PART D — CUSTOMERS & ORDERS TABLES (16–30)

Tables: `Customers(customer_id, customer_name, city)`, `Orders(order_id, customer_id, amount, order_date)`
*(No sample data given — queries are in general form. Versions with `JOIN` show names; the short `GROUP BY customer_id` form works on `Orders` alone.)*

**16. Find the total order amount for each customer.**
```sql
SELECT c.customer_id, c.customer_name, SUM(o.amount) AS total_amount
FROM Customers c
INNER JOIN Orders o ON c.customer_id = o.customer_id
GROUP BY c.customer_id, c.customer_name;
```

**17. Find customers who have placed more than 3 orders.**
```sql
SELECT customer_id, COUNT(*) AS order_count
FROM Orders
GROUP BY customer_id
HAVING COUNT(*) > 3;
```

**18. Find the average order amount for each customer.**
```sql
SELECT customer_id, AVG(amount) AS average_amount
FROM Orders
GROUP BY customer_id;
```

**19. Find the highest order amount placed by each customer.**
```sql
SELECT customer_id, MAX(amount) AS highest_amount
FROM Orders
GROUP BY customer_id;
```

**20. Display customers sorted by their total purchase amount.**
```sql
SELECT c.customer_id, c.customer_name, SUM(o.amount) AS total_purchase
FROM Customers c
INNER JOIN Orders o ON c.customer_id = o.customer_id
GROUP BY c.customer_id, c.customer_name
ORDER BY total_purchase DESC;
```
*Use `ASC` for lowest-first.*

**21. Find customers whose total purchase amount exceeds 10,000.**
```sql
SELECT customer_id, SUM(amount) AS total_purchase
FROM Orders
GROUP BY customer_id
HAVING SUM(amount) > 10000;
```

**22. Display customer names along with the total number of orders placed.**
```sql
SELECT c.customer_name, COUNT(o.order_id) AS order_count
FROM Customers c
LEFT JOIN Orders o ON c.customer_id = o.customer_id
GROUP BY c.customer_id, c.customer_name;
```
*`LEFT JOIN` + `COUNT(o.order_id)` shows 0 for customers who never ordered. Use `INNER JOIN` if only ordering customers are wanted.*

**23. Find the customer who spent the highest amount.**
```sql
SELECT c.customer_id, c.customer_name, SUM(o.amount) AS total_spent
FROM Customers c
INNER JOIN Orders o ON c.customer_id = o.customer_id
GROUP BY c.customer_id, c.customer_name
ORDER BY total_spent DESC
LIMIT 1;
```

**24. Find the customer who placed the maximum number of orders.**
```sql
SELECT c.customer_id, c.customer_name, COUNT(o.order_id) AS order_count
FROM Customers c
INNER JOIN Orders o ON c.customer_id = o.customer_id
GROUP BY c.customer_id, c.customer_name
ORDER BY order_count DESC
LIMIT 1;
```

**25. Find customers whose average order amount is greater than 2,000.**
```sql
SELECT customer_id, AVG(amount) AS average_amount
FROM Orders
GROUP BY customer_id
HAVING AVG(amount) > 2000;
```

**26. Display the top 5 customers based on total purchase amount.**
```sql
SELECT c.customer_id, c.customer_name, SUM(o.amount) AS total_purchase
FROM Customers c
INNER JOIN Orders o ON c.customer_id = o.customer_id
GROUP BY c.customer_id, c.customer_name
ORDER BY total_purchase DESC
LIMIT 5;
```

**27. Find the minimum order amount for each customer.**
```sql
SELECT customer_id, MIN(amount) AS minimum_amount
FROM Orders
GROUP BY customer_id;
```

**28. Find customers who have placed orders worth more than 5,000 in total.**
```sql
SELECT customer_id, SUM(amount) AS total_purchase
FROM Orders
GROUP BY customer_id
HAVING SUM(amount) > 5000;
```

**29. Display customer-wise total orders and total purchase amount.**
```sql
SELECT c.customer_id, c.customer_name,
       COUNT(o.order_id) AS total_orders,
       SUM(o.amount) AS total_purchase
FROM Customers c
INNER JOIN Orders o ON c.customer_id = o.customer_id
GROUP BY c.customer_id, c.customer_name;
```

**30. Find customers who placed more than 2 orders and spent more than 8,000.**
```sql
SELECT customer_id,
       COUNT(*) AS order_count,
       SUM(amount) AS total_spent
FROM Orders
GROUP BY customer_id
HAVING COUNT(*) > 2 AND SUM(amount) > 8000;
```
*Two aggregate conditions → both go in `HAVING`, joined by `AND`.*

---

# PART E — STUDENTS TABLE (31–45)

Table: `Students(student_id, student_name, department, marks)` *(No sample data given — general form.)*

**31. Find the average marks scored by students in each department.**
```sql
SELECT department, AVG(marks) AS average_marks
FROM Students
GROUP BY department;
```

**32. Find departments whose average marks are above 75.**
```sql
SELECT department, AVG(marks) AS average_marks
FROM Students
GROUP BY department
HAVING AVG(marks) > 75;
```

**33. Find the highest mark scored in each department.**
```sql
SELECT department, MAX(marks) AS highest_mark
FROM Students
GROUP BY department;
```

**34. Find the total number of students in each department.**
```sql
SELECT department, COUNT(*) AS student_count
FROM Students
GROUP BY department;
```

**35. Find departments having more than 5 students.**
```sql
SELECT department, COUNT(*) AS student_count
FROM Students
GROUP BY department
HAVING COUNT(*) > 5;
```

**36. Display departments sorted by average marks in descending order.**
```sql
SELECT department, AVG(marks) AS average_marks
FROM Students
GROUP BY department
ORDER BY average_marks DESC;
```

**37. Find the top 3 departments based on average marks.**
```sql
SELECT department, AVG(marks) AS average_marks
FROM Students
GROUP BY department
ORDER BY average_marks DESC
LIMIT 3;
```

**38. Find departments whose average marks are between 70 and 90.**
```sql
SELECT department, AVG(marks) AS average_marks
FROM Students
GROUP BY department
HAVING AVG(marks) BETWEEN 70 AND 90;
```

**39. Find the total marks scored by students in each department.**
```sql
SELECT department, SUM(marks) AS total_marks
FROM Students
GROUP BY department;
```

**40. Display departments sorted by the total number of students.**
```sql
SELECT department, COUNT(*) AS student_count
FROM Students
GROUP BY department
ORDER BY student_count ASC;
```
*Add `DESC` for largest-department-first.*

**41. Find the lowest mark scored in each department.**
```sql
SELECT department, MIN(marks) AS lowest_mark
FROM Students
GROUP BY department;
```

**42. Find departments where the highest mark is greater than 90.**
```sql
SELECT department, MAX(marks) AS highest_mark
FROM Students
GROUP BY department
HAVING MAX(marks) > 90;
```

**43. Find the number of students scoring above 80 in each department.**
```sql
SELECT department, COUNT(*) AS students_above_80
FROM Students
WHERE marks > 80
GROUP BY department;
```
*Filter individual rows first (`WHERE`), then count per group. Departments with zero such students won't appear.*

**44. Find departments where more than 3 students scored above 75.**
```sql
SELECT department, COUNT(*) AS students_above_75
FROM Students
WHERE marks > 75
GROUP BY department
HAVING COUNT(*) > 3;
```
*Classic `WHERE` + `GROUP BY` + `HAVING` combination: `WHERE marks > 75` selects qualifying students, `HAVING COUNT(*) > 3` keeps only departments with enough of them.*

**45. Display departments ordered by highest mark in descending order.**
```sql
SELECT department, MAX(marks) AS highest_mark
FROM Students
GROUP BY department
ORDER BY highest_mark DESC;
```

---

# Key Concepts — Quick Revision

| Concept | Rule |
|---|---|
| `WHERE` vs `HAVING` | `WHERE` filters **rows before** grouping (no aggregates allowed). `HAVING` filters **groups after** `GROUP BY` (use it for `SUM/AVG/COUNT/MAX/MIN` conditions). |
| `LIKE` patterns | `%` = any sequence (incl. empty), `_` = exactly one character. `'J%'` starts-with, `'%i'` ends-with, `'%a%'` contains. |
| `BETWEEN` | Inclusive of both endpoints. `NOT BETWEEN` is its exact negation. |
| `IN / NOT IN` | Cleaner than chains of `OR` / `<>`. Beware: `NOT IN` with a subquery returning a `NULL` gives no rows — prefer `NOT EXISTS` then. |
| `INNER JOIN` | Only matching rows from both tables. |
| `LEFT JOIN` | All rows from the left table; `NULL`s where no match. Pair with `COUNT(right.column)` (not `COUNT(*)`) to get true 0 counts. |
| Aggregate + other columns | Non-aggregated selected columns must appear in `GROUP BY` (e.g. `GROUP BY c.customer_id, c.customer_name`). |
| Scalar subquery | `(SELECT AVG(...))`, `(SELECT MAX(...))` return one value — compare with `=`, `>`, etc. Using `=` for "maximum" (Q39/Q49) correctly returns **all tied rows**. |
| Order of execution | `FROM/JOIN → WHERE → GROUP BY → HAVING → SELECT → ORDER BY → LIMIT`. This is why aliases defined in `SELECT` can be used in `ORDER BY`/`HAVING` (MySQL) but not in `WHERE`. |
| Top-N pattern | `ORDER BY <measure> DESC LIMIT n` (Q15 Employee, Q23/Q24/Q26 Orders, Q37 Students). |

### Expected results cheat-check (sample data)

- **Banking:** total balance `155000`, average `31000`, max `50000` (Janani-Savings), min `15000`. Totals by type: Savings `95000`, Current `60000`. Only Janani has >1 account and total > 40000.
- **Railway:** bookings per train — Express1 `2`, Express2 `2`, Express3 `1`; fare per train — `1000 / 600 / 450`; highest fare `500` → Janani & Arun.
- **Employee:** dept totals — IT `130000`, Finance `120000`, HR `93000`; dept averages — IT `65000`, Finance `60000`, HR `46500`; top-3 earners — Smith, Linda, John.
