### 1. Display all data from the `employees` table
```sql
SELECT * FROM employees;
```

### 2. Count how many employees have the job title "Manager"
```sql
SELECT COUNT(*) FROM employees WHERE job_title = 'Manager';
```

### 3. List names and salaries of employees working in "Sales" or "Marketing" departments
```sql
SELECT name, salary FROM employees WHERE department IN ('Sales', 'Marketing');
```

### 4. Calculate the average salary of employees who joined in the last 5 years
```sql
SELECT AVG(salary) AS average_salary FROM employees
WHERE joined_date >= (CURRENT_DATE - INTERVAL '5 year');
```

### 5. Display top 5 employees with the highest total sales
```sql
SELECT e.name, SUM(s.sales) AS total_sales
FROM employees e
JOIN sales_data s ON e.employee_id = s.employee_id
GROUP BY e.name
ORDER BY total_sales DESC
LIMIT 5;
```

### 6. Display the name, salary, and average salary of employees in the department with higher average salaries than the overall average
```sql
SELECT e.name, e.salary, d.avg_salary
FROM employees e
JOIN (
    SELECT department, AVG(salary) AS avg_salary
    FROM employees
    GROUP BY department
    HAVING AVG(salary) > (SELECT AVG(salary) FROM employees)
) d ON e.department = d.department;
```

### 7. Display the name and total sales of each employee ranked by total sales
```sql
SELECT e.name, COALESCE(SUM(s.sales), 0) AS total_sales, RANK() OVER (ORDER BY COALESCE(SUM(s.sales), 0) DESC) AS rank
FROM employees e
LEFT JOIN sales_data s ON e.employee_id = s.employee_id
GROUP BY e.name;
```

### 8. Create a stored procedure that accepts a department name and returns a list of employees and their total salaries in that department
**Creating the stored procedure:**
```sql
CREATE OR REPLACE FUNCTION get_department_salaries(dept_name VARCHAR)
RETURNS TABLE (employee_name VARCHAR, total_salary DECIMAL) AS $$
BEGIN
    RETURN QUERY
    SELECT name, salary FROM employees
    WHERE department = dept_name;
END;
$$ LANGUAGE plpgsql;
```
**Using the stored procedure:**
```sql
SELECT * FROM get_department_salaries('Sales');
```
