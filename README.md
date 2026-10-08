# 🎓 University Course Management System (UCMS)
### Advanced Relational Database Management System (RDBMS) & SQL Project

---

## 📌 Project Information

| Field | Details |
| :--- | :--- |
| **Student Name** | Priyal Patel |
| **Course & Subject** | SQL & Advanced Relational Database Management System (RDBMS) |
| **DBMS Platform** | MySQL Server & phpMyAdmin (Apache / XAMPP) |
| **Project Script File** | `university_management.sql` |
| **Database Name** | `final_project` |

---

## 📖 Executive Summary & Overview

The **University Course Management System** is an enterprise-grade relational database solution developed in **MySQL** and managed via **phpMyAdmin**. This database models a standard higher-education university structure by establishing clear relations between students, academic departments, instructors, courses, and course enrollments.

The primary objective of this project is to implement a robust database architecture satisfying **1NF, 2NF, and 3NF** normalization rules while demonstrating complete proficiency across:
- **Data Definition Language (DDL):** Primary Keys, Foreign Keys, Referential Constraints, Cascade rules.
- **Data Manipulation Language (DML):** Seeding realistic baseline records.
- **Full CRUD Operations:** Lifecycle management (`INSERT`, `SELECT`, `UPDATE`, `DELETE`) across all 5 entities.
- **Advanced Querying Techniques:** Data filtering (`WHERE`, `LIMIT`), logical operators (`AND`, `OR`, `IN`).
- **Aggregate Analytics & Grouping:** `COUNT`, `AVG`, `MAX`, `GROUP BY`, `HAVING`.
- **Relational Joins:** Multi-table `INNER JOIN` and `LEFT JOIN` structures.
- **Subqueries & Advanced Analytics:** Nested/correlated subqueries, analytical window functions (`SUM() OVER`), and dynamic conditional expressions (`CASE WHEN`).

---

## 🗄️ Database Architecture & Schema Design (DDL)

The schema consists of five interrelated core tables:

```
    +-------------------+           +-------------------+
    |    Departments    | 1       * |    Instructors    |
    |-------------------|-----------|-------------------|
    | PK: DepartmentID  |           | PK: InstructorID  |
    |     DepartmentName|           |     FirstName     |
    +-------------------+           |     LastName      |
              | 1                   |     Email         |
              |                     | FK: DepartmentID  |
              | *                   |     Salary        |
    +-------------------+           +-------------------+
    |      Courses      |
    |-------------------|
    | PK: CourseID      |
    |     CourseName    |
    | FK: DepartmentID  |
    |     Credits       |
    +-------------------+
              | 1
              |
              | *
    +-------------------+           +-------------------+
    |    Enrollments    | *       1 |     Students      |
    |-------------------|-----------|-------------------|
    | PK: EnrollmentID  |           | PK: StudentID     |
    | FK: StudentID     |           |     FirstName     |
    | FK: CourseID      |           |     LastName      |
    |     EnrollmentDate|           |     Email         |
    +-------------------+           |     BirthDate     |
                                    |     EnrollmentDate|
                                    +-------------------+
```

---

### DDL Table Creation Queries

#### 1. Students Table
Stores personal profiles, birth records, and admission timestamps.
```sql
CREATE TABLE Students (
    StudentID INT PRIMARY KEY,
    FirstName VARCHAR(50) NOT NULL,
    LastName VARCHAR(50) NOT NULL,
    Email VARCHAR(100) UNIQUE,
    BirthDate DATE,
    EnrollmentDate DATE NOT NULL
);
```

#### 2. Departments Table
Catalogs the university departments.
```sql
CREATE TABLE Departments (
    DepartmentID INT PRIMARY KEY,
    DepartmentName VARCHAR(100) NOT NULL
);
```

#### 3. Courses Table
Contains courses mapped to their parent department.
```sql
CREATE TABLE Courses (
    CourseID INT PRIMARY KEY,
    CourseName VARCHAR(100) NOT NULL,
    DepartmentID INT,
    Credits INT CHECK (Credits > 0),
    FOREIGN KEY (DepartmentID) REFERENCES Departments(DepartmentID)
        ON DELETE SET NULL ON UPDATE CASCADE
);
```

#### 4. Instructors Table
Faculty information including departmental association and compensation.
```sql
CREATE TABLE Instructors (
    InstructorID INT PRIMARY KEY,
    FirstName VARCHAR(50) NOT NULL,
    LastName VARCHAR(50) NOT NULL,
    Email VARCHAR(100) UNIQUE,
    DepartmentID INT,
    Salary DECIMAL(10, 2),
    FOREIGN KEY (DepartmentID) REFERENCES Departments(DepartmentID)
        ON DELETE SET NULL ON UPDATE CASCADE
);
```

