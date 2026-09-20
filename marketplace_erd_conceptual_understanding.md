# Conceptual Understanding of an ERD

## What is an ERD?

An **Entity Relationship Diagram (ERD)** is a visual model of how data in a database is organized and how different pieces of data relate to each other.

Think of it as a **map of the database before you build the database**.

For your student marketplace, the ERD answers questions such as:

- What information do we need to store?
- Which tables should exist?
- Which table owns or depends on another table?
- How do buyers, sellers, products, orders, messages, and reviews connect?
- Which relationships should the database enforce?

---

# 1. Entity

An **entity** is a real-world thing or concept that we want to store information about.

In a relational database, an entity normally becomes a **table**.

Examples from the marketplace:

```text
profiles
shops
products
orders
messages
reviews
institutions
student_profiles
```

For example:

```text
products
```

represents all products listed on the marketplace.

Each individual product is a **row** inside that table.

Example:

| id | title | price | quantity |
|---|---|---:|---:|
| p1 | Calculus Textbook | 350 | 1 |
| p2 | Scientific Calculator | 250 | 3 |

So:

```text
Entity = Table
Instance of entity = Row
```

---

# 2. Attribute

An **attribute** is information that describes an entity.

Attributes normally become **columns**.

Example:

```text
products
----------------
id
shop_id
title
description
price
condition
quantity
status
```

Here:

- `title` describes the product.
- `price` stores its current price.
- `quantity` stores how many are available.
- `shop_id` tells us which shop owns the product.

Conceptually:

```text
ENTITY
   |
   +-- ATTRIBUTE
   +-- ATTRIBUTE
   +-- ATTRIBUTE
```

---

# 3. Primary Key

A **Primary Key (PK)** uniquely identifies one row.

For example:

```text
products
----------------
id UUID PK
title
price
```

Even if two sellers list a product called:

```text
Scientific Calculator
```

their database rows still have different IDs.

Example:

```text
Product A
id = 2ca4...

Product B
id = 97be...
```

The primary key tells the database:

> This is exactly this record and no other one.

For this project, most tables use:

```text
UUID
```

as their primary key.

---

# 4. Foreign Key

A **Foreign Key (FK)** connects one table to another.

Example:

```text
shops
----------------
id PK
owner_id FK
```

`owner_id` points to:

```text
profiles.id
```

So:

```text
profiles
   |
   | id
   |
   +---------> shops.owner_id
```

This means:

> The shop belongs to a particular profile.

Another example:

```text
products.shop_id
    -> shops.id
```

Meaning:

> Every product belongs to a shop.

Foreign keys are one of the most important parts of an ERD because they define the relationships between entities.

---

# 5. Relationships

A relationship describes how two entities are connected.

Examples:

```text
profile owns shop

shop sells products

student places orders

order contains order items

conversation contains messages
```

In an ERD, these relationships are shown using lines between tables.

---

# 6. Cardinality

**Cardinality** tells us how many records on one side can relate to records on the other side.

The main types are:

## One-to-One — 1:1

Example:

```text
auth.users 1 ----- 1 profiles
```

One Supabase authenticated user has one application profile.

Conceptually:

```text
User A -> Profile A
User B -> Profile B
```

---

## One-to-Many — 1:N

Example:

```text
shops 1 ----- N products
```

One shop may have many products.

```text
Campus Tech
   |
   +-- Laptop
   +-- Calculator
   +-- Headphones
```

But each of those products belongs to one shop.

Another example:

```text
orders 1 ----- N order_items
```

One order can contain multiple items.

---

## Optional One-to-One — 1:0..1

Your marketplace uses this relationship here:

```text
profiles 1 ----- 0..1 student_profiles
```

Every `student_profile` must belong to a profile.

However, not every profile must be a student.

This supports your marketplace rule:

```text
Student
profiles ✓
student_profiles ✓

Non-student seller
profiles ✓
student_profiles ✗
```

---

# 7. Your Buyer and Seller Model

One of the most important concepts in your ERD is that **buyer and seller are not simply account roles**.

You should not model:

```text
role = buyer
role = seller
role = both
```

Instead, the relationships determine what the user can do.

---

## Buyer

A buyer must be a verified student.

Conceptually:

