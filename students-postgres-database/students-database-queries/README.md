# Working with our students database

Objectives:
- Create a script to print info about students


1. Build the database from the pg_dump file student.sql

#
    psql -U postgres < students.sql

Verify 
#
    \l

3. Connect to the database
#
    \c students

4. Display the tables and relations
#
    \d students
5. Run some commands. Select the column first_name from the students table with the following command:
#
    SELECT first_name FROM students;

6. Select multiple columns by separating with a comma:
#
    SELECT first_name, last_name, gpa FROM students;

7. You can return only rows you want by adding WHERE <condition> to your query. A condition can consist of a column, an operator, and a value. Use one of these to view the same columns as before but only rows WHERE gpa < 2.5

#
    SELECT first_name, last_name, gpa FROM students WHERE gpa < 2.5;


8. The < only return rows where the gpa column was less than 2.5. Some other operators are: <, >, <=, >=. View the same columns, but only rows for students with a gpa greater than or equal to 3.8.

#
    SELECT first_name, last_name, gpa FROM students WHERE gpa >= 3.8;

9. There's equal (=) and not equal (!=) operators as well. View the same columns for students that don't have a 4.0 gpa.

#
    SELECT first_name, last_name, gpa FROM students WHERE gpa != 4.0;

10. Use the = to view all majors named Game Design. Don't forget that You need single quotes around text values.

#
    SELECT * FROM majors WHERE major = 'Game Design';

11. Use the greater than operator to see majors that come after it alphabetically.

# 
    SELECT * FROM majors WHERE major > 'Game Design';

12. So if you want to see results that start with a G or after, you could use major >= 'G'. View the majors that come before G.
#
    SELECT * FROM majors WHERE major < 'G';

13. You can use multiple conditions after WHERE with AND or OR, among others. Just add the keyword and another condition. In the psql prompt, use the same command as before, but add an OR to also return rows of students with a 3.9 GPA.

#
    SELECT * FROM students WHERE last_name < 'M' OR gpa = 3.9;

AND:
#
    SELECT * FROM students WHERE last_name < 'M' AND gpa = 3.9;

#
    SELECT * FROM students WHERE last_name < 'M' AND gpa = 3.9 OR gpa < 2.3;

14. You can group conditions together with parenthesis like this: WHERE <condition_1> AND (<condition_2> OR <condition_2>). This would only return rows where <condition_1> is true and one of the others is true. View students whose last name is before M that have a GPA of 3.9 or less than 2.3.

#
    echo "$($PSQL "SELECT first_name, last_name, gpa FROM students WHERE last_name >= 'R' AND (gpa > 3.8 OR gpa < 2.0)")"

15. You can use LIKE to find patterns in text like this: WHERE <column> LIKE '<pattern>'. An underscore (_) in a pattern will return rows that have any character in that spot. View the rows in this table with a course name that matches the pattern '_lgorithms'

#
    SELECT * FROM courses WHERE course LIKE '_lgorithms';

16. That pattern matched only rows that had exactly one character, followed by lgorithms. Another pattern character is %. It means anything can be there. To find names that start with W, you could use W%. View the courses that end in lgorithms

#
    SELECT * FROM courses WHERE course LIKE '%lgorithms';

For courses beginning with Web
#
    SELECT * FROM courses WHERE course LIKE 'Web%';

17. Combine the two pattern matching characters to show courses that have a second letter of e
#
    SELECT * FROM COURSES WHERE course LIKE '_e%';

18. View rows with spaces in their names
#
    SELECT * FROM courses WHERE course LIKE '% %';

19. You can use NOT LIKE to find things that don't match a pattern. View courses that don't contain a space.
#
    SELECT * FROM courses WHERE course NOT LIKE '% %';

20. Try finding the courses that contain an A.

#
    SELECT * FROM courses WHERE course LIKE '%A%';

21. To ignore the cases use, ILIKE.

#
    SELECT * FROM courses WHERE course ILIKE '%A%';

22. You can put NOT in front of ILIKE as well. Use it to see the courses that don't contain an A or a.

#
    SELECT * FROM courses WHERE course NOT ILIKE '%A%';

