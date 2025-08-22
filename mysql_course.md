วิชาการจัดการฐานข้อมูล
อ.โซฟีร์ หะยียูโซ๊ะ 
มหาวิทยาลัยราชภัฏยะลา

---

## **Week 1: พื้นฐาน MySQL และ DDL (Data Definition Language)**

### **หัวข้อที่ 1: แนะนำ MySQL และการติดตั้ง**
- ความรู้เบื้องต้นเกี่ยวกับฐานข้อมูล
- การติดตั้ง MySQL และ MySQL Workbench
- การเชื่อมต่อและการใช้งานเบื้องต้น
- ความเข้าใจเกี่ยวกับ Database, Table, Column, Row

### **หัวข้อที่ 2: DDL Commands - การสร้างและจัดการโครงสร้าง**

#### **CREATE Commands**
```sql
-- สร้างฐานข้อมูล
CREATE DATABASE school_db;
USE school_db;

-- สร้างตาราง
CREATE TABLE students (
    student_id INT PRIMARY KEY AUTO_INCREMENT,
    first_name VARCHAR(50) NOT NULL,
    last_name VARCHAR(50) NOT NULL,
    email VARCHAR(100) UNIQUE,
    birth_date DATE,
    enrollment_date TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);

CREATE TABLE courses (
    course_id INT PRIMARY KEY AUTO_INCREMENT,
    course_name VARCHAR(100) NOT NULL,
    credits INT DEFAULT 3,
    department VARCHAR(50)
);
```

#### **ALTER Commands**
```sql
-- เพิ่มคอลัมน์
ALTER TABLE students 
ADD COLUMN phone VARCHAR(15);

-- แก้ไขคอลัมน์
ALTER TABLE students 
MODIFY COLUMN email VARCHAR(150);

-- เปลี่ยนชื่อคอลัมน์
ALTER TABLE students 
CHANGE COLUMN birth_date date_of_birth DATE;

-- เพิ่ม Constraint
ALTER TABLE students 
ADD CONSTRAINT chk_email CHECK (email LIKE '%@%.%');
```

#### **DROP และ TRUNCATE Commands**
```sql
-- ลบคอลัมน์
ALTER TABLE students DROP COLUMN phone;

-- ลบตาราง
DROP TABLE IF EXISTS temp_table;

-- ลบข้อมูลทั้งหมดในตาราง
TRUNCATE TABLE students;
```

### **หัวข้อที่ 3: INDEX และ CONSTRAINT**
```sql
-- สร้าง Index
CREATE INDEX idx_student_email ON students(email);
CREATE INDEX idx_course_dept ON courses(department);

-- Foreign Key Constraint
CREATE TABLE enrollments (
    enrollment_id INT PRIMARY KEY AUTO_INCREMENT,
    student_id INT,
    course_id INT,
    enrollment_date DATE,
    grade CHAR(2),
    FOREIGN KEY (student_id) REFERENCES students(student_id) ON DELETE CASCADE,
    FOREIGN KEY (course_id) REFERENCES courses(course_id) ON DELETE CASCADE
);
```

**แบบฝึกหัด Week 1:**
1. สร้างฐานข้อมูล library_db
2. สร้างตาราง books, authors, publishers
3. เพิ่มและแก้ไข constraints ต่างๆ
4. สร้าง indexes ที่เหมาะสม

---

## **Week 2: DML (Data Manipulation Language) และการใช้ WHERE**

### **หัวข้อที่ 1: INSERT Commands**

#### **INSERT พื้นฐาน**
```sql
-- Insert แบบระบุคอลัมน์
INSERT INTO students (first_name, last_name, email, date_of_birth)
VALUES ('สมชาย', 'ใจดี', 'somchai@email.com', '2000-05-15');

-- Insert หลายแถวพร้อมกัน
INSERT INTO students (first_name, last_name, email, date_of_birth)
VALUES 
    ('สมหญิง', 'รักดี', 'somying@email.com', '1999-08-20'),
    ('วิชัย', 'มั่นคง', 'wichai@email.com', '2001-03-10'),
    ('นิรนาม', 'สมบูรณ์', 'nirnam@email.com', '2000-12-25');

-- Insert จากตารางอื่น
INSERT INTO backup_students 
SELECT * FROM students WHERE enrollment_date < '2023-01-01';
```

### **หัวข้อที่ 2: UPDATE และ DELETE Commands**

#### **UPDATE Commands**
```sql
-- Update แถวเดียว
UPDATE students 
SET email = 'new_somchai@email.com' 
WHERE student_id = 1;

-- Update หลายคอลัมน์
UPDATE students 
SET first_name = 'สมชาย', 
    last_name = 'ใหม่',
    email = 'somchai_new@email.com'
WHERE student_id = 1;

-- Update ด้วย JOIN
UPDATE enrollments e
JOIN courses c ON e.course_id = c.course_id
SET e.grade = 'A'
WHERE c.course_name = 'Database Systems' AND e.student_id = 1;

-- Update ด้วย Subquery
UPDATE students 
SET email = CONCAT(LOWER(first_name), '@school.ac.th')
WHERE student_id IN (SELECT student_id FROM enrollments WHERE grade IS NULL);
```

