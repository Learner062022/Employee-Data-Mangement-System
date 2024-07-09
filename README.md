# EmployeeAndSkillManagementDatabase

This repository contains SQL scripts for creating and managing a database to store employee data and their assosciated skills for a small game developement shop. The database supports various queries and stored procedures to facilitate operations and analytics.

## Why This Project is Useful
Provides a structured way to strore retrieve and analyze employee information and their capabilities, making it easier to:
  - Track employee skills and their proficiency levels.
  - Analyze salary distribution and manage payroll effectively.
  - Identify high-earning employees and those within specific salary ranges.
  - Calculate skill-to-salary ratios to assess employee value and performance.

## Database Structure
  - **Employees Table**: Stores employees' first name, last name, annual salary, and contact information (address, email, work phone, and personal phone).
  - **Game Skills Table**: Stores various game-related skills.
  - **Job Capabilities Table**: Links employees to their skills with a rating (0-10).

## Sample Data
Included in the SQL scripts to populate the database for testing and demonstration purposes.

## Views
  - **names_salaries**: Displays employees' names and salaries.
  - **names_salaries_skills_ranks**: Displays employees':
    - Name
    - Salary
    - Skills
    - Skills' ranks.

## Procedures
  - **Salary Queries**: Provides various salary-related queries.
  - **Get High Earning Employees**: Displays employees with a higher salary than the given amount.
  - **Salary Range**: Displays employees with a salary in the given range.
  - **Skills Salary Ratio**: Calculates the skill to salary ratio for a given employee.

## Queries
  - **Display Names, Salaries, and Skill Ratings**: Display employees' names, salaries, and skill ratings.
  - **Display Employees with Salary > $50000**: Displays all employees with a salary greater than $50,000.
  - **Calculate Average Skill Rating for Programming**: Calculates the average skill rating for programming.

## Usage
Run the scripts in your preferred SQL environment.

## File 
**ProceduresAndViews**: Contains the SQL for creating tables, inserting sample data, creating views, procedures and executing queries.

## License
[GNU General Public License](https://www.gnu.org/licenses/gpl-3.0.txt)

## Contributing
Contributions are welcome! Please open an issue or submit a pull request for any changes or improvements.
