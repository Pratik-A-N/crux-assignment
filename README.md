# Crux Assignment Documentation

## Solution Approach

### Library used: [python-sqlparse](https://sqlparse.readthedocs.io/en/latest/index.html)

![WhatsApp Image 2024-05-28 at 06 34 07_0cde06b8](https://github.com/Pratik-A-N/crux-assignment/assets/91190547/e71012aa-b713-4434-8d52-85fd0bfa8bd2)

1. Firstly I parsed the sql query to the sqlparse and tokenizing them
2. Then process the query to store the outermost 'WHERE' token and its related table Name, column name and its corresponding operator and value
3. While processing the table name also stored the alias names in the dictionary which helps in efficient displaying of output in case of JOINS
4. Once the Where token is extracted from the main query then process only WHERE Token, so in that i have handled the cases for IN, BETWEEN, IS NULL, IS NOT NULL, NOT and Comparison operators
5. And as this checks are in if condition if there are multiple queries each will be handled resulting in the output for each operator along with its corresponding values
6. Frequently used Functions from sqlparse library are
   -  [parse()](https://sqlparse.readthedocs.io/en/latest/api.html#sqlparse.parse) - Gives a list of strings 
   -  [get_parent_name()](https://sqlparse.readthedocs.io/en/latest/analyzing.html) - To get the parent object. For eg. in a.department if this the token the a is the parent which can be further changed to its real table column name
   -  [get_real_name()](https://sqlparse.readthedocs.io/en/latest/analyzing.html) - To get the real Names of the columns in Table
   -  [token_first()](https://sqlparse.readthedocs.io/en/latest/analyzing.html) - Some tokens are like tree so to get it first child this function was used
   -  [token_index()](https://sqlparse.readthedocs.io/en/latest/analyzing.html) - It gave the index of the token in the token list 
   -  [token_next()](https://sqlparse.readthedocs.io/en/latest/analyzing.html) - Gave the token next to the current index in the token list
   -  [token_prev()](https://sqlparse.readthedocs.io/en/latest/analyzing.html) - Gave the token previous to the current index int the token list

### Test Cases and Expected output

```python
1. 
Query: SELECT * FROM users WHERE user_id = 123;
Output: [{'table_name': 'users', 'column_name': 'user_id', 'operator': '=', 'value': 123}]

2.
Query: SELECT * FROM users WHERE user_id IS NULL
Output: [{'table_name': 'users', 'column_name': 'user_id', 'operator': 'IS ', 'value': 'NULL'}]

3.
Query: SELECT * FROM users WHERE user_id BETWEEN 30000 AND 45000
Output: [{'table_name': 'users', 'column_name': 'user_id', 'operator': 'BETWEEN', 'value': [30000, 45000]}]

4.
Query: SELECT u.name FROM users u WHERE u.age > 30;
Output: [{'table_name': 'users', 'column_name': 'age', 'operator': '>', 'value': 30}]

5.
Query: SELECT SupplierID, Name, Address FROM Suppliers WHERE Name LIKE '_ango%'
Output: [{'table_name': 'Suppliers', 'column_name': 'Name', 'operator': 'LIKE', 'value': '_ango%'}]

6.
Query: SELECT SupplierID, Name, Address FROM Suppliers WHERE Name NOT LIKE '_ango%'
Output: [{'table_name': 'Suppliers', 'column_name': 'Name', 'operator': 'NOT LIKE', 'value': '_ango%'}]

7.
Query: SELECT * FROM orders WHERE order_id IN (30000, 40000, 25000) AND status = 'Shipped';
Output: [{'table_name': 'orders', 'column_name': 'order_id', 'operator': 'IN', 'value': [30000, 40000, 25000]}, {'table_name': 'orders', 'column_name': 'status', 'operator': '=', 'value': 'Shipped'}]

8.
Query: SELECT * FROM orders WHERE order_id IN (SELECT order_id FROM order_details WHERE quantity > 10) AND status = 'Shipped';
Output: [{'table_name': 'orders', 'column_name': 'order_id', 'operator': 'IN', 'value': 'Sub Query'}, {'table_name': 'orders', 'column_name': 'status', 'operator': '=', 'value': 'Shipped'}]

9.
Query: SELECT a.name, b.salary FROM employees a JOIN payroll b ON a.emp_id = b.emp_id WHERE a.department IN (30000, 40000, 25000) AND b.salary > 50000;
Output: [{'table_name': 'employees', 'column_name': 'department', 'operator': 'IN', 'value': [30000, 40000, 25000]}, {'table_name': 'payroll', 'column_name': 'salary', 'operator': '>', 'value': 50000}]

10.
Query: SELECT a.name, b.salary FROM employees a JOIN payroll b ON a.emp_id = b.emp_id WHERE a.department = 'Sales' AND b.salary > 50000;
Output: [{'table_name': 'employees', 'column_name': 'department', 'operator': '=', 'value': 'Sales'}, {'table_name': 'payroll', 'column_name': 'salary', 'operator': '>', 'value': 50000}]

11.
Query: SELECT name FROM (SELECT * FROM employees WHERE department = 'HR') AS dept_hr;
Output: N/A

12.
Query: WITH recent_sales AS (SELECT product_id, sale_date, amount FROM sales WHERE sale_date > '2023-01-01') SELECT * FROM recent_sales WHERE amount >= 1000;
Output: [{'table_name': 'recent_sales', 'column_name': 'amount', 'operator': '>=', 'value': 1000}]

```
