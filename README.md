# MYSQL-Challenge
SQL - Tableau integration projects for Data Analytics, Business Intelligence and Data Visualizations.


## Create Table
 ```mysql

                  
                                         
CREATE TABLE t_employees (
    emp_no      INT		        NOT NULL,
    birth_date  DATE            NOT NULL,
    first_name  VARCHAR(14)     NOT NULL,
    last_name   VARCHAR(16)     NOT NULL,
    gender      ENUM ('M','F')  NOT NULL,    
    hire_date   DATE            NOT NULL,
    PRIMARY KEY (emp_no)
);

CREATE TABLE t_departments (
    dept_no     CHAR(4)         NOT NULL,
    dept_name   VARCHAR(40)     NOT NULL,
    PRIMARY KEY (dept_no),
    UNIQUE  KEY (dept_name)
);

CREATE TABLE t_dept_manager (
   emp_no       INT             NOT NULL,
   dept_no      CHAR(4)         NOT NULL,
   from_date    DATE            NOT NULL,
   to_date      DATE            NOT NULL,
   PRIMARY KEY (emp_no,dept_no)
); 

CREATE TABLE t_dept_emp (
    emp_no      INT             NOT NULL,
    dept_no     CHAR(4)         NOT NULL,
    from_date   DATE            NOT NULL,
    to_date     DATE            NOT NULL,
    PRIMARY KEY (emp_no,dept_no)
);

CREATE TABLE t_salaries (
    emp_no      INT             NOT NULL,
    salary      INT             NOT NULL,
    from_date   DATE            NOT NULL,
    to_date     DATE            NOT NULL,
    PRIMARY KEY (emp_no, from_date)
) 
;

```