#### **DELETE Commands**
```sql
-- Delete แถวเดียว
DELETE FROM students WHERE student_id = 5;

-- Delete ด้วยเงื่อนไข
DELETE FROM enrollments WHERE grade = 'F';

-- Delete ด้วย JOIN
DELETE e FROM enrollments e
JOIN courses c ON e.course_id = c.course_id
WHERE c.department = 'Old Department';

-- Delete ด้วย Subquery
DELETE FROM students 
WHERE student_id NOT IN (SELECT DISTINCT student_id FROM enrollments);
```

### **หัวข้อที่ 3: SELECT พื้นฐาน**
```sql
-- SELECT พื้นฐาน
SELECT * FROM students;
SELECT first_name, last_name, email FROM students;

-- WHERE Clause
SELECT * FROM students WHERE date_of_birth > '2000-01-01';
SELECT * FROM students WHERE first_name LIKE 'ส%';

-- ORDER BY และ LIMIT
SELECT * FROM students ORDER BY last_name, first_name;
SELECT * FROM students ORDER BY enrollment_date DESC LIMIT 5;

-- Aggregate Functions
SELECT COUNT(*) as total_students FROM students;
SELECT AVG(credits) as avg_credits FROM courses;
SELECT department, COUNT(*) as course_count 
FROM courses 
GROUP BY department
HAVING COUNT(*) > 2;
```

### **หัวข้อที่ 4: การใช้ WHERE Clause แบบละเอียด**

#### **WHERE พื้นฐาน**
```sql
-- เงื่อนไขพื้นฐาน
SELECT * FROM students WHERE student_id = 1;
SELECT * FROM students WHERE first_name = 'สมชาย';
SELECT * FROM courses WHERE credits > 3;

-- การเปรียบเทียบตัวเลข
SELECT * FROM students WHERE student_id >= 10 AND student_id <= 20;
SELECT * FROM courses WHERE credits BETWEEN 2 AND 4;
SELECT * FROM students WHERE date_of_birth > '2000-01-01';
```

#### **WHERE กับ String Operations**
```sql
-- LIKE Pattern Matching
SELECT * FROM students WHERE first_name LIKE 'ส%';          -- เริ่มต้นด้วย "ส"
SELECT * FROM students WHERE first_name LIKE '%ชาย';        -- ลงท้ายด้วย "ชาย"
SELECT * FROM students WHERE first_name LIKE '%ม%';         -- มีตัวอักษร "ม"
SELECT * FROM students WHERE first_name LIKE 'ส_ม%';        -- รูปแบบเฉพาะ

-- การใช้ REGEXP (Regular Expression)
SELECT * FROM students WHERE first_name REGEXP '^[ก-ฮ]{3,}
1. เพิ่มข้อมูลนักเรียน 50 คน
2. อัปเดตข้อมูลนักเรียนที่มีเงื่อนไขต่างๆ ด้วย WHERE
3. ลบข้อมูลที่ไม่จำเป็น โดยใช้เงื่อนไข WHERE ที่ซับซ้อน
4. สร้างรายงานสถิติต่างๆ ด้วย WHERE และ GROUP BY
5. ฝึกใช้ WHERE กับ Pattern Matching และ Date Functions

---

## **Week 3: JOIN Operations**

### **หัวข้อที่ 1: INNER JOIN และ LEFT JOIN**

#### **INNER JOIN**
```sql
-- JOIN พื้นฐาน
SELECT s.first_name, s.last_name, c.course_name, e.grade
FROM students s
INNER JOIN enrollments e ON s.student_id = e.student_id
INNER JOIN courses c ON e.course_id = c.course_id;

-- Multiple INNER JOINs
SELECT s.first_name, s.last_name, c.course_name, c.credits, e.grade
FROM students s
INNER JOIN enrollments e ON s.student_id = e.student_id
INNER JOIN courses c ON e.course_id = c.course_id
WHERE e.grade IN ('A', 'B+');
```

#### **LEFT JOIN (LEFT OUTER JOIN)**
```sql
-- แสดงนักเรียนทั้งหมด รวมทั้งที่ยังไม่ได้ลงทะเบียน
SELECT s.first_name, s.last_name, c.course_name, e.grade
FROM students s
LEFT JOIN enrollments e ON s.student_id = e.student_id
LEFT JOIN courses c ON e.course_id = c.course_id;