#### 5. Enrollments Table (Junction Table)
Resolves the **Many-to-Many ($M:N$)** relationship between `Students` and `Courses`.
```sql
CREATE TABLE Enrollments (
    EnrollmentID INT PRIMARY KEY,
    StudentID INT,
    CourseID INT,
    EnrollmentDate DATE NOT NULL,
    FOREIGN KEY (StudentID) REFERENCES Students(StudentID)
        ON DELETE CASCADE ON UPDATE CASCADE,
    FOREIGN KEY (CourseID) REFERENCES Courses(CourseID)
        ON DELETE CASCADE ON UPDATE CASCADE
);
```

---

## 🌱 Sample Baseline Seeding (DML)

### 1. Seeding Departments
```sql
INSERT INTO Departments (DepartmentID, DepartmentName) VALUES
(1, 'Computer Science'),
(2, 'Mathematics'),
(3, 'Physics'),
(4, 'Business Administration'),
(5, 'Information Technology');
```

### 2. Seeding Students
```sql
INSERT INTO Students (StudentID, FirstName, LastName, Email, BirthDate, EnrollmentDate) VALUES
(1, 'John', 'Doe', 'john.doe@email.com', '2000-01-15', '2022-08-01'),
(2, 'Jane', 'Smith', 'jane.smith@email.com', '1999-05-25', '2021-08-01'),
(3, 'Michael', 'Brown', 'michael.brown@email.com', '2001-03-10', '2023-07-15'),
(4, 'Emily', 'Davis', 'emily.davis@email.com', '2000-11-20', '2024-08-01'),
(5, 'David', 'Wilson', 'david.wilson@email.com', '1999-09-05', '2023-08-10');
```

### 3. Seeding Courses
```sql
INSERT INTO Courses (CourseID, CourseName, DepartmentID, Credits) VALUES
(101, 'Introduction to SQL', 1, 3),
(102, 'Data Structures', 1, 4),
(103, 'Calculus', 2, 4),
(104, 'Physics Fundamentals', 3, 3),
(105, 'Business Management', 4, 3);
```

### 4. Seeding Instructors
```sql
INSERT INTO Instructors (InstructorID, FirstName, LastName, Email, DepartmentID, Salary) VALUES
(1, 'Robert', 'Johnson', 'robert.johnson@university.com', 1, 75000.00),
(2, 'Sarah', 'Williams', 'sarah.williams@university.com', 2, 68000.00),
(3, 'James', 'Miller', 'james.miller@university.com', 3, 72000.00),
(4, 'Linda', 'Anderson', 'linda.anderson@university.com', 4, 80000.00),
(5, 'Daniel', 'Taylor', 'daniel.taylor@university.com', 1, 85000.00);
```

### 5. Seeding Enrollments
```sql
INSERT INTO Enrollments (EnrollmentID, StudentID, CourseID, EnrollmentDate) VALUES
(1, 1, 101, '2022-08-05'),
(2, 1, 103, '2022-08-06'),
(3, 2, 101, '2021-08-05'),
(4, 2, 102, '2021-08-06'),
(5, 3, 101, '2023-07-20'),
(6, 3, 104, '2023-07-21'),
(7, 4, 102, '2024-08-05'),
(8, 4, 103, '2024-08-06'),
(9, 5, 101, '2023-08-15'),
(10, 5, 105, '2023-08-16');
```

---

## 🛠️ Complete Implementation of 16 Practical Tasks

### Task 1: Complete CRUD Operations Across All 5 Tables

#### 1.1 CRUD on `Students`
```sql
-- Create
INSERT INTO Students (StudentID, FirstName, LastName, Email, BirthDate, EnrollmentDate)
VALUES (6, 'Olivia', 'Martin', 'olivia.martin@email.com', '2001-07-12', '2024-08-10');

-- Read
SELECT * FROM Students WHERE StudentID = 6;

-- Update
UPDATE Students SET Email = 'olivia.martin@university.com' WHERE StudentID = 6;

-- Delete
DELETE FROM Students WHERE StudentID = 6;
```
> **Observation:** Student ID 6 verified through insert, retrieve, email update, and deletion without orphan dependency issues.

#### 1.2 CRUD on `Departments`
```sql
-- Create
INSERT INTO Departments (DepartmentID, DepartmentName) VALUES (6, 'Civil Engineering');

-- Read
SELECT * FROM Departments WHERE DepartmentID = 6;

-- Update
UPDATE Departments SET DepartmentName = 'Civil & Environmental Engineering' WHERE DepartmentID = 6;

-- Delete
DELETE FROM Departments WHERE DepartmentID = 6;
```
> **Observation:** Successfully manipulated department directory independently.