23. View courses without A or a and have a space
#
    SELECT * FROM courses WHERE course NOT ILIKE '%A%' AND course LIKE '% %';

24. View students who do not have GPA.
#
    SELECT * FROM students WHERE gpa IS NULL;

25. who have GPA
#
    SELECT * FROM students WHERE gpa IS NOT NULL;

26. View students who do not have a major but dont include the students that don't have a gpa
#
    SELECT * FROM students WHERE major_id IS NULL AND gpa IS NOT NULL;

27. You can specify the order you want your results to be in by adding ORDER BY <column_name> at the end of a query. In the psql prompt, view all the info in the students table in order by the GPA's

 When using ORDER BY, it will be in ascending (ASC) order by default. Add DESC (descending) at the end of the last query to put the highest ones at the top.
#
    SELECT * FROM students ORDER BY gpa;

For descending order
#
    SELECT * FROM students ORDER BY gpa DESC;

Order by multiple columns:
#
    SELECT * FROM students ORDER BY gpa, first_name;


28. Many times, you only want to return a certain number of rows. You can add LIMIT <number> at the end of the query to only get the amount you want. View the students in the same order as the last command, but only return the first 10 rows.
#
    SELECT * FROM students ORDER BY gpa DESC, first_name LIMIT 10;

29. Note that The order of the keywords in your query matters. You cannot put LIMIT before ORDER BY, or either of them before WHERE. View the same number of students, in the same order, but don't get the ones who don't have a GPA.  

#
    SELECT * FROM students WHERE gpa IS NOT NULL ORDER BY gpa DESC, first_name LIMIT 10;


30. There's a number of mathematic functions to use with numerical columns. One of them is MIN, you can use it when selecting a column like this: SELECT MIN(<column>) FROM <table>. It will find the lowest value in the column. In the psql prompt, view the lowest value in the gpa column of the students table.

To get the minimum gpa from the students table
#
    SELECT MIN(gpa) FROM students;


Another one is MAX, use it to see the largest gpa of the same table.
#
    SELECT MAX(gpa) FROM students;

31. To sum up a column:
#
    SELECT SUM(major_id) FROM students;

32. To get AVG of a column:
#
    SELECT AVG(major_id) FROM students;

33. You can round decimals up or down to the nearest whole number with CEIL and FLOOR, respectively. Use CEIL to round the average major_id up to the nearest whole number. Here's an example: CEIL(<number_to_round>)

#
    SELECT CEIL(AVG(major_id)) FROM students;

34. you can round a number to the nearest whole number with ROUND. Use it to round the average of the major_id column to the nearest whole number. 

#
    SELECT ROUND(AVG(major_id)) FROM students;  


35. You can round to a specific number of decimal places by adding a comma and number to ROUND, like this: ROUND(<number_to_round>, <decimals_places>). Round the average of the major_id to five decimal places.

#
    SELECT ROUND(AVG(major_id), 5) FROM students; 


36. Another function is COUNT. You can use it like this: COUNT(<column>). It will tell you how many entries are in a table for the column. Try it out in the psql prompt by using COUNT(*) to see how many majors there are.

#
    SELECT COUNT(*) FROM majors; 

#
    SELECT COUNT(*) FROM students;

37. View the count of the major_id column in the students table to see how many of your students have picked a major.

#
    SELECT COUNT(major_id) FROM students;

38. Using major_id didn't count the null values in that column. 23 students have a major. DISTINCT is a function that will show you only unique values. You can use it like this: DISTINCT(<column>). View the unique major_id values in the students table.
#
    SELECT DISTINCT(major_id) FROM students; 

39. There's six unique major_id values in the students table. You can get the same results with GROUP BY. Here's an example of how to use it: SELECT <column> FROM <table> GROUP BY <column>. Use this method to view the unique major_id values in the students table again.
#
    SELECT major_id FROM students GROUP BY major_id;

40. The output was the same as DISTINCT, but with GROUP BY you can add any of the aggregate functions (MIN, MAX, COUNT, etc) to it to find more information. For instance, if you wanted to see how many students were in each major you could use SELECT COUNT(*) FROM students GROUP BY major_id. View the major_id column and number of students in each major_id

#
    SELECT  major_id, COUNT (*) FROM students GROUP BY major_id;


