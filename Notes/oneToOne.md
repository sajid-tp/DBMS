### What is it?

A 1-to-1 relationship is a relationship between two entities where:

One instance of Entity A is associated with at most one instance of Entity B, and one instance of Entity B is associated with at most one instance of Entity A.

Example:
```
User ↔ UserProfile
```
One User has one UserProfile.
One UserProfile belongs to one User.

So:
```
User 1 ───────── 1 UserProfile
```
### Why does it exist?

It exists when you have two different types of information/entities that have a unique relationship with each other.

For example:
```
User
---------
user_id
name
email


UserProfile
------------
profile_id
bio
profile_picture
date_of_birth
```
You could put everything into User, but sometimes you want to separate the information because the two entities represent different concepts.

### Who provides it?

The database/data-modeling theory provides the concept of relationships such as:
```
1-to-1
1-to-many
many-to-many
```
A DBMS such as PostgreSQL, MySQL, MongoDB, etc. provides mechanisms to implement these relationships.

Important distinction:

1-to-1 is a data-modeling concept.
The database system provides the tools/constraints used to enforce it.

### What problem does it solve?

It solves situations where one entity should correspond to only one entity of another type.

For example:

Person ─── 1 Passport

A person shouldn't have two passports within this particular model if the business rule says one person can have only one passport.

Another example:

User ─── 1 UserProfile

You don't want:
```
User 101 → Profile 1
User 101 → Profile 2
```
if your business rule says every user can have only one profile.

### How do I use it?

First identify the two entities.

Example:

User
UserProfile

Then identify the relationship:

One User → One UserProfile
One UserProfile → One User

In a relational database, you normally enforce this using a foreign key + UNIQUE constraint.

For example:
```sql
User
---------
id (PK)
name


UserProfile
------------
id (PK)
user_id (FK, UNIQUE)
bio
```
The UNIQUE on user_id is important.

Without it:
```
Profile 1 → User 10
Profile 2 → User 10
```
would be possible.

With UNIQUE:
```
Profile 1 → User 10
```
but another profile cannot also reference User 10.

### What are its important parts?

The important concepts are:

Entity A

For example:
```
User
Entity B
UserProfile
Primary key
```
Uniquely identifies an instance/record.
```
User.id
Foreign key
```
Connects one entity to another.
```
UserProfile.user_id
Uniqueness
```
Ensures that the relationship is actually one-to-one rather than many-to-one.
```
UNIQUE(UserProfile.user_id)
Cardinality
```
Describes how many instances can participate in the relationship.

User 1 : 1 UserProfile
### What goes in?

Conceptually, you have instances of two entities.

For example:
```
User
id = 101
name = Sajid
```
and:
```
UserProfile
id = 501
user_id = 101
bio = "Developer"
```
The user_id = 101 establishes the connection.

### What comes out?

You can retrieve the related information.

Conceptually:
```
User 101
   ↓
UserProfile 501
```
So a query can give you:
```
User:
Sajid


Profile:
Developer
```
The important point is that the relationship allows you to navigate from one entity to its uniquely related entity.

### When should I use it?

Use 1-to-1 when the business rule genuinely says one-to-one.

Examples:
```
Person ─── Passport
User ─── UserProfile
Employee ─── EmployeeDetails
User ─── AccountSettings
Vehicle ─── VehicleRegistration
```
But don't automatically create 1-to-1 relationships just because you can.

For example, if:
```
User → Orders
```
a user can have multiple orders.

That's 1-to-many, not 1-to-1.
```
User 1 ─────── * Orders
```
10. How does it work internally?

This is the most important part.

Suppose you have:
```
Users
----------------
id
name


UserProfiles
----------------
id
user_id
bio

Data:

Users


id    name
101   Sajid
102   Ali
UserProfiles


id    user_id    bio
501   101        Developer
502   102        Designer
```
The relationship is created through:
```
UserProfiles.user_id
        ↓
Users.id

But there's a crucial constraint:

UNIQUE(user_id)
```
Therefore:
```
user_id = 101
```
can appear only once in UserProfiles.

So this is allowed:
```
Profile 501 → User 101
```
but this is rejected:
```
Profile 501 → User 101
Profile 502 → User 101   ❌
```
because 101 would occur twice.

The core idea

Think of 1-to-1 as:

A unique connection between two entity instances.

Or even simpler:

User 1  ←→  1 UserProfile

And remember:

The "1" on both sides is a business/data rule. In a relational database, constraints such as foreign keys and UNIQUE are what enforce that rule.