-- หานักเรียนที่ยังไม่ได้ลงทะเบียนเรียน
SELECT s.student_id, s.first_name, s.last_name
FROM students s
LEFT JOIN enrollments e ON s.student_id = e.student_id
WHERE e.student_id IS NULL;
```

### **หัวข้อที่ 2: RIGHT JOIN และ FULL OUTER JOIN**

#### **RIGHT JOIN (RIGHT OUTER JOIN)**
```sql
-- แสดงคอร์สทั้งหมด รวมทั้งที่ยังไม่มีนักเรียนลงทะเบียน
SELECT s.first_name, s.last_name, c.course_name, c.credits
FROM students s
RIGHT JOIN enrollments e ON s.student_id = e.student_id
RIGHT JOIN courses c ON e.course_id = c.course_id;

-- หาคอร์สที่ยังไม่มีนักเรียนลงทะเบียน
SELECT c.course_id, c.course_name, c.department
FROM enrollments e
RIGHT JOIN courses c ON e.course_id = c.course_id
WHERE e.course_id IS NULL;
```

#### **FULL OUTER JOIN (ใน MySQL ใช้ UNION)**
```sql
-- แสดงข้อมูลทั้งหมด ทั้งที่มีและไม่มีการ match
SELECT s.first_name, s.last_name, c.course_name
FROM students s
LEFT JOIN enrollments e ON s.student_id = e.student_id
LEFT JOIN courses c ON e.course_id = c.course_id

UNION

SELECT s.first_name, s.last_name, c.course_name
FROM students s
RIGHT JOIN enrollments e ON s.student_id = e.student_id
RIGHT JOIN courses c ON e.course_id = c.course_id;
```

### **หัวข้อที่ 3: CROSS JOIN และ SELF JOIN**

#### **CROSS JOIN**
```sql
-- Cartesian Product
SELECT s.first_name, s.last_name, c.course_name
FROM students s
CROSS JOIN courses c
WHERE s.student_id <= 3 AND c.course_id <= 3;
```

#### **SELF JOIN**
```sql
-- สร้างตารางพนักงานเพื่อตัวอย่าง
CREATE TABLE employees (
    emp_id INT PRIMARY KEY,
    first_name VARCHAR(50),
    last_name VARCHAR(50),
    manager_id INT
);

-- หาพนักงานและผู้จัดการ
SELECT e.first_name AS employee_name, 
       m.first_name AS manager_name
FROM employees e
LEFT JOIN employees m ON e.manager_id = m.emp_id;
```

### **หัวข้อที่ 4: Advanced JOIN Techniques**

#### **JOIN ด้วย Multiple Conditions**
```sql
-- JOIN ด้วยเงื่อนไขหลายตัว
SELECT s.first_name, s.last_name, c.course_name, e.grade
FROM students s
INNER JOIN enrollments e ON s.student_id = e.student_id 
    AND e.enrollment_date > '2023-01-01'
INNER JOIN courses c ON e.course_id = c.course_id 
    AND c.credits >= 3;
```

#### **Subqueries ใน JOIN**
```sql
-- ใช้ subquery ใน FROM clause
SELECT avg_grade.department, avg_grade.average_grade
FROM (
    SELECT c.department, AVG(
        CASE 
            WHEN e.grade = 'A' THEN 4.0
            WHEN e.grade = 'B+' THEN 3.5
            WHEN e.grade = 'B' THEN 3.0
            WHEN e.grade = 'C+' THEN 2.5
            WHEN e.grade = 'C' THEN 2.0
            ELSE 1.0
        END
    ) as average_grade
    FROM courses c
    JOIN enrollments e ON c.course_id = e.course_id
    GROUP BY c.department
) as avg_grade
WHERE avg_grade.average_grade > 3.0;
```

### **หัวข้อที่ 5: การใช้ JOIN ในงานจริง**

#### **รายงานที่ซับซ้อน**
```sql
-- รายงานผลการเรียนของนักเรียนแต่ละคน
SELECT 
    s.student_id,
    CONCAT(s.first_name, ' ', s.last_name) as student_name,
    COUNT(e.course_id) as total_courses,
    AVG(CASE 
        WHEN e.grade = 'A' THEN 4.0
        WHEN e.grade = 'B+' THEN 3.5
        WHEN e.grade = 'B' THEN 3.0
        WHEN e.grade = 'C+' THEN 2.5
        WHEN e.grade = 'C' THEN 2.0
        ELSE 1.0
    END) as gpa,
    SUM(c.credits) as total_credits
FROM students s
LEFT JOIN enrollments e ON s.student_id = e.student_id
LEFT JOIN courses c ON e.course_id = c.course_id
GROUP BY s.student_id, s.first_name, s.last_name
ORDER BY gpa DESC;

-- รายงานคอร์สที่ได้รับความนิยม
SELECT 
    c.course_name,
    c.department,
    COUNT(e.student_id) as enrollment_count,
    AVG(CASE 
        WHEN e.grade = 'A' THEN 4.0
        WHEN e.grade = 'B+' THEN 3.5
        WHEN e.grade = 'B' THEN 3.0
        WHEN e.grade = 'C+' THEN 2.5
        WHEN e.grade = 'C' THEN 2.0
        ELSE 1.0
    END) as average_grade