41. When using GROUP BY, any columns in the SELECT area must be included in the GROUP BY area. Other columns must be used with any of the aggregate functions (MAX, AVG, COUNT, etc). View the unique major_id values with GROUP BY again, but see what the lowest GPA is in each of them.
#
    SELECT  major_id, MIN(gpa) FROM students GROUP BY major_id;

42. Enter the same query, but add a column that shows you the highest GPA in each major as well.

#
    SELECT  major_id, MIN(gpa), MAX(gpa) FROM students GROUP BY major_id;

43. Another option with GROUP BY is HAVING. You can add it at the end like this: SELECT <column> FROM <table> GROUP BY <column> HAVING <condition>. The condition must be an aggregate function with a test. An example to might be to use HAVING COUNT(*) > 0 to only show what whatever column is grouped that have at least one row. Use HAVING to only show rows from the last query that have a maximum GPA of 4.0.

#
    SELECT  major_id, MIN(gpa), MAX(gpa) FROM students GROUP BY major_id HAVING MAX(gpa) = 4.0;

44. Two of your majors have at least one student with a 4.0 GPA. Looking at the results, the column is named min. You can rename a column with AS like this: SELECT <column> AS <new_column_name> Enter the same command, but rename the min column to min_gpa and the max column to max_gpa

#
    SELECT  major_id, MIN(gpa) AS min_gpa, MAX(gpa) AS max_gpa FROM students GROUP BY major_id HAVING MAX(gpa) = 4.0;

45.  View the major_id and number of students in each major_id in a column named number_of_students.

#
    SELECT major_id, COUNT (*) AS number_of_students FROM students GROUP BY major_id;

46. Use HAVING with the last query to only show the rows with less than eight students in the major.

#
    SELECT major_id, COUNT (*) AS number_of_students FROM students GROUP BY major_id HAVING COUNT(*) < 8;

47. The majors and students table are linked with the major_id foreign key. If you want to see the name of a major that a student is taking, you need to JOIN the two tables into one. Here's an example of how to do that:
SELECT * FROM <table_1> FULL JOIN <table_2> ON <table_1>.<foreign_key_column> = <table_2>.<foreign_key_column>;


#
    SELECT * FROM students FULL JOIN majors ON students.major_id = majors.major_id;

48. It's showing all the columns from both tables, the two major_id columns are the same in each row for the ones that have it. You can see that there are some students without a major, and some majors without any students. The FULL JOIN you used will include all rows from both tables, whether or not they have a row using that foreign key in the other. From there, you could use any of the previous methods to narrow down, group, order, etc. Use a LEFT JOIN to join the same two tables in the same way.



#
    SELECT * FROM students LEFT JOIN majors ON students.major_id = majors.major_id;

49. There's a few less rows than the last query. In the LEFT JOIN you used, the students table was the left table since it was on the left side of the JOIN. majors was the right table. A LEFT JOIN gets all rows from the left table, but only rows from the right table that are linked to from the left one. Looking at the data, you can see that every student was returned, but the majors without any students were not. Join the same two tables with a RIGHT JOIN this time.


#
    SELECT * FROM students RIGHT JOIN majors ON students.major_id = majors.major_id;

50. The right join showed all the rows from the right table (majors), but only rows from the left table (students) if they have a major. There's one more type you should know about. Join the two tables with an INNER JOIN.

#
    SELECT * FROM students INNER JOIN majors ON students.major_id = majors.major_id;


51. The INNER JOIN only returned students if they have a major and majors that have a student. In other words, it only returned rows if they have a value in the foreign key column (major_id) of the opposite table. You should know a little about the four main types of joins now. Try using a LEFT JOIN to show all the majors but only students that have a major.

#
    SELECT * FROM majors LEFT JOIN students ON majors.major_id = students.major_id;

52. Next, use the appropriate join to show only students that are enrolled in a major, and only majors that have a student enrolled in it.

#
    SELECT * FROM students INNER JOIN majors ON students.major_id = majors.major_id; 

53. Try using a right join to show all students but only majors if a student is enrolled in it.

#
    SELECT * FROM majors RIGHT JOIN students ON majors.major_id = students.major_id;

