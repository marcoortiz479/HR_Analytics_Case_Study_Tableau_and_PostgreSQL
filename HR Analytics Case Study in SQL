SELECT *
FROM education_level;

SELECT *
FROM performance_rating;

SELECT *
FROM employees;

SELECT *
FROM "employees"
LEFT JOIN "performance_rating" ON "employees"."EmployeeID" = "performance_rating"."EmployeeID";

SELECT *
FROM "employees"
LEFT JOIN "education_level" ON "employees"."Education" = "education_level"."EducationLevelID";


SELECT "education_level"."EducationLevel", COUNT( DISTINCT "employees"."EmployeeID") AS total_employees
FROM "employees"
LEFT JOIN "education_level" ON "employees"."Education" = "education_level"."EducationLevelID"
GROUP BY "education_level"."EducationLevel";

--KPI's of employee count and attrition rate
WITH current_emp AS (
    SELECT COUNT( "employees"."EmployeeID") AS current_emp_count
    FROM employees
    WHERE "Attrition" = 'No'
),
past_employees AS (
    SELECT COUNT( "employees"."EmployeeID") AS past_emp_count
    FROM employees
    WHERE "Attrition" = 'Yes'
),
total_employees AS (
    SELECT COUNT( "employees"."EmployeeID") AS total_emp_count
    FROM employees
)
SELECT *, ROUND((past_emp_count*1.0/total_emp_count)*100,2) AS attrition_rate_perc
FROM current_emp
CROSS JOIN past_employees
CROSS JOIN total_employees;

-- Current employee rate, per department and job role
SELECT "Department", "JobRole", COUNT("EmployeeID") AS current_employee_count
FROM employees
WHERE "Attrition" = 'No'
GROUP BY "Department", "JobRole"
ORDER BY "Department" ASC, current_employee_count DESC;

-- current employee total spilt by Age group and gender
SELECT 
    CASE 
        WHEN "Age" BETWEEN 0 AND 19 THEN '19 & under'
        WHEN "Age" BETWEEN 20 AND 29 THEN '20 - 29'
        WHEN "Age" BETWEEN 30 AND 39 THEN '30 - 39'
        WHEN "Age" BETWEEN 40 AND 49 THEN '40 - 49'
        ELSE '50 & over'
    END AS "age_group",
    "Gender",
    COUNT("EmployeeID") AS total_current_employees
FROM employees
WHERE "Attrition" = 'No'
GROUP BY "age_group", "Gender"
ORDER BY "age_group", "total_current_employees" DESC;

-- Current employees by marriage type
WITH current_marital_status AS(
    SELECT "MaritalStatus",
        COUNT("EmployeeID") AS total_current_employees"
    FROM employees
    WHERE "Attrition" = 'No'
    GROUP BY "MaritalStatus"
),
total_marital_status AS (
    SELECT "MaritalStatus", 
        COUNT("EmployeeID") AS total_employees_count
    FROM employees
    GROUP BY employees."MaritalStatus"
),
total_employees AS (
    SELECT COUNT( "employees"."EmployeeID") AS total_curr_emp_count
    FROM employees
    WHERE "Attrition" = 'No'
)
SELECT 
    total_marital_status."MaritalStatus", 
    current_marital_status."total_current_employees" AS "current_employees",
    ROUND((current_marital_status."total_current_employees"*1.0/total_employees."total_curr_emp_count")*100, 2) AS "%_of_curr_total"
FROM  total_marital_status
LEFT JOIN current_marital_status ON  total_marital_status."MaritalStatus" = current_marital_status."MaritalStatus"
CROSS JOIN total_employees;

--Attrition by Department and Job Role
WITH current_emp AS (
    SELECT 
        "Department",
        "JobRole",
        COUNT( "employees"."EmployeeID") AS current_emp_count
    FROM employees
    WHERE "Attrition" = 'No'
    GROUP BY "Department", "JobRole"
    ORDER BY "Department"
),
past_employees AS (
    SELECT 
        "Department",
        "JobRole",
        COUNT( "employees"."EmployeeID") AS past_emp_count
    FROM employees
    WHERE "Attrition" = 'Yes'
    GROUP BY "Department", "JobRole"
    ORDER BY "Department"
),
total_employees AS (
    SELECT 
        "Department",
        "JobRole",
        COUNT( "employees"."EmployeeID") AS total_emp_count
    FROM employees
    GROUP BY "Department", "JobRole"
    ORDER BY "Department"
)
SELECT 
    ce."Department",
    ce."JobRole" ,
    COALESCE(current_emp_count, 0) AS current_emp_count,
    COALESCE(past_emp_count, 0) AS past_emp_count,
    COALESCE(total_emp_count, 0) AS total_emp_count,
    COALESCE(
        ROUND((past_emp_count*1.0/total_emp_count)*100,1),
        0.00) AS attrition_rate_perc
FROM current_emp ce
LEFT JOIN past_employees pe ON ce."Department" = pe."Department" AND ce."JobRole" = pe."JobRole"
LEFT JOIN total_employees te ON te."Department" = pe."Department" AND te."JobRole" = pe."JobRole"
ORDER BY ce."Department",  ce."JobRole" ;

--Attrition by Travel
WITH past_employees AS (
    SELECT 
        "BusinessTravel",
        COUNT( "employees"."EmployeeID") AS past_emp_count
    FROM employees
    WHERE "Attrition" = 'Yes'
    GROUP BY "BusinessTravel"
),
total_employees AS (
    SELECT 
        "BusinessTravel",
        COUNT( "employees"."EmployeeID") AS total_emp_count
    FROM employees
    GROUP BY "BusinessTravel"
),
sum_total_employees AS(
    SELECT COUNT( "employees"."EmployeeID") AS sum_emp_count
    FROM employees
)
SELECT 
    pe."BusinessTravel",
    ROUND((total_emp_count*1.0/sum_emp_count)*100,1) AS total_emp_count_perc,
    ROUND((past_emp_count*1.0/total_emp_count)*100,1) AS attrition_rate_perc
FROM past_employees pe 
LEFT JOIN total_employees te ON te."BusinessTravel" = pe."BusinessTravel" 
CROSS JOIN sum_total_employees