FROM courses c
JOIN enrollments e ON c.course_id = e.course_id
GROUP BY c.course_id, c.course_name, c.department
HAVING enrollment_count >= 5
ORDER BY enrollment_count DESC, average_grade DESC;
```

**แบบฝึกหัด Week 3:**
1. สร้างรายงานนักเรียนดีเด่น (GPA > 3.5)
2. หาคอร์สที่ยังไม่มีนักเรียนลงทะเบียน
3. สร้างรายงานเปรียบเทียบผลการเรียนแต่ละภาค
4. วิเคราะห์แนวโน้มการลงทะเบียนเรียน

---

## **โปรเจกต์สุดท้าย: ระบบจัดการห้องสมุด**

สร้างฐานข้อมูลห้องสมุดที่สมบูรณ์ ประกอบด้วย:

1. **ตารางหลัก:** books, authors, publishers, members, borrowings
2. **ฟีเจอร์ที่ต้องมี:**
   - ระบบยืม-คืนหนังสือ
   - รายงานหนังสือยอดนิยม
   - ติดตามสมาชิกที่ค้างคืน
   - สถิติการใช้งานห้องสมุด

```sql
-- ตัวอย่างโครงสร้างตาราง
CREATE TABLE books (
    book_id INT PRIMARY KEY AUTO_INCREMENT,
    title VARCHAR(200) NOT NULL,
    isbn VARCHAR(20) UNIQUE,
    publication_year YEAR,
    copies_available INT DEFAULT 1,
    total_copies INT DEFAULT 1,
    author_id INT,
    publisher_id INT,
    FOREIGN KEY (author_id) REFERENCES authors(author_id),
    FOREIGN KEY (publisher_id) REFERENCES publishers(publisher_id)
);
```

---

## **การประเมินผล**

### **Week 1:** แบบทดสอบ DDL (25%)
### **Week 2:** แบบทดสอบ DML และ WHERE (25%)
### **Week 3:** แบบทดสอบ JOIN (25%)
### **โปรเจกต์สุดท้าย:** ระบบจัดการห้องสมุด (25%)

---

## **เอกสารอ้างอิง**
- MySQL Official Documentation
- W3Schools MySQL Tutorial
- MySQL Workbook exercises
- Practice datasets สำหรับฝึกหัด;
SELECT * FROM students WHERE email REGEXP '^[a-zA-Z0-9._%+-]+@[a-zA-Z0-9.-]+\.[a-zA-Z]{2,}
1. เพิ่มข้อมูลนักเรียน 50 คน
2. อัปเดตข้อมูลนักเรียนที่มีเงื่อนไขต่างๆ
3. ลบข้อมูลที่ไม่จำเป็น
4. สร้างรายงานสถิติต่างๆ

---

## **สัปดาห์ที่ 3: JOIN Operations**

### **วันที่ 1: INNER JOIN และ LEFT JOIN**

#### **INNER JOIN**
```sql
-- JOIN พื้นฐาน
SELECT s.first_name, s.last_name, c.course_name, e.grade
FROM students s
INNER JOIN enrollments e ON s.student_id = e.student_id
INNER JOIN courses c ON e.course_id = c.course_id;

-- Multiple INNER JOINs
SELECT s.first_name, s.last_name, c.course_name, c.credits, e.grade
FROM students s
INNER JOIN enrollments e ON s.student_id = e.student_id
INNER JOIN courses c ON e.course_id = c.course_id
WHERE e.grade IN ('A', 'B+');
```

#### **LEFT JOIN (LEFT OUTER JOIN)**
```sql
-- แสดงนักเรียนทั้งหมด รวมทั้งที่ยังไม่ได้ลงทะเบียน
SELECT s.first_name, s.last_name, c.course_name, e.grade
FROM students s
LEFT JOIN enrollments e ON s.student_id = e.student_id
LEFT JOIN courses c ON e.course_id = c.course_id;

-- หานักเรียนที่ยังไม่ได้ลงทะเบียนเรียน
SELECT s.student_id, s.first_name, s.last_name
FROM students s
LEFT JOIN enrollments e ON s.student_id = e.student_id
WHERE e.student_id IS NULL;
```

### **วันที่ 2: RIGHT JOIN และ FULL OUTER JOIN**

#### **RIGHT JOIN (RIGHT OUTER JOIN)**
```sql
-- แสดงคอร์สทั้งหมด รวมทั้งที่ยังไม่มีนักเรียนลงทะเบียน
SELECT s.first_name, s.last_name, c.course_name, c.credits
FROM students s
RIGHT JOIN enrollments e ON s.student_id = e.student_id
RIGHT JOIN courses c ON e.course_id = c.course_id;