## Insert satatment 
 ```mysql
INSERT INTO t_employees(emp_no,birth_date,first_name,last_name,gender,hire_date) VALUES
(10008,'1958-02-19','Saniya','Kalloufi','M','1994-09-15'),
(10011,'1953-11-07','Mary','Sluis','F','1990-01-22'),
(10012,'1960-10-04','Patricio','Bridgland','M','1992-12-18'),
(10016,'1961-05-02','Kazuhito','Cappelletti','M','1995-01-27'),
(10017,'1958-07-06','Cristinel','Bouloucos','F','1993-08-03'),
(10019,'1953-01-23','Lillian','Haddadi','M','1999-04-30'),
(10020,'1952-12-24','Mayuko','Warwick','M','1991-01-26'),
(10022,'1952-07-08','Shahaf','Famili','M','1995-08-22'),
(10024,'1958-09-05','Suzette','Pettey','F','1997-05-19'),
(10026,'1953-04-03','Yongqiao','Berztiss','M','1995-03-20'),
(10028,'1963-11-26','Domenick','Tempesti','M','1991-10-22'),
(10030,'1958-07-14','Elvis','Demeyer','M','1994-02-17'),
(10031,'1959-01-27','Karsten','Joslin','M','1991-09-01'),
(10032,'1960-08-09','Jeong','Reistad','F','1990-06-20'),
(10036,'1959-08-10','Adamantios','Portugali','M','1992-01-03'),
(10037,'1963-07-22','Pradeep','Makrucki','M','1990-12-05'),
(10040,'1959-09-13','Weiyi','Meriste','F','1993-02-14'),
(10042,'1956-02-26','Magy','Stamatiou','F','1993-03-21'),
(10043,'1960-09-19','Yishay','Tzvieli','M','1990-10-20'),
(10044,'1961-09-21','Mingsen','Casley','F','1994-05-21'),
(10046,'1960-07-23','Lucien','Rosenbaum','M','1992-06-20'),
(10049,'1961-04-24','Basil','Tramer','F','1992-05-04'),
(10050,'1958-05-21','Yinghua','Dredge','M','1990-12-25'),
(10051,'1953-07-28','Hidefumi','Caine','M','1992-10-15'),
(10054,'1957-04-04','Mayumi','Schueller','M','1995-03-13'),
(10055,'1956-06-06','Georgy','Dredge','M','1992-04-27'),
(10056,'1961-09-01','Brendon','Bernini','F','1990-02-01'),
(10057,'1954-05-30','Ebbe','Callaway','F','1992-01-15'),
(10059,'1953-09-19','Alejandro','McAlpine','F','1991-06-26'),
(10062,'1961-11-02','Anoosh','Peyn','M','1991-08-30'),
(10073,'1954-02-23','Shir','McClurg','M','1991-12-01'),
```
## Query : Select (LIKE, IN)
 ```mysql
/*Select all people from the “employees” table whose first name is “Elvis”.*/
SELECT *
FROM employees
WHERE first_name LIKE 'Elvis';

/*Use the IN operator to select all individuals from the “employees” table, whose first name is either “Denis”, or “Elvis”.*/
SELECT *
FROM employees
WHERE first_name IN ('Denis','Elvis');

/*Retrieve a list with data about all female employees who were hired in the year 2000 or after.*/
/*Hint: If you solve the task correctly, SQL should return 7 rows.*/
SELECT *
FROM employees
WHERE YEAR(hire_date) >= 2000
AND gender = 'F';

/*Write a query that obtains two columns. The first column must contain annual salaries higher than 80,000 dollars. 
The second column, renamed to “emps_with_same_salary”, must show the number of employees contracted to that salary. 
Lastly, sort the output by the first column.*/
SELECT salary, COUNT(salary) AS "emps_with_same_salary"
FROM salaries
WHERE salary > 80000
GROUP BY 1
ORDER BY 1;

 

```
## Query : Join
```mysql
/*Extract a list containing information about all managers’ employee number, first and last name, 
department number, and hire date. Use the old type of join syntax to obtain the result.*/
/*Old style*/
SELECT e.emp_no, e.first_name, e.last_name, dm.dept_no, e.hire_date
FROM employees e, dept_manager dm
WHERE  dm.emp_no = e.emp_no
ORDER BY dm.dept_no DESC, e.emp_no;
```
## Query : Sub-Query
```mysql
/*Extract the information about all department managers who were hired between the 1st of January 1990 and the 1st of January 1995.*/
SELECT *
FROM dept_manager
WHERE emp_no IN (
	SELECT emp_no FROM employees
    WHERE hire_date BETWEEN '1990-01-01' AND '1995-01-01'
);
```
## Query : View
```mysql
/*get the latest department info of the employee using VIEW*/
CREATE OR REPLACE VIEW v_dept_emp_latest_date AS
	SELECT emp_no, MAX(from_date) AS from_date, MAX(to_date) AS to_date
    FROM dept_emp
    GROUP BY emp_no;
    
SELECT * FROM v_dept_emp_latest_date;
```


## Query : Trigger
```mysql

/*check salary < 0 or not when inserted , if < 0, insert as 0 instead of negative number*/
DELIMITER $$
CREATE TRIGGER before_salaries_insert
BEFORE INSERT ON salaries
FOR EACH ROW
BEGIN
	IF NEW.salary < 0 THEN
		SET NEW.salary = 0;
	END IF;
END $$
DELIMITER ;


SELECT * FROM salaries
WHERE emp_no = '10001';

#testing
INSERT INTO salaries VALUES ('10001', -92891, '2010-06-22', '9999-01-01');
```
## Query : Case
```mysql
/*Create a fourth column in the query, indicating whether this employee is also a manager, 
according to the data provided in the dept_manager table, or a regular employee. */
SELECT e.emp_no, e.first_name, e.last_name,
	CASE
		WHEN dm.emp_no IS NOT NULL THEN 'Manager'
        ELSE 'Regular Employee'
    END AS manager_or_employee
FROM employees e
LEFT JOIN dept_manager dm ON dm.emp_no = e.emp_no
```
## Query : Index

we create index when we use that field a lot in our queries.
by using index we gain faster result!
```mysql
/*Select all records from the ‘salaries’ table of people whose salary is higher than $89,000 per annum.
Then, create an index on the ‘salary’ column of that table, and check if it has sped up the search of the same SELECT statement.*/
SELECT salary FROM salaries
WHERE salary > 89000;

CREATE INDEX i_salary
ON salaries(salary);

SELECT salary FROM salaries
WHERE salary > 89000;
```
## visualize it by Tablue 
cooming soon