54. That showed all the students since it was the right table of the RIGHT JOIN. Use the appropriate join with the same two table to show all rows in both tables whether they have a value in the foreign key column or not.
#
    SELECT * FROM majors FULL JOIN students ON majors.major_id = students.major_id;


55. Say you wanted to find a list of majors that students are taking. Use the most efficient JOIN to join the two tables you need. Only join the tables for now, don't use any other conditions.

#
    SELECT * FROM majors INNER JOIN students ON majors.major_id = students.major_id;


    SELECT major FROM majors INNER JOIN students ON majors.major_id = students.major_id;

56. Assuming You also don't want any duplicates. Use DISTINCT to only return the unique ones to see the list of majors who have students.

#
    SELECT DISTINCT(major) FROM majors INNER JOIN students ON majors.major_id = students.major_id;


57. Next, say you wanted a list of majors that students aren't taking. Use the most efficient JOIN to join the two tables you need. Only join the tables for now, don't use any other conditions.

#
    SELECT * FROM students RIGHT JOIN majors ON students.major_id = majors.major_id;

    SELECT * FROM students RIGHT JOIN majors ON students.major_id = majors.major_id WHERE student_id IS NULL;

    SELECT major FROM students RIGHT JOIN majors ON students.major_id = majors.major_id WHERE student_id IS NULL;  


58. Next, use the most efficient 'JOIN' to join the tables you would need if you were asked to get the first name, last name, major, and GPA of students who are taking Data Science or have a gpa of 3.8 or greater. Only join the tables for now, don't use any other conditions.

    SELECT * FROM students LEFT JOIN majors ON students.major_id = majors.major_id WHERE major = 'Data Science' OR gpa >= 3.8;

    SELECT first_name, last_name, major, gpa FROM students LEFT JOIN majors ON students.major_id = majors.major_id WHERE major = 'Data Science' OR gpa >= 3.8;

59. From there, you could put them in a specific order if you wanted or limit the results to a certain number among other things. Lastly, use the most efficient 'JOIN' to join the tables you would need if you were asked to get the first name and major for students whose first_name, or the major, contains ri. Only join the tables for now, don't use any other conditions.

#
    SELECT * FROM students FULL JOIN majors ON students.major_id = majors.major_id;

    SELECT * FROM students FULL JOIN majors ON students.major_id = majors.major_id WHERE first_name LIKE '%ri%' OR major LIKE '%ri%';

#

    SELECT first_name, major FROM students FULL JOIN majors ON students.major_id = majors.major_id WHERE first_name LIKE '%ri%' OR major LIKE '%ri%';

60. List of majors, in alphabetical order, that either no student is taking or has a student whose first name contains a case insensitive 'ma'

#
    SELECT major FROM students FULL JOIN majors ON students.major_id = majors.major_id WHERE major IS NOT NULL AND (student_id IS NULL OR first_name ILIKE '%ma%') ORDER BY major;


61. SETTING ALIASES FOR READABILITY
    SELECT s.major_id FROM students AS s FULL JOIN majors AS m ON s.major_id = m.major_id; 

62. There's a shortcut keyword, USING to join tables if the foreign key column has the same name in both tables. Here's an example: SELECT * FROM <table_1> FULL JOIN <table_2> USING(<column>);. Use this method to see all the columns in the students and majors table. Don't use any aliases.

#
    SELECT * FROM students FULL JOIN majors USING(major_id);

63. Note that the two major_id columns were turned into one with USING. In order to find out what courses a student is taking, you will need to join all the tables together. You can add a third table to a join like this: SELECT * FROM <table_1> FULL JOIN <table_2> USING(<column>) FULL JOIN <table_3> USING(<column>). This example will join the first two tables into one, turning it into the left table for the second join. Use this method to join the two tables from the previous query with the majors_courses table.

#
    SELECT * FROM students FULL JOIN majors USING(major_id) FULL JOIN majors_courses USING(major_id); 


64. You may need to adjust the terminal size to align the output. What you're seeing is every unique combination of rows in the database. Students with a major are listed multiple times, one for each course included in the major. The majors without any students are there along with the courses for them. The students without a major are included, they have no courses and are only listed once. You can join as many tables together as you want. Join the last table to the previous command to get the names of the courses with all this info.