-- หาคอร์สที่ยังไม่มีนักเรียนลงทะเบียน
SELECT c.course_id, c.course_name, c.department
FROM enrollments e
RIGHT JOIN courses c ON e.course_id = c.course_id
WHERE e.course_id IS NULL;
```

#### **FULL OUTER JOIN (ใน MySQL ใช้ UNION)**
```sql
-- แสดงข้อมูลทั้งหมด ทั้งที่มีและไม่มีการ match
SELECT s.first_name, s.last_name, c.course_name
FROM students s
LEFT JOIN enrollments e ON s.student_id = e.student_id
LEFT JOIN courses c ON e.course_id = c.course_id

UNION

SELECT s.first_name, s.last_name, c.course_name
FROM students s
RIGHT JOIN enrollments e ON s.student_id = e.student_id
RIGHT JOIN courses c ON e.course_id = c.course_id;
```

### **วันที่ 3: CROSS JOIN และ SELF JOIN**

#### **CROSS JOIN**
```sql
-- Cartesian Product
SELECT s.first_name, s.last_name, c.course_name
FROM students s
CROSS JOIN courses c
WHERE s.student_id <= 3 AND c.course_id <= 3;
```

#### **SELF JOIN**
```sql
-- สร้างตารางพนักงานเพื่อตัวอย่าง
CREATE TABLE employees (
    emp_id INT PRIMARY KEY,
    first_name VARCHAR(50),
    last_name VARCHAR(50),
    manager_id INT
);

-- หาพนักงานและผู้จัดการ
SELECT e.first_name AS employee_name, 
       m.first_name AS manager_name
FROM employees e
LEFT JOIN employees m ON e.manager_id = m.emp_id;
```

### **วันที่ 4: Advanced JOIN Techniques**

#### **JOIN ด้วย Multiple Conditions**
```sql
-- JOIN ด้วยเงื่อนไขหลายตัว
SELECT s.first_name, s.last_name, c.course_name, e.grade
FROM students s
INNER JOIN enrollments e ON s.student_id = e.student_id 
    AND e.enrollment_date > '2023-01-01'
INNER JOIN courses c ON e.course_id = c.course_id 
    AND c.credits >= 3;
```

#### **Subqueries ใน JOIN**
```sql
-- ใช้ subquery ใน FROM clause
SELECT avg_grade.department, avg_grade.average_grade
FROM (
    SELECT c.department, AVG(
        CASE 
            WHEN e.grade = 'A' THEN 4.0
            WHEN e.grade = 'B+' THEN 3.5
            WHEN e.grade = 'B' THEN 3.0
            WHEN e.grade = 'C+' THEN 2.5
            WHEN e.grade = 'C' THEN 2.0
            ELSE 1.0
        END
    ) as average_grade
    FROM courses c
    JOIN enrollments e ON c.course_id = e.course_id
    GROUP BY c.department
) as avg_grade
WHERE avg_grade.average_grade > 3.0;
```

### **วันที่ 5: การใช้ JOIN ในงานจริง**

#### **รายงานที่ซับซ้อน**
```sql
-- รายงานผลการเรียนของนักเรียนแต่ละคน
SELECT 
    s.student_id,
    CONCAT(s.first_name, ' ', s.last_name) as student_name,
    COUNT(e.course_id) as total_courses,
    AVG(CASE 
        WHEN e.grade = 'A' THEN 4.0
        WHEN e.grade = 'B+' THEN 3.5
        WHEN e.grade = 'B' THEN 3.0
        WHEN e.grade = 'C+' THEN 2.5
        WHEN e.grade = 'C' THEN 2.0
        ELSE 1.0
    END) as gpa,
    SUM(c.credits) as total_credits
FROM students s
LEFT JOIN enrollments e ON s.student_id = e.student_id
LEFT JOIN courses c ON e.course_id = c.course_id
GROUP BY s.student_id, s.first_name, s.last_name
ORDER BY gpa DESC;

-- รายงานคอร์สที่ได้รับความนิยม
SELECT 
    c.course_name,
    c.department,
    COUNT(e.student_id) as enrollment_count,
    AVG(CASE 
        WHEN e.grade = 'A' THEN 4.0
        WHEN e.grade = 'B+' THEN 3.5
        WHEN e.grade = 'B' THEN 3.0
        WHEN e.grade = 'C+' THEN 2.5
        WHEN e.grade = 'C' THEN 2.0
        ELSE 1.0
    END) as average_grade
