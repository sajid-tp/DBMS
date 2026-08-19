### What is it?

A 1-to-many relationship means:

One instance of Entity A can be related to many instances of Entity B, while each instance of Entity B is related to one instance of Entity A.

Example:
```
User 1 ─────────── * Order
```
Meaning:

One User can have many Orders.
Each Order belongs to one User.

Example:
```
User 101
   │
   ├── Order 501
   ├── Order 502
   └── Order 503
```
### Why does it exist?

It exists because in the real world, one entity often needs to be associated with multiple instances of another entity.

For example:
```
Customer → Orders
Teacher → Students
Company → Employees
Category → Products
```
A customer doesn't normally have just one order. They can place many orders.

So the data model needs a way to represent:

One Customer → Many Orders
### Who provides it?

1-to-many is a data-modeling concept, not something invented specifically by one database.

Database systems such as:

- PostgreSQL
- MySQL
- SQL Server
- Oracle

provide mechanisms such as foreign keys and constraints to implement it.

MongoDB can also represent 1-to-many relationships, but it doesn't enforce relationships in exactly the same way relational databases do.

### What problem does it solve?

It solves the problem of representing repeated relationships without duplicating the parent entity.

Imagine:
```
User
---------
id = 101
name = Sajid
```
and Sajid has three orders.

Instead of doing:
```sql
Order
---------------------
userName | orderId
Sajid    | 501
Sajid    | 502
Sajid    | 503
```
we use an identifier:
```sql
Order
----------------
orderId | userId
501     | 101
502     | 101
503     | 101
```
Now all three orders point to the same user.

### How do I use it?

First identify the two entities.

For example:
```
User
Order
```
Then determine the cardinality:
```
User 1 ───── * Order
```
Then place the foreign key on the many side.
```sql
Users
---------
userId  PK
name
Orders
---------
orderId PK
userId  FK
amount
```
The Orders.userId points to Users.userId.

That's the normal relational implementation.

### What are its important parts?

There are several important parts.

- Parent entity
User
- Child entity
Order
- Primary key

Uniquely identifies each instance.
```
Users.userId
Orders.orderId
```

- Foreign key

Connects the child to the parent.
```
Orders.userId
```
references:
```
Users.userId
```
- Cardinality

Defines how many can be related:
```
1 : N
```
### What goes in?

You put instances/records of both entities into the database.

For example:
```sql
Users


userId | name
101    | Sajid
102    | Ali
```
And:
```sql
Orders


orderId | userId | amount
501     | 101    | 500
502     | 101    | 700
503     | 101    | 300
504     | 102    | 900
```
Notice:

101

appears multiple times in Orders.

That's allowed because Orders.userId is on the many side.

### What comes out?

When you query the relationship, you can get:
```
Sajid
 ├── Order 501 → ₹500
 ├── Order 502 → ₹700
 └── Order 503 → ₹300
```
So the relationship lets you retrieve:

One parent and all of its related children.

For example:
```
User 101
   ↓
Orders where userId = 101
```
Result:
501
502
503
### When should I use it?

Use 1-to-many when your business rule says:

One A can have many B, but each B belongs to one A.

Examples:
```
User 1 ───── * Orders
Category 1 ───── * Products
Company 1 ───── * Employees
Author 1 ───── * Books
Post 1 ───── * Comments
```
10. How does it work internally?

This is the most important part.

Suppose:
```sql
Users
----------------
userId | name
101    | Sajid
```
and:
```sql
Orders
-------------------------
orderId | userId | amount
501     | 101    | 500
502     | 101    | 700
503     | 101    | 300
```
The connection is:
```
Users.userId
     ↑
     │
Orders.userId
```
More specifically:
```
Users
  101
   ↑
   │
   ├──── Orders 501
   ├──── Orders 502
   └──── Orders 503
```
The database sees that each Orders.userId references the Users.userId.

Why can 101 appear multiple times?

Because we're on the many side.
```
Orders.userId
```
```
101
101
101
102
```
is perfectly valid.

But:
```
Users.userId
```
```
101
101   ❌
102
```
is not valid because Users.userId is the primary key and must be unique.

The core idea

You can reduce the entire concept to this:
```
1-to-many
```
means:

One parent can have many children.

And the implementation rule in a relational database is usually:
```
ONE SIDE                  MANY SIDE
```
```sql
User                      Order
-----                     -----
userId PK  ←────────────  userId FK
                          orderId PK
```
The foreign key goes on the MANY side.

That's one of the most important rules to remember:

1-to-many → put the foreign key in the table on the many side.
