The code used in the solution videos for this lab can be found here.

### Investigate Source Database
1. In a terminal session, log in to the provided bastion host:

    ```
    ssh cloud_user@<BASTION_PUBLIC_IP>
    ```
2. Connect to the source database:
    ```
    mysql -h <SOURCE_DATABASE_PUBLIC_IP> -u cloud_user -pbettertogether -D employees
    ```
3. Take a look at the tables in the database:
    ```
    SHOW TABLES;
    ```
4. See what the employees table looks like:
    ```
    DESCRIBE employees;
    ```
5. See what the dms_source table looks like:
    ```
    DESCRIBE dms_source;
    ```
6. See the first 10 rows of the dms_source table:
    ```
    SELECT * FROM dms_source LIMIT 10\G
    ```
### Design Data Model and DynamoDB Table
With the information gathered from the source database, design your data model with consideration to the provided access patterns, and map this to DynamoDB table partition/sort keys, as well as any local or global secondary indexes.

Watch the "Solution 1/2" video to see our process.

### Create employees DynamoDB Table
1. In the AWS console, navigate to DynamoDB > Tables.
2. Click Create table, and set the following values:
* Table name: employees
* Primary key: department_name
3. Check Add sort key.
4. In the sort key field that appears, enter "TitleSalt".
5. Un-check Use default settings.
6. Click + Add index.
7. In the Add index dialog, set the following values:
* Primary key: department_name
* Add sort key: Check
* In the field under Add sort key: Fullname
* Index name: Fullname-index
* Create as Local Secondary Index: Check
8. Click Add index.
9. Click + Add index.
10. In the Add index dialog, set the following values:
* Primary key: department_name
* Add sort key: Check
* In the field under Add sort key: hire_date
* Index name: hire_date-index
* Create as Local Secondary Index: Check
11. Click Add index.
Click + Add index.
In the Add index dialog, set the following values:
Primary key: department_name
Add sort key: Check
In the field under Add sort key: title_from_date
Index name: title_from_date-index
Create as Local Secondary Index: Check
Click Add index.
Click + Add index.
In the Add index dialog, set the following values:
Primary key: title
Add sort key: Check
In the field under Add sort key: salary_to_date
Index name: salary_to_date-index
Click Add index.
In the Read/write capacity mode section, check On-demand.
Click Create. Give it a couple minutes to finish creating.
### Create/Start DMS Task
In a new browser tab, navigate to Database Migration Service.
Click Database migration tasks in the left-hand menu.
Click Create task, and set the following values:
Task Configuration
Task identifier: relational2ddb
Replication instance: relational2ddb
Source database endpoint: mysqlendpoint
Target database endpoint: ddbendpoint
Migration type: Migrate existing data
Start task on create: Select
Task settings
Target table preparation mode: Do nothing
Table mappings
Editing mode: JSON editor
In the JSON editor box that appears, delete the existing code and paste in the entire contents of the rules.json file (found on GitHub).
Click Create task. It will take about 10 minutes to complete.

### Test Access Patterns

In the DynamoDB browser tab, click the Items tab.

#### Get the department name, title, employee ID, first name, last name, and salary for all employees in department Development with title Senior Engineer (only including most recent salary):

Change Scan to Query.
Enter a partition key string value of "Development".
Click the = dropdown, and select Begins with.
Enter a sort key string value of "Senior Engineer".
Click Start search. This will return a list of records with a TitleSalt starting with Senior Engineer.
Click Add filter.
In the attribute field, enter "salary_to_date".
Enter a string value of "9999-01-01".
Click Start search. This will list all the records for employees with the title Senior Engineer and their most recent salary.
Clear the filter.

#### Get all information about an employee in Department Production with full name Hercules Benzmuller:

Change the query to [Index] Fullname-index: department_name, Fullname.
Enter a partition key string value of "Production".
Enter a sort key string value of "Hercules Benzmuller".
Click Start search. We should then see 14 records listed.

#### Get all employees in the Production department hired between 1999 and 2001:

Change to our [Index] hire_date-index: department_name, hire_date.
Enter a partition key string value of "Production".
Click the = dropdown, and select Between.
Enter values of "1999-01-01" and "2001-01-01".
Click Start search. We should see 34 records listed — but this includes duplicates.
Click Add filter.
Enter a filter attribute of "salary_to_date".
Enter a string value of "9999-01-01".
Click Start search. We should now see nine results (the correct amount).
Clear the filter.

#### Get all employees in the Development department who had a title change in the six months before January 1, 2001:

Change to our [Index] title_from_date-index: department_name, title_from_date.
Enter a partition key string value of "Development".
Click the = dropdown, and select Between.
Enter values of "2000-06-01" and "2001-01-01".
Click Start search. We'll get back a long list, which includes duplicates.
Click Add filter.
Enter a filter attribute of "salary_to_date".
Enter a string value of "9999-01-01".
Click Start search. We should now see 40 results (the correct amount).
Clear the filter.

#### Get all employees with title Engineer, returning only most recent salary:

1. Change to our [Index] salary_to_date-index: department_name, salary_to_date.
2. Enter a partition key string value of "Engineer".
3. Enter a sort key string value of "9999-01-01".
4. Click Start search. We'll get back a long list or results.