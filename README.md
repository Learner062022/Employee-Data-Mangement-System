# Employee and Management Database

This repository contains SQL scripts for creating and managing a database to store employee data and their assosciated skills for a small game developement shop. The database supports various queries and stored procedures to facilitate operations and analytics.

## Database Structure
### Employees Table
The **employees** table stores the first name, last name, annual salary, and contact information (address, email, work phone, and personal phone) for each employee.
```sql
CREATE TABLE IF NOT EXISTS employees (
    first_name VARCHAR(10) NOT NULL,
    last_name VARCHAR(10) NOT NULL,
    annual_salary DECIMAL(8, 2) UNSIGNED NOT NULL,
    address VARCHAR(100) NOT NULL,
    email VARCHAR(100) NOT NULL PRIMARY KEY,
    work_phone VARCHAR(15) NOT NULL,
    personal_phone VARCHAR(15) NOT NULL
);

CREATE INDEX idx_annual_salary ON employees (annual_salary);
  ```
#### Sample Data:
```sql
INSERT INTO employees VALUES 
('Dylan', 'De Souza', 60000.00, '4 Somewhere', '12345678@tafe.wa.edu.au', '0401234567', '0412345678'),
('Daniel', 'Johnston', 35000.00, '5 Somewhereanywhere', '23456789@tafe.wa.edu.au', '0423456789', '0434567890'),
('Jordan', 'Clark', 10000.00, '6 Anywhere', '34567890@tafe.wa.edu.au', '0445678901', '0456789012'),
('Michael', 'Stead', 45000.00, '7 Someplace', '45678901@tafe.wa.edu.au', '0467890123', '0478901234'),
('Peter', 'Jolly', 405000.00, '8 Someplacesomewhere', '56789012@tafe.wa.edu.au', '0478901234', '0489012345');
  ```
### Game Skills Table 
The **game_skills** table stores various game-related skills.
```sql
CREATE TABLE IF NOT EXISTS game_skills (
    skill VARCHAR(15) NOT NULL PRIMARY KEY
);

CREATE INDEX idx_skill ON game_skills (skill);
  ```
#### Sample Data:
```sql
INSERT INTO game_skills (skill) VALUES
('programming'),
('3D modelling'),
('sound effects'),
('music'),
('level design'),
('play testing'),
('team management');
  ```
### Job Capabilities Table
The **job_capabilities** table links employees to their skills with a rating (0-10).
```sql
CREATE TABLE IF NOT EXISTS job_capabilities (                     
    skill VARCHAR(15) NOT NULL,
    employee_email VARCHAR(100) NOT NULL,
    skill_rating TINYINT UNSIGNED NOT NULL,
    PRIMARY KEY (skill, employee_email),
    FOREIGN KEY (skill) REFERENCES game_skills(skill),
    FOREIGN KEY (employee_email) REFERENCES employees(email)
);

CREATE INDEX idx_job_capabilities_skill ON job_capabilities (skill);
CREATE INDEX idx_job_capabilities_email ON job_capabilities (employee_email);
  ```
#### Sample Data:
```sql
INSERT INTO job_capabilities (skill, employee_email, skill_rating) VALUES
('programming', '12345678@tafe.wa.edu.au', 2),
('3D modelling', '23456789@tafe.wa.edu.au', 4),
('sound effects', '34567890@tafe.wa.edu.au', 6),
('music', '45678901@tafe.wa.edu.au', 8),
('level design', '56789012@tafe.wa.edu.au', 10);
  ```
## Views
### Names and Salaries
The **names_salaries** view displays the names and salaries of all employees.
```sql
CREATE VIEW names_salaries AS 
SELECT first_name, last_name, annual_salary 
FROM employees;
  ```
### Names, Salaries, Skills and Ranks
The **names_salaries_skills_ranks** displays the names of the employees, their salaries, their skills and the ranks they have in those skills.
```sql
CREATE VIEW names_salaries_skills_ranks AS
SELECT employees.first_name, employees.last_name, employees.annual_salary, job_capabilities.skill, job_capabilities.skill_rating 
FROM job_capabilities
INNER JOIN employees ON employees.email = job_capabilities.employee_email;
  ```
## Procedures
### Salary Queries
This procedure provides various salary-related queries.
```sql
DELIMITER //
CREATE PROCEDURE get_high_earning_employees (amount_earned DECIMAL(8, 2) UNSIGNED)
BEGIN
     -- Displaying names and salaries sorted by salary in descending order
    SELECT * FROM names_salaries ORDER BY annual_salary DESC;

    -- Calculating fortnightly salary and sorting by last name and first name
    SELECT first_name, last_name, annual_salary / 26 AS fortnightly_salary
    FROM names_salaries 
    ORDER BY last_name, first_name;

    -- Calculating the total monthly salary bill
    SELECT SUM(annual_salary / 12) AS total_monthly_salary
    FROM names_salaries;

    -- Displaying the name and salary of the highest-paid employee
    SELECT first_name, last_name, annual_salary 
    FROM names_salaries 
    WHERE annual_salary = (SELECT MAX(annual_salary) FROM names_salaries);
END //
DELIMITER ;
  ```
### Get High Earning Employees
This procedure displays all employees with a higher salary than the given amount.
```sql
DELIMITER //
CREATE PROCEDURE get_high_earning_employees (amount_earned DECIMAL(10, 2) UNSIGNED)
BEGIN
    SELECT first_name, last_name 
    FROM employees 
    WHERE annual_salary > amount_earned;
END //
DELIMITER ;
  ```
### Salary Range
This procedure displays all employees with a salary in the given range.
```sql
DELIMITER //
CREATE PROCEDURE salary_range (min_salary DECIMAL(10, 2) UNSIGNED, max_salary DECIMAL(10, 2) UNSIGNED)
BEGIN
    SELECT first_name, last_name 
    FROM names_salaries 
    WHERE annual_salary BETWEEN min_salary AND max_salary 
    ORDER BY annual_salary ASC;
END //
DELIMITER ;
  ```
### Skills Salary Ratio
This procedure calculates the skill to salary ratio for a given employee.
```sql
DELIMITER //
CREATE PROCEDURE skill_salary_ratio (in_first_name VARCHAR(10), in_last_name VARCHAR(10))
BEGIN
    SELECT annual_salary / total_skill_rating AS salary_skill_ratio
    FROM (
        SELECT annual_salary, SUM(skill_rating) AS total_skill_rating
        FROM names_salaries_skills_ranks 
        WHERE first_name = in_first_name
        AND last_name = in_last_name
        GROUP BY annual_salary
    ) AS employee_skills;
END //
DELIMITER ;
  ```
## Queries
### Display Names, Salaries, and Skill Ratings
```sql
SELECT first_name, last_name, skill_rating 
FROM job_capabilities 
INNER JOIN employees ON employees.email = job_capabilities.employee_email;
  ```
### Display Employees with Salary > $50000
```sql
SELECT * FROM names_salaries_skills_ranks 
WHERE annual_salary > 50000 
ORDER BY annual_salary ASC;
  ```
### Calculate Average Skill Rating for Programming
```sql
SELECT AVG(skill_rating) 
FROM names_salaries_skills_ranks 
WHERE skill = 'programming';
  ```