#### 1.3 CRUD on `Courses`
```sql
-- Create
INSERT INTO Courses (CourseID, CourseName, DepartmentID, Credits) VALUES (106, 'Robotics', 1, 3);

-- Read
SELECT * FROM Courses WHERE CourseID = 106;

-- Update
UPDATE Courses SET Credits = 4 WHERE CourseID = 106;

-- Delete
DELETE FROM Courses WHERE CourseID = 106;
```
> **Observation:** Managed course entity while respecting foreign key linkage to `Departments`.

#### 1.4 CRUD on `Instructors`
```sql
-- Create
INSERT INTO Instructors (InstructorID, FirstName, LastName, Email, DepartmentID, Salary)
VALUES (6, 'Alan', 'Turing', 'alan.turing@university.com', 1, 90000.00);

-- Read
SELECT * FROM Instructors WHERE InstructorID = 6;

-- Update
UPDATE Instructors SET Salary = 95000.00 WHERE InstructorID = 6;

-- Delete
DELETE FROM Instructors WHERE InstructorID = 6;
```
> **Observation:** Instructor salary modification and record purge completed successfully.

#### 1.5 CRUD on `Enrollments`
```sql
-- Create
INSERT INTO Enrollments (EnrollmentID, StudentID, CourseID, EnrollmentDate) VALUES (11, 1, 104, '2023-08-01');

-- Read
SELECT * FROM Enrollments WHERE EnrollmentID = 11;

-- Update
UPDATE Enrollments SET EnrollmentDate = '2023-08-02' WHERE EnrollmentID = 11;

-- Delete
DELETE FROM Enrollments WHERE EnrollmentID = 11;
```
> **Observation:** Tested the junction table while validating referential keys pointing to both Student and Course tables.

---

### Task 2: Retrieve Students Enrolled After 2022
```sql
SELECT *
FROM Students
WHERE EnrollmentDate > '2022-12-31';
```
> **Observation:** Accurately returned students enrolled in calendar years 2023 and 2024 (*Michael Brown*, *Emily Davis*, *David Wilson*).

---

### Task 3: Retrieve Courses Offered by Mathematics Department (Limit 5)
```sql
SELECT *
FROM Courses
WHERE DepartmentID = 2
LIMIT 5;
```
> **Observation:** Filters courses where `DepartmentID = 2` (Mathematics), enforcing a maximum of 5 records.

---

### Task 4: Find Courses Having More Than 5 Students
```sql
SELECT 
    c.CourseID,
    c.CourseName,
    COUNT(e.StudentID) AS StudentCount
FROM Courses c
JOIN Enrollments e ON c.CourseID = e.CourseID
GROUP BY c.CourseID, c.CourseName
HAVING COUNT(e.StudentID) > 5;
```
> **Observation:** Groups enrollments by Course and leverages `HAVING` to identify high-demand courses surpassing threshold capacity.

---

### Task 5: Find Students Enrolled in Both Specified Courses (Course 101 & 102)
```sql
SELECT 
    s.StudentID,
    s.FirstName,
    s.LastName
FROM Students s
JOIN Enrollments e1 ON s.StudentID = e1.StudentID
JOIN Enrollments e2 ON s.StudentID = e2.StudentID
WHERE e1.CourseID = 101 
  AND e2.CourseID = 102;
```
> **Observation:** Implements a self-join across `Enrollments` to isolate student records that satisfy intersection criteria.

---

### Task 6: Find Students Enrolled in Either Course (Course 101 OR 102)
```sql
SELECT DISTINCT 
    s.StudentID,
    s.FirstName,
    s.LastName
FROM Students s
JOIN Enrollments e ON s.StudentID = e.StudentID
WHERE e.CourseID IN (101, 102);
```
> **Observation:** Uses `DISTINCT` and the `IN` operator to prevent duplication when students meet both enrollment conditions.

---

### Task 7: Calculate Average Number of Credits for All Courses
```sql
SELECT AVG(Credits) AS AverageCredits
FROM Courses;
```
> **Observation:** Computed the mean credit load per course (3.4000) across all offerings.

---

### Task 8: Maximum Salary of Instructors in Computer Science Department
```sql
SELECT MAX(i.Salary) AS MaxCSSalary
FROM Instructors i
JOIN Departments d ON i.DepartmentID = d.DepartmentID
WHERE d.DepartmentName = 'Computer Science';
```
> **Observation:** Evaluates instructor compensation records linked to Computer Science and determines the maximum value ($85,000.00).

---