#
    SELECT * FROM students FULL JOIN majors USING(major_id) FULL JOIN majors_courses USING(major_id) FULL JOIN courses USING(course_id);

    SELECT DISTINCT(course) FROM students RIGHT JOIN majors USING(major_id) INNER JOIN majors_courses USING(major_id) INNER JOIN courses USING(course_id) WHERE (first_name = 'Obie' AND last_name = 'Hilpert') OR student_id IS NULL ORDER BY course DESC


66. SHELL SCRIPT TO RUN QUERIES ON DB

 Create a file named `students_info.sh` and give it executable permissions.(`chmod +x`)
The following script will run some complex queries on the database. We can apply the syntax to run more complex queries:

```
#!/bin/bash

# Info about my computer science students from students database

echo -e "\n~~ My Computer Science Students ~~\n"

PSQL="psql -X --username=freecodecamp --dbname=students --no-align --tuples-only -c"

echo -e "\nFirst name, last name, and GPA of students with a 4.0 GPA:"
echo "$($PSQL "SELECT first_name, last_name, gpa FROM students WHERE gpa = 4.0")"

echo -e "\nAll course names whose first letter is before 'D' in the alphabet:"
echo "$($PSQL "SELECT course FROM courses WHERE course < 'D'")"

echo -e "\nFirst name, last name, and GPA of students whose last name begins with an 'R' or after and have a GPA greater than 3.8 or less than 2.0:"
echo "$($PSQL "SELECT first_name, last_name, gpa FROM students WHERE last_name >= 'R' AND (gpa > 3.8 OR gpa < 2.0)")"

echo -e "\nLast name of students whose last name contains a case insensitive 'sa' or have an 'r' as the second to last letter:"
echo "$($PSQL "SELECT last_name FROM students WHERE last_name ILIKE '%sa%' OR last_name ILIKE '%r_'")"

echo -e "\nFirst name, last name, and GPA of students who have not selected a major and either their first name begins with 'D' or they have a GPA greater than 3.0:"
echo "$($PSQL "SELECT first_name, last_name, gpa FROM students WHERE major_id IS NULL AND (first_name LIKE 'D%' OR gpa > 3.0)")"

echo -e "\nCourse name of the first five courses, in reverse alphabetical order, that have an 'e' as the second letter or end with an 's':"
echo "$($PSQL "SELECT course FROM courses WHERE course LIKE '_e%' OR course LIKE '%s' ORDER BY course DESC LIMIT 5")"

echo -e "\nAverage GPA of all students rounded to two decimal places:"
echo "$($PSQL "SELECT ROUND(AVG(gpa), 2) FROM students")"

echo -e "\nMajor ID, total number of students in a column named 'number_of_students', and average GPA rounded to two decimal places in a column name 'average_gpa', for each major ID in the students table having a student count greater than 1:"
echo "$($PSQL "SELECT major_id, COUNT(*) AS number_of_students, ROUND(AVG(gpa), 2) AS average_gpa FROM students GROUP BY major_id HAVING COUNT(*) > 1")"

echo -e "\nList of majors, in alphabetical order, that either no student is taking or has a student whose first name contains a case insensitive 'ma':"
echo "$($PSQL "SELECT major FROM students FULL JOIN majors ON students.major_id = majors.major_id WHERE major IS NOT NULL AND (student_id IS NULL OR first_name ILIKE '%ma%') ORDER BY major")"

echo -e "\nList of unique courses, in reverse alphabetical order, that no student or 'Obie Hilpert' is taking:"

echo "$($PSQL "SELECT DISTINCT(course) FROM students RIGHT JOIN majors USING(major_id) INNER JOIN majors_courses USING(major_id) INNER JOIN courses USING(course_id) WHERE (first_name = 'Obie' AND last_name = 'Hilpert') OR student_id IS NULL ORDER BY course DESC")"

echo -e "\nList of courses, in alphabetical order, with only one student enrolled:"

echo "$($PSQL "SELECT course FROM students INNER JOIN majors_courses USING(major_id) INNER JOIN courses USING(course_id) GROUP BY course HAVING COUNT(student_id) = 1 ORDER BY course")"
```