FROM courses c
JOIN enrollments e ON c.course_id = e.course_id
GROUP BY c.course_id, c.course_name, c.department
HAVING enrollment_count >= 5
ORDER BY enrollment_count DESC, average_grade DESC;
```

**แบบฝึกหัดสัปดาห์ที่ 3:**
1. สร้างรายงานนักเรียนดีเด่น (GPA > 3.5)
2. หาคอร์สที่ยังไม่มีนักเรียนลงทะเบียน
3. สร้างรายงานเปรียบเทียบผลการเรียนแต่ละภาค
4. วิเคราะห์แนวโน้มการลงทะเบียนเรียน

---

## **โปรเจกต์สุดท้าย: ระบบจัดการห้องสมุด**

สร้างฐานข้อมูลห้องสมุดที่สมบูรณ์ ประกอบด้วย:

1. **ตารางหลัก:** books, authors, publishers, members, borrowings
2. **ฟีเจอร์ที่ต้องมี:**
   - ระบบยืม-คืนหนังสือ
   - รายงานหนังสือยอดนิยม
   - ติดตามสมาชิกที่ค้างคืน
   - สถิติการใช้งานห้องสมุด

```sql
-- ตัวอย่างโครงสร้างตาราง
CREATE TABLE books (
    book_id INT PRIMARY KEY AUTO_INCREMENT,
    title VARCHAR(200) NOT NULL,
    isbn VARCHAR(20) UNIQUE,
    publication_year YEAR,
    copies_available INT DEFAULT 1,
    total_copies INT DEFAULT 1,
    author_id INT,
    publisher_id INT,
    FOREIGN KEY (author_id) REFERENCES authors(author_id),
    FOREIGN KEY (publisher_id) REFERENCES publishers(publisher_id)
);
```

---

## **การประเมินผล**

### **สัปดาห์ที่ 1:** แบบทดสอบ DDL (25%)
### **สัปดาห์ที่ 2:** แบบทดสอบ DML (25%)
### **สัปดาห์ที่ 3:** แบบทดสอบ JOIN (25%)
### **โปรเจกต์สุดท้าย:** ระบบจัดการห้องสมุด (25%)

---

## **เอกสารอ้างอิง**
- MySQL Official Documentation
- W3Schools MySQL Tutorial
- MySQL Workbook exercises
- Practice datasets สำหรับฝึกหัด;

-- Case Sensitive vs Insensitive
SELECT * FROM students WHERE first_name = 'SOMCHAI' COLLATE utf8mb4_bin;
SELECT * FROM students WHERE BINARY first_name = 'somchai';
```

#### **WHERE กับ Logical Operators**
```sql
-- AND, OR, NOT
SELECT * FROM students 
WHERE first_name = 'สมชาย' AND date_of_birth > '2000-01-01';

SELECT * FROM students 
WHERE first_name = 'สมชาย' OR first_name = 'สมหญิง';

SELECT * FROM students 
WHERE NOT (date_of_birth < '1990-01-01');

-- การใช้วงเล็บจัดกลุ่มเงื่อนไข
SELECT * FROM students 
WHERE (first_name = 'สมชาย' OR first_name = 'สมหญิng') 
  AND date_of_birth BETWEEN '1999-01-01' AND '2001-12-31';
```

#### **WHERE กับ NULL Values**
```sql
-- ตรวจสอบ NULL
SELECT * FROM students WHERE email IS NULL;
SELECT * FROM students WHERE email IS NOT NULL;

-- COALESCE และ IFNULL
SELECT first_name, COALESCE(email, 'ไม่มีอีเมล') as email_status 
FROM students;

SELECT first_name, IFNULL(email, 'No Email') as email_info 
FROM students;
```

#### **WHERE กับ IN และ EXISTS**
```sql
-- IN Operator
SELECT * FROM students 
WHERE student_id IN (1, 3, 5, 7, 9);

SELECT * FROM courses 
WHERE department IN ('Computer Science', 'Mathematics', 'Physics');

-- EXISTS Subquery
SELECT * FROM students s
WHERE EXISTS (
    SELECT 1 FROM enrollments e 
    WHERE e.student_id = s.student_id
);

-- NOT EXISTS
SELECT * FROM students s
WHERE NOT EXISTS (
    SELECT 1 FROM enrollments e 
    WHERE e.student_id = s.student_id
);
```

#### **WHERE กับ Date และ Time Functions**
```sql
-- Date Functions
SELECT * FROM students WHERE YEAR(date_of_birth) = 2000;
SELECT * FROM students WHERE MONTH(enrollment_date) = 9;
SELECT * FROM students WHERE DAY(date_of_birth) = 15;

-- Date Ranges
SELECT * FROM students 
WHERE date_of_birth BETWEEN '2000-01-01' AND '2000-12-31';

-- Current Date Functions
SELECT * FROM students 
WHERE enrollment_date >= CURDATE() - INTERVAL 30 DAY;

SELECT * FROM students 
WHERE TIMESTAMPDIFF(YEAR, date_of_birth, CURDATE()) >= 18;
```

#### **WHERE กับ Mathematical Operations**
```sql
-- คำนวณในเงื่อนไข
SELECT * FROM courses WHERE credits * 15 > 45;  -- คิดชั่วโมงเรียน

-- การใช้ MOD
SELECT * FROM students WHERE student_id % 2 = 0;  -- student_id เลขคู่

-- การใช้ ABS, ROUND
SELECT * FROM enrollments 
WHERE ABS(DATEDIFF(enrollment_date, '2024-09-01')) <= 7;
```