```text
auth.users
     |
     v
profiles
     |
     v
student_profiles
     |
     v
orders
```

So buyer eligibility comes from:

```text
student_profiles.verification_status = verified
```

A user without a verified student profile should not be able to place an order.

---

## Seller

A seller is a profile that owns a shop.

```text
auth.users
     |
     v
profiles
     |
     v
shops
     |
     v
products
```

A seller does **not** need a `student_profile`.

Therefore:

```text
Non-student seller

profiles ✓
student_profiles ✗
shops ✓
```

is valid.

---

## Student who is both

A verified student can also own a shop.

```text
                  profiles
                 /        \
                v          v
       student_profiles   shops
             |              |
           BUYER          SELLER
```

Therefore:

```text
profiles ✓
student_profiles ✓
shops ✓
```

means:

> Buyer and seller at the same time.

---

# 8. Why `student_profiles` Exists Separately

You could technically put:

```text
university
student_number
is_verified
```

inside `profiles`.

However, this would imply that student information belongs to every user.

That is incorrect because some sellers may not be students.

Instead:

```text
profiles
```

contains information every user can have.

```text
student_profiles
```

contains information only students have.

This is an example of separating data according to its real-world meaning.

---

# 9. Institutions

Instead of storing:

```text
university = "Wits"
```

as plain text, the ERD uses:

```text
institutions
```

and:

```text
student_profiles.institution_id
```

Relationship:

```text
institutions
      |
      | 1:N
      v
student_profiles
```

This prevents inconsistent values such as:

```text
Wits
WITS
Wits University
University of Witwatersrand
University of the Witwatersrand
```

All students instead reference the same institution record.

---

# 10. Selling Structure

The selling side is:

```text
profiles
    |
    | owns
    v
shops
    |
    | sells
    v
products
    |
    | has
    v
product_images
```

This creates an ownership chain.

For example:

```text
User
  -> Campus Tech
       -> Dell Laptop
            -> front.jpg
            -> side.jpg
```

The product does not need a direct `seller_id`.

The seller can already be found through:

```text
product
 -> shop
 -> owner
 -> profile
```

This avoids duplicating the same relationship.

---

# 11. Ordering Structure

The ordering side is:

```text
student_profiles
       |
       | places
       v
     orders
       |
       | contains
       v
  order_items
       |
       v
    products
```

The shop is also connected directly to the order:

```text
shops
  |
  v
orders
```

Therefore an order identifies:

```text
Who bought it?
orders.buyer_id

Which shop received it?
orders.shop_id
```

---

# 12. Why `order_items` Exists

Suppose a student buys:

```text
1 textbook
2 notebooks
1 calculator
```

Trying to store everything directly inside `orders` would be difficult.

Instead:

```text
orders

Order #100
   |
   +-- order_item -> Textbook
   +-- order_item -> Notebook x2
   +-- order_item -> Calculator
```

This creates a flexible one-to-many structure.

---

# 13. Why `unit_price` Is Stored in `order_items`

The current product price can change.

For example:

```text
Today:
Laptop = 5000

Next month:
Laptop = 4500
```

If someone purchased it today for 5000, their historical order should still show the original purchase price.

Therefore:

```text
order_items.unit_price
```

stores the price at the moment the order was placed.

---

# 14. Messaging Structure

Messaging is modeled as:

```text
student_profiles
       |
       v
conversations
       |
       v
messages
```

The conversation also references:

```text
shop
```

and optionally:

```text
product
```

Example:

```text
Buyer
  |
  v
Conversation
  |
  +-- Shop: Campus Tech
  |
  +-- Product: Dell Laptop
  |
  +-- Message 1
  +-- Message 2
  +-- Message 3
```

`product_id` is optional because a student may contact a seller without asking about one particular product.

---

# 15. Why `messages.sender_id` References `profiles`

This relationship is important.

```text
messages.sender_id
    -> profiles.id
```

It does **not** point only to `student_profiles`.

Why?

Because messages can be sent by:

```text
student buyer
student seller
non-student seller
```

All of them have a `profile`.

Therefore `profiles` is the correct parent entity.

---

# 16. Reviews

Reviews connect:

```text
student_profiles
orders
products
```

Conceptually:

```text
Student
   |
   | writes
   v
 Review
   |
   +----> Order
   |
   +----> Product
```

This allows the marketplace to determine:

- who wrote the review;
- which product was reviewed;
- which purchase proves that the user actually bought the product.

The application can therefore enforce:

> Only students who actually completed an eligible purchase can review the product.

---

# 17. Referential Integrity

Foreign keys help maintain **referential integrity**.

For example:

```text
products.shop_id -> shops.id
```

means the database should not allow a product to reference a shop that does not exist.

Without foreign keys, you could accidentally have:

```text
products.shop_id = abc123
```

while no shop with:

```text
id = abc123
```

exists.

Foreign keys prevent this kind of broken data.

---

# 18. The ERD Is Not the Same as the Database

The ERD is the **design**.

The actual PostgreSQL database is the implementation.

The development flow is:

```text
Business requirements
        |
        v
       ERD
        |
        v
Database schema
        |
        v
Supabase migrations
        |
        v
RLS policies
        |
        v
API / backend
        |
        v
Frontend
```

That is why drawing the ERD before migrations is useful.

You are deciding how the data should work before committing it to SQL.

---

# 19. ERD vs Business Rules

An ERD can enforce some rules directly.

For example:

```text
orders.buyer_id
 -> student_profiles.profile_id
```

means an order cannot reference an arbitrary non-student profile.

However, the ERD alone cannot enforce every rule.

For example:

```text
verification_status = 'verified'
```

before checkout may require:

- database constraints;
- Supabase Row Level Security (RLS);
- backend validation.

So think of the ERD as defining the **structure**, while policies and application logic enforce more detailed behavior.

---

# 20. Your Marketplace ERD at a High Level

```text
                         auth.users
                             |
                             v
                          profiles
                        /          \
                       /            \
                      v              v
             student_profiles      shops
                  |                  |
                  |                  v
                  |               products
                  |                  |
                  |                  v
                  |            product_images
                  |
                  v
                orders <----------- shops
                  |
                  v
             order_items
                  |
                  v
               products


student_profiles
       |
       v
conversations <---------- shops
       |
       +-----------------> products (optional)
       |
       v
    messages
       ^
       |
    profiles


student_profiles
       |
       v
    reviews
     /   \
    v     v
 orders products
```

---

# 21. How to Read an ERD

When looking at any table, ask five questions:

### 1. What real-world thing does this table represent?

Example:

```text
shops
```

represents seller storefronts.

### 2. What uniquely identifies a row?

Look for:

```text
PK
```

### 3. Which other tables does it depend on?

Look for:

```text
FK
```

### 4. How many records can exist on each side?

Look at:

```text
1:1
1:N
0..1
```

### 5. What business rule is the relationship expressing?

Example:

```text
shops.owner_id -> profiles.id
```

means:

> Every shop has an owner who is a registered user.

---

# 22. Key Concepts to Remember

```text
Entity
= thing we store
= usually a table

Attribute
= information about an entity
= usually a column

Primary Key
= uniquely identifies a row

Foreign Key
= references another table

Relationship
= connection between entities

Cardinality
= how many records may participate

1:1
= one to one

1:N
= one to many

0..1
= optional one

Referential Integrity
= relationships must point to valid records
```

---

# 23. Most Important Mental Model

Do not think of the ERD simply as a collection of tables.

Think of it as a description of the real marketplace.

```text
A person registers
        |
        v
      profile
        |
        +---- if student ----> student_profile
        |                         |
        |                         +---- buys
        |
        +---- if selling ----> shop
                                  |
                                  +---- products
```

Then:

```text
student buys product
        |
        v
      order
        |
        v
   order_items
```

And:

```text
student talks to seller
        |
        v
 conversation
        |
        v
     messages
```

And after purchasing:

```text
student
   |
   v
 review
   |
   v
 product
```

Once you can tell that story from the diagram, you understand the ERD.

---

# 24. Final Conceptual Rule

For this marketplace:

> **A profile represents a person or account. A student profile represents verified student identity. A shop represents selling capability. Orders represent buying activity. Relationships, rather than a simple buyer/seller role field, determine how each user participates in the marketplace.**

That is the core concept behind the ERD.
