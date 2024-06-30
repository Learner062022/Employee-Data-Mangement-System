# Employee and Skills Database

## Overview
Project contains SQL scripts to create and manage a database for a small game developement shop. The database includes tables for employee informationb, game-related skills and the relationship between them. Various views and procedures are included to query and manage the data efficiently.

## Database Structure
### Employees Table
This table stores employee data.
```sql
CREATE TABLE IF NOT EXISTS employees (
    first_name VARCHAR(10),
    last_name VARCHAR(10),
    annual_salary DECIMAL(8, 2) UNSIGNED,
    address VARCHAR(100),
    email VARCHAR(100) NOT NULL PRIMARY KEY,
    work_phone CHAR(10),
    personal_phone CHAR(10)
);
  ```
### Sample Data:
```sql
INSERT INTO employees VALUES 
('Dylan', 'De Souza', 60000.00, '4 Somewhere', '12345678@tafe.wa.edu.au', '0401234567', '0412345678'),
('Daniel', 'Johnston', 35000.00, '5 Somewhereanywhere', '23456789@tafe.wa.edu.au', '0423456789', '0434567890'),
('Jordan', 'Clark', 10000.00, '6 Anywhere', '34567890@tafe.wa.edu.au', '0445678901', '0456789012'),
('Michael', 'Stead', 45000.00, '7 Someplace', '45678901@tafe.wa.edu.au', '0467890123', '0478901234'),
('Peter', 'Jolly', 405000.00, '8 Someplacesomewhere', '56789012@tafe.wa.edu.au', '0478901234', '0489012345');
  ```
### Game Skills Table 
This table stores various game-related skills.
```sql
CREATE TABLE IF NOT EXISTS game_skills (
    id INT UNSIGNED NOT NULL AUTO_INCREMENT PRIMARY KEY,
    skill VARCHAR(15)
);
  ```
### Sample Data:
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
This table links employees to their skills with a rating from 0-10.
```sql
CREATE TABLE IF NOT EXISTS job_capabilities (
    id INT UNSIGNED NOT NULL AUTO_INCREMENT PRIMARY KEY,
    game_skill_id INT UNSIGNED,
    employee_email VARCHAR(100),
    skill_rating TINYINT UNSIGNED
);
  ```
### Sample Data:
```sql
INSERT INTO job_capabilities (game_skill_id, employee_email, skill_rating) VALUES
(1, '12345678@tafe.wa.edu.au', 2),
(2, '23456789@tafe.wa.edu.au', 4),
(3, '34567890@tafe.wa.edu.au', 6),
(4, '45678901@tafe.wa.edu.au', 8),
(5, '56789012@tafe.wa.edu.au', 10);
  ```
## Views
### Names and Salaries
This view displays the names and salaries of all employees.
```sql
CREATE VIEW names_salaries AS 
SELECT first_name, last_name, annual_salary 
FROM employees;
  ```
### Names, Salaries, Skills and Ranks
This view displays the names of the employees, their salaries, their skills and the ranks they have in those skills.
```sql
CREATE VIEW names_salaries_skills_ranks AS
SELECT first_name, last_name, annual_salary, skill, skill_rating 
FROM job_capabilities
INNER JOIN employees ON email = employee_email
INNER JOIN game_skills ON game_skill_id = game_skills.id;
  ```
## Procedures
### Get High Earning Employees
This procedure takes a salary and displays all the employees with a higher salary than that.
```sql
DELIMITER //
CREATE PROCEDURE get_high_earning_employees (amount_earned DECIMAL(8, 2) UNSIGNED)
BEGIN
    SELECT first_name, last_name 
    FROM employees 
    WHERE annual_salary > amount_earned;
END //
DELIMITER ;
  ```
### Salary Range
This procedure takes a salary range and displays all the employees with a salary in that range sorted from lowest to highest.
```sql
DELIMITER //
CREATE PROCEDURE salary_range (min_salary DECIMAL(8, 2) UNSIGNED, max_salary DECIMAL(8, 2) UNSIGNED)
BEGIN
    SELECT first_name, last_name 
    FROM names_salaries 
    WHERE annual_salary BETWEEN min_salary AND max_salary 
    ORDER BY annual_salary ASC;
END //
DELIMITER ;
  ```
### Skills Salary Ratio
This procedure takes an employee and shows his or her salary divided by the total of their skill rankings.
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
### Sorted Names and Salaries
Displays the names and salaries of the employees sorted from biggest salary to smallest.
```sql
SELECT * FROM names_salaries 
ORDER BY annual_salary DESC;
  ```
### Fortnightly Salary
Displays how much money each employee gets in a fortnight, sorted by last name and then first name.
```sql
SELECT first_name, last_name, annual_salary / 26 AS fortnightly_salary
FROM names_salaries 
ORDER BY last_name, first_name;
  ```
### Total Monthly Salary Bill
Displays the total monthly salary bill for the company.
```sql
SELECT SUM(annual_salary / 12) AS total_monthly_salary
FROM names_salaries;
  ```
### Highest Paid Employee
Displays the name and salary of the employee that is paid the most.
```sql
SELECT first_name, last_name, annual_salary 
FROM names_salaries 
WHERE annual_salary = (SELECT MAX(annual_salary) FROM names_salaries);
  ```
### Employees Earning More Than $50,000
Displays information in ascending order of salary for those employees earning more than $50,000.
```sql
SELECT * FROM names_salaries_skills_ranks 
WHERE annual_salary > 50000 
ORDER BY annual_salary ASC;
  ```
### Average Skill Ranking
Displays the average ranking for a skill given its name.
```sql
SELECT AVG(skill_rating) 
FROM names_salaries_skills_ranks 
WHERE skill = "programming";
  ```
## License
[GNU General Public License](https://www.gnu.org/licenses/gpl-3.0.txt)
