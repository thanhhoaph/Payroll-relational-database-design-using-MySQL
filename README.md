# Payroll database

## 1. Overview
This project was a group project in my Business Analytics program. However, I decided to improve the project by refining and making some changes to the database design and database itself.

This database includes information of 10 people in a company from different departments, their working day records, salaries, allowances and net salaries for each month. With the main purpose of practicing to design a database and implementing it using MySQL, I decided to keep the database small with 15 records.

## 2. Database design
After finalizing all the information I wanted for my payroll database, I used draw.io to visualize ER diagram and schema diagram. 
### ER diagram


![Image](https://user-images.githubusercontent.com/133604339/238210737-058f438c-2d63-450c-a401-70c7f89f0620.jpg)

### Schema diagram


![Image](https://user-images.githubusercontent.com/133604339/238210760-ae894fb5-4f6e-46fd-9ca0-4ed669c95ffc.jpg)




## 3. Invent data and create database using MySQL command prompt
There are totally 6 tables, codes used to create tables and insert data for each table as below. For inserting values to each table, I show here only one record as an example to make it simple for review.

create database payroll;
use payroll;

#### - Employee table:
```
create table employee (
    -> emp_id varchar(8) not null,
    -> first_name varchar(100),
    -> last_name varchar(100),
    -> job_title varchar(100),
    -> gender varchar(10),
    -> email varchar(100),
    -> dob date,
    -> doj date,
    -> dept_name varchar(100)
    -> primary key (emp_id));
insert into employee
    -> values ("66928",  "Mark", "John", "Manager", "M", "mjohn@abc.com", cast('1997-04-19' as datetime), cast('2020-03-04' as datetime), "Sales");
```

#### - Phone table:
```
create table phone (
    -> emp_id varchar(8),
    -> phone_num varchar(15),
    -> primary key (emp_id, phone_num));
alter table phone add foreign key (emp_id) references employee(emp_id);
insert into phone
    -> values ("66928", "9028229897");
```

#### - Timesheet table:
```
create table timesheet (
    -> timesheet_id varchar(8) not null,
    -> year int(4),
    -> month TINYINT(2),
    -> working_day TINYINT(2),
    -> paid_leave TINYINT(2),
    -> emp_id VARCHAR (8),
    -> primary key (timesheet_id),
    -> foreign key (emp_id) references employee(emp_id));
insert into timesheet
    -> values ("26928", 2022, 11, 18, 2, "66928");
alter table timesheet add total_paid_day TINYINT(2) as (working_day+paid_leave);
```

#### - Salary table:
```
create table salary (
    -> salary_id varchar(8) not null,
    -> fixed_salary decimal(10,2),
    -> emp_id varchar(8),
    -> timesheet_id varchar(8),
    -> primary key (salary_id),
    -> foreign key (emp_id) references employee(emp_id),
    -> foreign key (timesheet_id) references timesheet(timesheet_id));
insert into salary
    -> values ("36928", 154000, "66928", "26928");
alter table salary add total_salary decimal(10,2);
update salary T1 set T1.total_salary = T1.fixed_salary/260*(select T2.total_paid_day from timesheet T2 where T1.timesheet_id = T2.timesheet_id);
```

#### - Allowances table:
```
create table allowances (
    -> allowance_id varchar(8),
    -> meal decimal(10,2),
    -> transportation decimal(10,2),
    -> year INT(4),
    -> month TINYINT(2),
    -> emp_id VARCHAR (8),
    -> primary key (allowance_id),
    -> foreign key (emp_id) references employee(emp_id));
insert into allowances
    -> values ("76928", 250, 100, 2022, 11, "66928");
alter table allowances add total_allowance decimal(10,2) as (meal+transportation);
```

#### - Paycheck table:
```
create table paycheck (
    -> pay_id varchar(8),
    -> emp_id varchar(8),
    -> salary_id varchar(8),
    -> allowance_id varchar(8),
    -> primary key (pay_id),
    -> foreign key (emp_id) references employee(emp_id),
    -> foreign key (salary_id) references salary(salary_id),
    -> foreign key (allowance_id) references allowances(allowance_id));
insert into paycheck
    -> values ("96928", "66928", "36928", "76928");
alter table paycheck add gross_salary decimal(10,2);
update paycheck T1 set T1.gross_salary = (select T2.total_allowance+T3.total_salary from allowances T2, salary T3 where T1.salary_id = T3.salary_id and T1.allowance_id = T2.allowance_id and T1.emp_id = T2.emp_id and T1.emp_id = T3.emp_id);

alter table paycheck add tax_ded decimal(10,2) as (gross_salary*0.2);

alter table paycheck add net_salary decimal(10,2) as (gross_salary-tax_ded);
```

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
