
# Learn SQL By Building a Student Database

**Disclaimer:** This README.md is for my personal references. It is not in anyway an attempt to reproduce the work of freecodecamp.org. To get access to free trainings on Databases, visit [freecodecamp](https://www.freecodecamp.org/)

I initially did this project within a gitpod sandbox. It can be replicated on any postgresql instance on cloud or premise.

Includes common commands used in POSTGRESQL Database.


### Objective
- To create a database in postgresql and insert data from csv files into the database.

- To create a pg_dump file of the created database.

- To practice sql commands within postgresql.

### Prerequisites
POSTGRESQL
SHELL SCRIPTING knowledge.
Basic Linux commands

You have two .csv files with info about fictional computer science students located within this folder.
- courses.csv
- students.csv


1. Login to the psql interactive terminal as user `freecodecamp`
#
    psql --username=freecodecamp --dbname=postgres


2. View existing databases
#
    \l

3. Create Database named `students`
#
    CREATE DATABASE students;

4. Connect to `students` Database
#
    \c students


The CSV files have a bunch of students with info about them, and some courses and majors. You will have four tables. One for the students and their info, one for each major, another for each course, and a final one for showing what courses are included in each major. First, create the students table.

5. Create table named `students`
#
    CREATE TABLE students();

 Create a table named `majors`
#
    CREATE TABLE majors();

Also create tables for `courses` and `majors_courses` using the syntax above.
`majors_courses`  will be a junction table.

6. View tables
#
    \d

### students table
7. Add a column to your `students` table named `student_id`. Give it a type of `SERIAL` so it automatically increments and make it a `PRIMARY KEY`
The primary key in SQL is a single, or a group of fields or columns that can uniquely identify a row in a table.
In an SQL table, the primary key acts like a special fingerprint. It's either a single column or a combination of columns that always pinpoints exactly one row of data. No two rows can share the same primary key value.

#
    ALTER TABLE students ADD COLUMN student_id SERIAL PRIMARY KEY;

8. The first column in students.csv is `first_name`. Add a column to the students table with that name. Make it a type of `VARCHAR(50)` and give it the `NOT NULL` constraint.

#
    ALTER TABLE students ADD COLUMN first_name VARCHAR(50) NOT NULL;
    
9. The next column in the data is `last_name`. Add it to the students table. Give it the same data type `VARCHAR(50)` and max-length as first_name and make sure it has the `NOT NULL` constraint.

#
    ALTER TABLE students ADD COLUMN last_name VARCHAR(50) NOT NULL;

10. The next column is for the `major`. Since you will have each major in another table this column will be a **foreign key** that references it. Create a column in the students table named `major_id`, give it a data type of `INT` for now. You will come back and set the foreign key later.

#
    ALTER TABLE students ADD COLUMN major_id INT;

11. 
Create the last column, `gpa`. The data in the CSV shows that they are decimals with a length of `2` and `1` number is to the right of the decimal. So give it a data type of `NUMERIC(2,1)`

#
    ALTER TABLE students ADD COLUMN gpa NUMERIC(2,1);

12. Verify details by viewing the students table
#
    \d students;

### majors table
13. The foreign key is still missing. Let's fill in the `majors` table next. Add a `major_id` column to it. Make it a type of `SERIAL` and the `PRIMARY KEY` for this table.

#
    ALTER TABLE majors ADD COLUMN major_id SERIAL PRIMARY KEY;

14. This table will only have one other column for the **name of the major**. Add a column to it named `major`. Make it a `VARCHAR(50)` and give it the `NOT NULL` constraint.

#
    ALTER TABLE majors ADD COLUMN major VARCHAR(50) NOT NULL;  

15. Now, set the `major_id` column from the students table as a **foreign key** that references the `major_id` column from the majors table. 
Foreign keys are like arrows in a database. They point from a column in one table to a matching column in another table, connecting related data.
`ALTER TABLE <table_name> ADD FOREIGN KEY(<column_name>) REFERENCES <referenced_table_name>(<referenced_column_name>);`

#
    ALTER TABLE students ADD FOREIGN KEY (major_id) REFERENCES majors (major_id);

### courses table
16. Next, is the `courses` table. Add a `course_id` column to it. Give it a type of `SERIAL` and make it the primary key.

#
    ALTER TABLE courses ADD COLUMN course_id SERIAL PRIMARY KEY;

17. Add a `course` column to the courses table that's a type of `VARCHAR(100)` because the names are likely longer. Also, make sure it can't accept null values-`NOT NULL`

#
    ALTER TABLE courses ADD COLUMN course VARCHAR(100) NOT NULL; 
18. Verify
#
    \d courses;

### majors_courses junction table
19. The majors_courses junction table will have two columns, each referencing the primary key from two related table. First, add a `major_id` column to it. Just give it a type of `INT` for now.

A junction table in SQL is a special kind of table that acts a bridge, used to manage many-to-many relationships between two other tables.
#
    ALTER TABLE majors_courses ADD COLUMN major_id INT;

20. Set the `major_id` column you just created as a foreign key that references the `major_id` column from the `majors` table.

#
    ALTER TABLE majors_courses ADD FOREIGN KEY (major_id) REFERENCES majors (major_id);

21. Next, add a `course_id` column to the same table. Just give it a type of `INT` again for now.

#
    ALTER TABLE majors_courses ADD COLUMN course_id INT;

22. Set your new `course_id` column as a foreign key that references the other `course_id` column.

#
    ALTER TABLE majors_courses ADD FOREIGN KEY (course_id) REFERENCES courses (course_id);

23. You can create a **composite primary key** that uses more than one column as a unique pair like this: `ALTER TABLE <table_name> ADD PRIMARY KEY(<column_name>, <column_name>);` 

A composite primary key in SQL is a way to uniquely identify rows in a table using a combination of two or more columns, rather than just a single column like a regular primary key

Add a composite primary key to the table using the two columns.

#
    ALTER TABLE majors_courses ADD PRIMARY KEY(major_id, course_id);

24. Verify
#
    \d majors_courses;

### Adding info to tables

25. Since the students table needs a `major_id`, we will add a major first. View the details of the majors table to see what info it expects.
#
    \d majors;

26. It only needs the name of a major. The ID will be added automatically. Add the first major from the courses.csv file into the majors table. It's a VARCHAR, so make sure to put the value in single quotes.

#
    INSERT INTO majors(major) VALUES('Database Administration');
27. View all the data in the `majors` table
#
    SELECT * FROM majors;

28. Insert the first course from courses.csv into the courses table.
View the table to check what fields are expected: 

#
    \d courses;

#
    INSERT INTO courses(course) VALUES('Data Structures and Algorithms'); 

29.  View all the data in the `courses` table
#
    SELECT * FROM courses;

30. you can add a row into the junction table. View the details of it to see what it expects.

#
    \d majors_courses;

`INSERT INTO <table_name>(<column_1_name>, <column_2_name>) VALUES(<value_1>, <value_2>);`

#
    INSERT INTO majors_courses(major_id, course_id) VALUES(1, 1);

View
#
    SELECT * FROM majors_courses;

31. The output shows what the table needs. Insert the first person from students.csv into the students table.
Check the fields expected. One of which is `major_id`. View the data in `majors` table to see the serial number.

#
    INSERT INTO students(first_name, last_name, major_id, gpa) VALUES('Rhea', 'Kellems', 1, 2.5);

View
#
    SELECT * FROM students;


### Creating script to enter data to database
32. Create a file named `insert_data.sh` and make it executable
#
    touch insert_data.sh

#
    chmod +x insert_data.sh

33. Edit the `insert_data.sh` with a suitable editor:

```
#!/bin/bash

# Script to insert data from courses.csv and students.csv into students database

PSQL="psql -X --username=freecodecamp --dbname=students --no-align --tuples-only -c"
echo $($PSQL "TRUNCATE students, majors, courses, majors_courses")

cat courses.csv | while IFS="," read MAJOR COURSE
do
  if [[ $MAJOR != "major" ]]
  then
    # get major_id
    MAJOR_ID=$($PSQL "SELECT major_id FROM majors WHERE major='$MAJOR'")

    # if not found
    if [[ -z $MAJOR_ID ]]
    then
      # insert major
      INSERT_MAJOR_RESULT=$($PSQL "INSERT INTO majors(major) VALUES('$MAJOR')")
      if [[ $INSERT_MAJOR_RESULT == "INSERT 0 1" ]]
      then
        echo Inserted into majors, $MAJOR
      fi

      # get new major_id
      MAJOR_ID=$($PSQL "SELECT major_id FROM majors WHERE major='$MAJOR'")
    fi

    # get course_id
    COURSE_ID=$($PSQL "SELECT course_id FROM courses WHERE course='$COURSE'")

    # if not found
    if [[ -z $COURSE_ID ]]
    then
      # insert course
      INSERT_COURSE_RESULT=$($PSQL "INSERT INTO courses(course) VALUES('$COURSE')")
      if [[ $INSERT_COURSE_RESULT == "INSERT 0 1" ]]
      then
        echo Inserted into courses, $COURSE
      fi

      # get new course_id
      COURSE_ID=$($PSQL "SELECT course_id FROM courses WHERE course='$COURSE'")
    fi

    # insert into majors_courses
    INSERT_MAJORS_COURSES_RESULT=$($PSQL "INSERT INTO majors_courses(major_id, course_id) VALUES($MAJOR_ID, $COURSE_ID)")
    if [[ $INSERT_MAJORS_COURSES_RESULT == "INSERT 0 1" ]]
    then
      echo Inserted into majors_courses, $MAJOR : $COURSE
    fi
  fi
done

cat students.csv | while IFS="," read FIRST LAST MAJOR GPA
do
  if [[ $FIRST != "first_name" ]]
  then
    # get major_id
    MAJOR_ID=$($PSQL "SELECT major_id FROM majors WHERE major='$MAJOR'")
   
    # if not found
    if [[ -z $MAJOR_ID ]]
    then 
            
    # set to null
    MAJOR_ID=null
    fi
    
    # insert student
    INSERT_STUDENT_RESULT=$($PSQL "INSERT INTO students(first_name, last_name, major_id, gpa) VALUES('$FIRST', '$LAST', $MAJOR_ID, $GPA)")
    if [[ $INSERT_STUDENT_RESULT == "INSERT 0 1" ]]
    then
            echo "Inserted into students, $FIRST $LAST"
    fi

  fi
done
```
34. Run the script with the following command:
#
    ./insert-data.sh

35. Finally make a pg_dump of the database and name it  `students.sql` with the following command. A pg_dump file is a format used to create backups of PostgreSQL databases. It's essentially a text file containing SQL statements that, when executed, can recreate the database schema and data in its entirety. 
#
    pg_dump --clean --create --inserts --username=freecodecamp students > students.sql

36. Verify and view the content of the pg_dump with the `cat` command.

 
**Notes**
The MAJOR variable is only being set to the first word. There's a default IFS variable in bash. IFS stands for "Internal Field Separator". View it with `declare -p IFS`. 
Between the while and read commands, we must set the IFS to a comma like this: IFS="," We will be able to view the full words after doing this.

Above your loop, add a PSQL variable that looks like this: `PSQL="psql -X --username=freecodecamp --dbname=students --no-align --tuples-only -c"`. This will allow you to query your database from your script. The important parts are the `username`, `dbname`, and the `-c` flag that is for running a single command and exiting. The rest of the flags are for formatting.

Now, you can query your database using the PSQL variable like this: `$($PSQL "<query_here>")`. The code in the parenthesis will run in a subshell, which is a separate bash process.

Other commands
1. To delete all the data in `majors` table
#
    TRUNCATE majors;

or if referenced by a foreign key
#
    TRUNCATE majors, students, majors_courses;


### Conclusion
We were able to create a database and insert data into it with sql commands and shell script. We also created a dump of the database.


