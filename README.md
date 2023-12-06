# Payroll relational database

## 1. Overview
This project was a group project in my Business Analytics program. However, I decided to improve the project by refining and making some changes to the database design and database itself.

This database includes information of 10 people in a company from different departments, their working day records, salaries, allowances and net salaries for each month. With the main purpose of practicing to design a database and implementing it using MySQL, I decided to keep the database small with 15 records.

## 2. Database design
After finalizing all the information I wanted for my payroll database, I used draw.io to visualize ER diagram and schema diagram. 
### ER diagram


![Image](https://user-images.githubusercontent.com/133604339/238210737-058f438c-2d63-450c-a401-70c7f89f0620.jpg)


### Schema diagram


![Image](https://user-images.githubusercontent.com/133604339/238210760-ae894fb5-4f6e-46fd-9ca0-4ed669c95ffc.jpg)




## 3. Invent data and create database using MySQL
There are totally 6 tables, codes used to create tables and insert data for each table can be found in the file attached.

[Create database.txt](https://github.com/thanhhoaph/payroll/files/11473112/Create.database.txt)




## 4. Testing database with some queries
#### - The average net salary group by department
```
select dept_name, round(avg(net_salary),2) as avg_salary
    -> from employee T1, paycheck T2
    -> where T1.emp_id = T2.emp_id
    -> group by dept_name;
```
#### - The list of employee in Sales that have net salary more than 4000, ordered from largest to smallest
```
select T1.emp_id, first_name, last_name, job_title, net_salary
    -> from employee T1, paycheck T2
    -> where T1.emp_id = T2.emp_id and net_salary > 4000 and T1.dept_name = "Sales"
    -> order by net_salary desc;
```
#### - The average net salary of employees that have total paid day > 20, group by department
```
select dept_name, round(avg(net_salary),2) as avg_salary, round(avg(total_paid_day),1) as avg_day
    -> from employee T1, timesheet T2, paycheck T3, salary T4
    -> where T1.emp_id = T2.emp_id and T1.emp_id = T3.emp_id and T2.timesheet_id = T4.timesheet_id and T4.salary_id = T3
.salary_id and T2.total_paid_day > 20
    -> group by dept_name;
```
#### - Working day of employees in Sales and Production departments, ordered by year, month, department name
```
select year, month, dept_name, first_name, working_day
    -> from employee T1, timesheet T2
    -> where T1.emp_id = T2.emp_id and (T1.dept_name = "Sales" or T1.dept_name = "Production")
    -> order by year, month, dept_name;
```
#### - The list of employees and their phone number in Production department.
```
select first_name, phone_num
    -> from employee T1, phone T2
    -> where T1.emp_id = T2.emp_id and T1.dept_name = "Production";
```

## 5. Team acknowledgement
As mentioned, at first this project was a group project, below is team members who contributed to the initial project.

Fiorella Maza

Megha Mohan

Blessy Anish

Anjana Rose
