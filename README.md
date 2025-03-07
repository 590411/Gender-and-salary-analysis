
# Evolutive analysis  of humain resource in term of gender and salaries

## Table of Content
-  [Project Overview](#project-overview)
-  [Data Sources](#data-sources)
-  [Tools](#tools)
-  [Data cleaning ](#data-cleaning )
-  [Exploratory data analysis](#exploratory-data-analysis)
-  [Data analysis](#data-analysis)
-  [Findings](#findings)
  
### Project overview
---

**Delivrables**

  p1-create a visualization that provides a breakdown between male and female employees working in the company each year , starting from 1990
	p1-Compare the number of male managers to the number of female managers from different departments for each year, starting from 1990
  p2-compare the number of male managers to the  number of female managers from different departments for each year starting from 1990
  P3-Compare the average salary of female versus male employees in the entire company until year 2002, and add a filter allowing you to see that per each department
  P4-Find the average male and female salary per department within a certain salary range .Let this range be defined by two values the user can insert . Finally, visualize the obtained result-set in tableau as a double bar chart


 ### Data sources
 ---

*Company  Data ,the dataset use for this analysis is "Employees_mod.sql"  file . we have data on employees , departments, department manager -----*

### Tools
---

  - SQL: Data cleaning
  - MySQL : ETL
  - Excel : Transformation and loading 
  - Tableaux : Data model , visualizations, dashboard and story
  
      
### Data cleaning 
---

p1-Query of breakdown between male and female employees working in the company each year , starting from 1990

 ```
		select
		year(D.from_date) as Calendar_year,
		E.gender as Gender,
		count(E.emp_no) as Number_of_employees
		from t_employees as E JOIN  t_dept_emp as D  ON E.emp_no = D.emp_no
		group by Calendar_year, Gender 
		having Calendar_year >= 1990 
		order by  Calendar_year asc
```

P2.Query to determined the number of male managers to the  number of female managers from different departments for each year starting from 1990

```

                  SELECT 
    d.dept_name,
    ee.gender,
    dm.emp_no,
    dm.from_date,
    dm.to_date,
    e.calender_year,
    CASE
        WHEN
            YEAR(dm.to_date) >= e.calender_year
                AND YEAR(dm.from_date) <= e.calender_year
        THEN
            1
        ELSE 0
    END AS active
FROM
    (SELECT 
        YEAR(hire_date) AS calender_year
    FROM
        t_employees
    GROUP BY calender_year) e
        CROSS JOIN
    t_dept_manager dm
        JOIN
    t_departments d ON dm.dept_no = d.dept_no
        JOIN
    t_employees ee ON dm.emp_no = ee.emp_no
ORDER BY dm.emp_no , calender_year

```


 P3.Query to determined the average salary of female versus male employees in the entire company until year 2002 per department

 ```
SELECT 
    e.gender,
    d.dept_name,
    ROUND(AVG(s.salary), 2) AS salary,
    YEAR(s.from_date) AS calendar_year
FROM
    t_salaries s
        JOIN
    t_employees e ON s.emp_no = e.emp_no
        JOIN
    t_dept_emp de ON de.emp_no = e.emp_no
        JOIN
    t_departments d ON d.dept_no = de.dept_no
GROUP BY d.dept_no , e.gender , calendar_year
HAVING calendar_year <= 2002
ORDER BY d.dept_no;

```

P4- Query to determined the average male and female salary per department within a certain salary rang

In order to have the salary range , we create an SQL store procedure with two inputs parameter . The two values of the range can be insert when call the procedure .
Lets P_min_salary = 50 000  and P_max_salary = 90 000


```
drop procedure if exists filter_salary ;
delimiter $$
create procedure filter_salary ( IN p_min_salary float, IN P_max_salary float )
begin
select 
e.gender,
d.dept_name,
round(avg(s.salary),2) as salary

from t_employees e 
join t_salaries s  on e.emp_no = s.emp_no
join t_dept_emp de on e.emp_no = de.emp_no
join t_departments d on  de.dept_no = d.dept_no 

where  s.salary between  p_min_salary and  p_max_salary
group by  e.gender  , d.dept_name ;
end$$
delimiter ;

call  filter_salary (50000 , 90000) ;    

```


### Exploratory data analysis
---

EDA involved exploring  the sales data to  answer key question , such as :

  - What is the overrall  number of men and women working in the company ?
  - Determine the proportion of male to female manager  per  departments ?
  - What is the average salary of female versus male employees in the entire company  ?
  - what is the average male and female salary per department within a certain salary range  ?

### Data analysis

In order to have the salary range , we create an SQL store procedure with two inputs parameter . The two values of the range can be insert when call the procedure .

Lets P_min_salary =50 000  and P_max_salary = 90 000



### Findings
---

 p1---Base on the Bar chart:
 
      -we can see that in 1990,there was less than 20 000 employees in the company while in 2002 there was more than 140 000 employees. 
      -The increase in the number of workers year by year have been  constant
      -The ratio of male and female has been approximately constant : Male 60% and Female 40%

 p2---Base on the area chart
 
      -Globally there is a constant increase from year to year of both male and female managers from 1990 to 1998
      -Globally over the years ,the increase in the number of male managers is always higher than that of female managers 

 p3---Base on the line chart
 
       - Salaries have been augmenting constantly for any department
       - The average salaries of male and female employees were approximately similar until 1993, and  after that male employees were paid more , with a few thousand of difference per year

 P4--- Base on the double bar chart
 
       - Both the average female and male salary was relatively high,  above  60 000 for the finance, marketing and sales departments
 

💻💻  


