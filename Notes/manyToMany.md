### What is it?

A many-to-many relationship means:

Many instances of Entity A can be related to many instances of Entity B.

For example:
```
Student * ───────── * Course
```
Meaning:

One student can enroll in many courses.
One course can have many students.

Example:
```sql
Student Sajid
   ├── JavaScript
   ├── Python
   └── SQL


Student Ali
   ├── Python
   └── SQL

And:

Python
   ├── Sajid
   └── Ali
```
So the relationship exists in both directions.

### Why does it exist?

It exists because many real-world relationships naturally have many on both sides.

Examples:
```
Students * ─── * Courses
Doctors  * ─── * Patients
Products * ─── * Orders
Users    * ─── * Roles
Posts    * ─── * Tags
```
For example, a student can take multiple courses, while each course can have multiple students.

A 1-to-many relationship cannot correctly represent that situation.

### Who provides it?

Many-to-many is a data-modeling concept.

Relational database systems such as PostgreSQL, MySQL, SQL Server, etc. provide mechanisms to implement it.

The important thing is:

A relational database normally implements M:N using an intermediate/junction table.

MongoDB can also model many-to-many relationships, usually using arrays of IDs, references, or other modeling approaches.

### What problem does it solve?

It solves the problem of representing multiple relationships on both sides without duplicating data unnecessarily.

Suppose:
```sql
Student
---------
1 Sajid
2 Ali
```
and:
```sql
Course
---------
101 Python
102 JavaScript
103 SQL
```
Sajid takes:
```
Python
JavaScript
SQL
```
Ali takes:
```
Python
SQL
```
We need to represent:
```
Sajid → Python
Sajid → JavaScript
Sajid → SQL
```
```
Ali → Python
Ali → SQL
```
That's exactly what M:N represents.

### How do I use it?

You normally create three tables.

Student
```sql
Student
---------
studentId PK
name
Course
Course
---------
courseId PK
name
Enrollment
```
This is the junction/intermediate table.
```sql
Enrollment
----------------
studentId FK
courseId FK
```
Now:
```
Student
   │
   │
   ↓
Enrollment
   ↑
   │
   │
Course
```
Or visually:
```
Student 1 ─── * Enrollment * ─── 1 Course
```
This is how the database represents:
```
Student * ───── * Course
```
### What are its important parts?

There are three major parts.

1. Entity A
Student
2. Entity B
Course
3. Junction table
Enrollment

The junction table contains the keys of both entities:
```sql
Enrollment
----------------
studentId FK
courseId FK
```
Often you make the combination unique:
```sql
UNIQUE(studentId, courseId)
```
This prevents:
```
Sajid → Python
Sajid → Python   ❌
```
from being stored twice.

### What goes in?

You insert records into the two entity tables:
```sql
Student


studentId | name
1         | Sajid
2         | Ali
Course
```
```sql
courseId | name
101      | Python
102      | JavaScript
103      | SQL
```
Then you insert the relationships into the junction table:
```sql
Enrollment


studentId | courseId
1         | 101
1         | 102
1         | 103
2         | 101
2         | 103
```
Read each row as:
```
Student 1 → Course 101
Student 1 → Course 102
Student 1 → Course 103
Student 2 → Course 101
Student 2 → Course 103
```
### What comes out?

When you query the relationship, you can get things like:

Courses taken by Sajid
```
Sajid
 ├── Python
 ├── JavaScript
 └── SQL
```
Or:
Students enrolled in Python
```
Python
 ├── Sajid
 └── Ali
```
So an M:N relationship lets you navigate in either direction.
```
Student → Courses
Course  → Students
```
### When should I use it?

Use many-to-many when:

One A can have many B, AND one B can have many A.

For example:

Students and courses
```
Student * ─── * Course
```
Products and categories

If a product can belong to multiple categories and a category can contain multiple products:
```
Product * ─── * Category
Posts and tags
Post * ─── * Tag
```
A post can have:
```
JavaScript
React
Backend
```
while each tag can belong to many posts.

### How does it work internally?

This is the most important part.

Suppose:
```
Student
---------
1 Sajid
2 Ali
```
and:
```
Course
---------
101 Python
102 JavaScript
103 SQL
```
The database doesn't directly store:
```
Student * ───── * Course
```
Instead, it creates the junction table:
```sql
Enrollment
----------------
studentId | courseId
1         | 101
1         | 102
1         | 103
2         | 101
2         | 103
```
Think of it like this:
```
                Enrollment
              ┌─────────────┐
Student 1 ───→│ 1     101   │←── Course 101
Student 1 ───→│ 1     102   │←── Course 102
Student 1 ───→│ 1     103   │←── Course 103
Student 2 ───→│ 2     101   │←── Course 101
Student 2 ───→│ 2     103   │←── Course 103
              └─────────────┘
```
Each row in Enrollment represents one relationship.

So:

(1, 101)

means:

Student 1 is related to Course 101.

And:

(1, 102)

means:

Student 1 is related to Course 102.

The key idea

You can reduce the entire concept to:
```
Student * ───── * Course
```
becomes:
```
Student 1 ─── * Enrollment * ─── 1 Course
```
The junction table converts the many-to-many relationship into two one-to-many relationships.
```
Student 1 ─── * Enrollment
Course  1 ─── * Enrollment
```
This is the most important thing to remember:

Many-to-many → create a junction table containing the keys of both entities.