#### **WHERE แบบ Advanced**
```sql
-- Subquery ใน WHERE
SELECT * FROM students 
WHERE student_id IN (
    SELECT student_id FROM enrollments 
    WHERE grade IN ('A', 'B+')
    GROUP BY student_id 
    HAVING COUNT(*) >= 3
);

-- CASE ใน WHERE
SELECT * FROM students
WHERE CASE 
    WHEN YEAR(CURDATE()) - YEAR(date_of_birth) < 20 THEN 'Young'
    WHEN YEAR(CURDATE()) - YEAR(date_of_birth) < 25 THEN 'Adult'
    ELSE 'Older'
END = 'Young';

-- การใช้ WHERE กับ Aggregate Functions (ต้องใช้ HAVING)
SELECT department, COUNT(*) as course_count
FROM courses 
GROUP BY department
HAVING COUNT(*) > 5;  -- ใช้ HAVING แทน WHERE สำหรับ aggregate
```

**แบบฝึกหัด Week 2:**
1. เพิ่มข้อมูลนักเรียน 50 คน
2. อัปเดตข้อมูลนักเรียนที่มีเงื่อนไขต่างๆ
3. ลบข้อมูลที่ไม่จำเป็น
4. สร้างรายงานสถิติต่างๆ

---

## **สัปดาห์ที่ 3: JOIN Operations**

### **วันที่ 1: INNER JOIN และ LEFT JOIN**

#### **INNER JOIN**
```sql
-- JOIN พื้นฐาน
SELECT s.first_name, s.last_name, c.course_name, e.grade
FROM students s
INNER JOIN enrollments e ON s.student_id = e.student_id
INNER JOIN courses c ON e.course_id = c.course_id;

-- Multiple INNER JOINs
SELECT s.first_name, s.last_name, c.course_name, c.credits, e.grade
FROM students s
INNER JOIN enrollments e ON s.student_id = e.student_id
INNER JOIN courses c ON e.course_id = c.course_id
WHERE e.grade IN ('A', 'B+');
```

#### **LEFT JOIN (LEFT OUTER JOIN)**
```sql
-- แสดงนักเรียนทั้งหมด รวมทั้งที่ยังไม่ได้ลงทะเบียน
SELECT s.first_name, s.last_name, c.course_name, e.grade
FROM students s
LEFT JOIN enrollments e ON s.student_id = e.student_id
LEFT JOIN courses c ON e.course_id = c.course_id;

-- หานักเรียนที่ยังไม่ได้ลงทะเบียนเรียน
SELECT s.student_id, s.first_name, s.last_name
FROM students s
LEFT JOIN enrollments e ON s.student_id = e.student_id
WHERE e.student_id IS NULL;
```

### **วันที่ 2: RIGHT JOIN และ FULL OUTER JOIN**

#### **RIGHT JOIN (RIGHT OUTER JOIN)**
```sql
-- แสดงคอร์สทั้งหมด รวมทั้งที่ยังไม่มีนักเรียนลงทะเบียน
SELECT s.first_name, s.last_name, c.course_name, c.credits
FROM students s
RIGHT JOIN enrollments e ON s.student_id = e.student_id
RIGHT JOIN courses c ON e.course_id = c.course_id;

-- หาคอร์สที่ยังไม่มีนักเรียนลงทะเบียน
SELECT c.course_id, c.course_name, c.department
FROM enrollments e
RIGHT JOIN courses c ON e.course_id = c.course_id
WHERE e.course_id IS NULL;
```

#### **FULL OUTER JOIN (ใน MySQL ใช้ UNION)**
```sql
-- แสดงข้อมูลทั้งหมด ทั้งที่มีและไม่มีการ match
SELECT s.first_name, s.last_name, c.course_name
FROM students s
LEFT JOIN enrollments e ON s.student_id = e.student_id
LEFT JOIN courses c ON e.course_id = c.course_id

UNION

SELECT s.first_name, s.last_name, c.course_name
FROM students s
RIGHT JOIN enrollments e ON s.student_id = e.student_id
RIGHT JOIN courses c ON e.course_id = c.course_id;
```

### **วันที่ 3: CROSS JOIN และ SELF JOIN**

#### **CROSS JOIN**
```sql
-- Cartesian Product
SELECT s.first_name, s.last_name, c.course_name
FROM students s
CROSS JOIN courses c
WHERE s.student_id <= 3 AND c.course_id <= 3;
```

#### **SELF JOIN**
```sql
-- สร้างตารางพนักงานเพื่อตัวอย่าง
CREATE TABLE employees (
    emp_id INT PRIMARY KEY,
    first_name VARCHAR(50),
    last_name VARCHAR(50),
    manager_id INT
);

-- หาพนักงานและผู้จัดการ
SELECT e.first_name AS employee_name, 
       m.first_name AS manager_name
FROM employees e
LEFT JOIN employees m ON e.manager_id = m.emp_id;
```

### **วันที่ 4: Advanced JOIN Techniques**