### Task 9: Count Students Enrolled in Each Department
```sql
SELECT 
    d.DepartmentID,
    d.DepartmentName,
    COUNT(DISTINCT e.StudentID) AS StudentCount
FROM Departments d
LEFT JOIN Courses c ON d.DepartmentID = c.DepartmentID
LEFT JOIN Enrollments e ON c.CourseID = e.CourseID
GROUP BY d.DepartmentID, d.DepartmentName;
```
> **Observation:** Utilizes multi-level `LEFT JOIN` and `COUNT(DISTINCT)` to preserve departments having zero active student enrollments.

---

### Task 10: INNER JOIN - Students and Their Corresponding Courses
```sql
SELECT 
    s.StudentID,
    s.FirstName,
    s.LastName,
    e.CourseID,
    e.EnrollmentDate
FROM Students s
INNER JOIN Enrollments e ON s.StudentID = e.StudentID;
```
> **Observation:** Returns only active combinations of registered students and their respective enrolled course codes.

---

### Task 11: LEFT JOIN - Retrieve All Students and Their Courses
```sql
SELECT 
    s.StudentID,
    s.FirstName,
    s.LastName,
    e.CourseID,
    e.EnrollmentDate
FROM Students s
LEFT JOIN Enrollments e ON s.StudentID = e.StudentID;
```
> **Observation:** Retains all student rows from the master table, even if an individual has not yet registered for any course.

---

### Task 12: Subquery - Students Enrolled in Courses with More Than 10 Students
```sql
SELECT DISTINCT 
    s.StudentID, 
    s.FirstName, 
    s.LastName
FROM Students s
JOIN Enrollments e ON s.StudentID = e.StudentID
WHERE e.CourseID IN (
    SELECT CourseID
    FROM Enrollments
    GROUP BY CourseID
    HAVING COUNT(StudentID) > 10
);
```
> **Observation:** Employs an uncorrelated subquery inside an `IN` clause to dynamically filter student enrollment in high-capacity classrooms.

---

### Task 13: Extract Year from Enrollment Date of Students
```sql
SELECT 
    StudentID,
    FirstName,
    LastName,
    EnrollmentDate,
    YEAR(EnrollmentDate) AS EnrollmentYear
FROM Students;
```
> **Observation:** Applies the built-in MySQL `YEAR()` function to parse numerical 4-digit years from `DATE` values.

---

### Task 14: Concatenate Instructor First and Last Name
```sql
SELECT 
    InstructorID,
    CONCAT(FirstName, ' ', LastName) AS FullName
FROM Instructors;
```
> **Observation:** Merges discrete string attributes into a clean, human-readable full name format.

---

### Task 15: Running Total of Students Enrolled in Courses (Window Function)
```sql
SELECT 
    c.CourseID,
    c.CourseName,
    COUNT(e.StudentID) AS StudentCount,
    SUM(COUNT(e.StudentID)) OVER (
        ORDER BY c.CourseID
    ) AS RunningTotal
FROM Courses c
LEFT JOIN Enrollments e ON c.CourseID = e.CourseID
GROUP BY c.CourseID, c.CourseName
ORDER BY c.CourseID;
```
> **Observation:** Leverages modern SQL windowing syntax (`SUM(...) OVER (ORDER BY ...)`) to construct cumulative enrollment totals across course records.

---

### Task 16: Dynamic Student Status Labeling (CASE Expression)
```sql
SELECT 
    StudentID,
    FirstName,
    LastName,
    EnrollmentDate,
    CASE
        WHEN EnrollmentDate < DATE_SUB(CURDATE(), INTERVAL 4 YEAR)
        THEN 'Senior'
        ELSE 'Junior'
    END AS StudentStatus
FROM Students;
```
> **Observation:** Computes active date duration relative to system `CURDATE()` and dynamically classifies student standing into `'Senior'` or `'Junior'`.

---

## 💻 How to Setup and Run in XAMPP / phpMyAdmin

1. **Start Services:** Open XAMPP Control Panel and start **Apache** and **MySQL**.
2. **Access phpMyAdmin:** Open your browser and navigate to `http://localhost/phpmyadmin/`.
3. **Create Database:**
   ```sql
   CREATE DATABASE IF NOT EXISTS final_project;
   USE final_project;
   ```
4. **Run Script:**
   - Go to the **SQL** tab in phpMyAdmin.
   - Paste the SQL script (`DDL`, `DML`, and query tasks) or import `university_management.sql`.
   - Click **Go** to execute.

---

## 🏁 Conclusion

The **University Course Management System** database satisfies all normalization and business logic requirements for enterprise academic operations. The successful execution and verification in phpMyAdmin confirms data integrity, optimal join execution, and robust analytical capabilities.