#### **JOIN ด้วย Multiple Conditions**
```sql
-- JOIN ด้วยเงื่อนไขหลายตัว
SELECT s.first_name, s.last_name, c.course_name, e.grade
FROM students s
INNER JOIN enrollments e ON s.student_id = e.student_id 
    AND e.enrollment_date > '2023-01-01'
INNER JOIN courses c ON e.course_id = c.course_id 
    AND c.credits >= 3;
```

#### **Subqueries ใน JOIN**
```sql
-- ใช้ subquery ใน FROM clause
SELECT avg_grade.department, avg_grade.average_grade
FROM (
    SELECT c.department, AVG(
        CASE 
            WHEN e.grade = 'A' THEN 4.0
            WHEN e.grade = 'B+' THEN 3.5
            WHEN e.grade = 'B' THEN 3.0
            WHEN e.grade = 'C+' THEN 2.5
            WHEN e.grade = 'C' THEN 2.0
            ELSE 1.0
        END
    ) as average_grade
    FROM courses c
    JOIN enrollments e ON c.course_id = e.course_id
    GROUP BY c.department
) as avg_grade
WHERE avg_grade.average_grade > 3.0;
```

### **วันที่ 5: การใช้ JOIN ในงานจริง**

#### **รายงานที่ซับซ้อน**
```sql
-- รายงานผลการเรียนของนักเรียนแต่ละคน
SELECT 
    s.student_id,
    CONCAT(s.first_name, ' ', s.last_name) as student_name,
    COUNT(e.course_id) as total_courses,
    AVG(CASE 
        WHEN e.grade = 'A' THEN 4.0
        WHEN e.grade = 'B+' THEN 3.5
        WHEN e.grade = 'B' THEN 3.0
        WHEN e.grade = 'C+' THEN 2.5
        WHEN e.grade = 'C' THEN 2.0
        ELSE 1.0
    END) as gpa,
    SUM(c.credits) as total_credits
FROM students s
LEFT JOIN enrollments e ON s.student_id = e.student_id
LEFT JOIN courses c ON e.course_id = c.course_id
GROUP BY s.student_id, s.first_name, s.last_name
ORDER BY gpa DESC;

-- รายงานคอร์สที่ได้รับความนิยม
SELECT 
    c.course_name,
    c.department,
    COUNT(e.student_id) as enrollment_count,
    AVG(CASE 
        WHEN e.grade = 'A' THEN 4.0
        WHEN e.grade = 'B+' THEN 3.5
        WHEN e.grade = 'B' THEN 3.0
        WHEN e.grade = 'C+' THEN 2.5
        WHEN e.grade = 'C' THEN 2.0
        ELSE 1.0
    END) as average_grade
FROM courses c
JOIN enrollments e ON c.course_id = e.course_id
GROUP BY c.course_id, c.course_name, c.department
HAVING enrollment_count >= 5
ORDER BY enrollment_count DESC, average_grade DESC;
```

**แบบฝึกหัดสัปดาห์ที่ 3:**
1. สร้างรายงานนักเรียนดีเด่น (GPA > 3.5)
2. หาคอร์สที่ยังไม่มีนักเรียนลงทะเบียน
3. สร้างรายงานเปรียบเทียบผลการเรียนแต่ละภาค
4. วิเคราะห์แนวโน้มการลงทะเบียนเรียน

---

## **โปรเจกต์สุดท้าย: ระบบจัดการห้องสมุด**

สร้างฐานข้อมูลห้องสมุดที่สมบูรณ์ ประกอบด้วย:

1. **ตารางหลัก:** books, authors, publishers, members, borrowings
2. **ฟีเจอร์ที่ต้องมี:**
   - ระบบยืม-คืนหนังสือ
   - รายงานหนังสือยอดนิยม
   - ติดตามสมาชิกที่ค้างคืน
   - สถิติการใช้งานห้องสมุด

```sql
-- ตัวอย่างโครงสร้างตาราง
CREATE TABLE books (
    book_id INT PRIMARY KEY AUTO_INCREMENT,
    title VARCHAR(200) NOT NULL,
    isbn VARCHAR(20) UNIQUE,
    publication_year YEAR,
    copies_available INT DEFAULT 1,
    total_copies INT DEFAULT 1,
    author_id INT,
    publisher_id INT,
    FOREIGN KEY (author_id) REFERENCES authors(author_id),
    FOREIGN KEY (publisher_id) REFERENCES publishers(publisher_id)
);
```

---

## **การประเมินผล**

### **สัปดาห์ที่ 1:** แบบทดสอบ DDL (25%)
### **สัปดาห์ที่ 2:** แบบทดสอบ DML (25%)
### **สัปดาห์ที่ 3:** แบบทดสอบ JOIN (25%)
### **โปรเจกต์สุดท้าย:** ระบบจัดการห้องสมุด (25%)

---

## **เอกสารอ้างอิง**
- MySQL Official Documentation
- W3Schools MySQL Tutorial
- MySQL Workbook exercises
- Practice datasets สำหรับฝึกหัด
