# 🧭 Lesson 6 — Advanced CRUD Operations (MongoDB)

## 🎯 Learning Objectives
By the end of this lesson, students will be able to:
- Use **advanced querying (READ)** techniques including comparison operators, logical operators, array queries, projections, sorting, and pagination.
- Apply **advanced UPDATE** operations including nested updates, array manipulation, field renaming, incrementing values, and upserts.
- Perform safe **DELETE** operations using filters and preview‑before‑delete techniques.
- Understand core MongoDB update operators such as `$set`, `$unset`, `$inc`, `$push`, `$pull`, `$addToSet`, `$rename`, and `$pop`.

---

# 📘 1. ADVANCED READ OPERATIONS (Simplest Version)
MongoDB advanced reads can also be extremely simple. Here are the easiest forms.

## 🔍 1.1 Find with multiple conditions (implicit AND)
```js
db.collection.find({ a: 5, b: 5 })
```

## 🔍 1.2 Count matching documents
```js
db.collection.find({ a: 5, b: 5 }).count()
```

## 🔍 1.3 Comparison Operators (simple format)
Users older than 30:
```js
db.users.find({ age: { $gt: 30 } })
```
Products cheaper than 20:
```js
db.products.find({ price: { $lt: 20 } })
```
Products with price >= 10 and <= 50:
```js
db.products.find({ price: { $gte: 10, $lte: 50 } })
```

## 🔍 1.4 OR condition (simplest form)
```js
db.users.find({ $or: [ { city: "Toronto" }, { city: "Vancouver" } ] })
```

---
# 🔧 2. ADVANCED UPDATE OPERATIONS (Simplest Version)
MongoDB updates can also be performed using very simple patterns.

## 🛠️ 2.1 Basic update with $set
```js
db.users.updateOne(
  { name: "Alex" },
  { $set: { age: 30 } }
)
```

## 🛠️ 2.2 Update using comparison
Increase age for users older than 25:
```js
db.users.updateMany(
  { age: { $gt: 25 } },
  { $set: { status: "senior" } }
)
```

## 🛠️ 2.3 Increment / Decrement a field (simple)
Increase:
```js
db.products.updateOne(
  { name: "Laptop" },
  { $inc: { stock: 1 } }
)
```

Decrease:
```js
db.products.updateOne(
  { name: "Laptop" },
  { $inc: { stock: -1 } }
)
```
```js
db.products.updateOne(
  { name: "Laptop" },
  { $inc: { stock: 1 } }
)
```

## 🛠️ 2.4 Remove a field (simple)
```js
db.users.updateOne(
  { name: "Alex" },
  { $unset: { temp: "" } }
)
```

## 🛠️ 2.5 Add or remove from array (simple)
Add:
```js
db.users.updateOne({ name: "John" }, { $push: { skills: "React" } })
```
Remove:
```js
db.users.updateOne({ name: "John" }, { $pull: { skills: "React" } })
```

--- ADVANCED UPDATE OPERATIONS
MongoDB allows highly flexible updates targeting fields, nested fields, and arrays.

## 🛠️ 2.1 Update Many
```js
db.users.updateMany(
  { country: "Canada" },
  { $set: { active: true } }
)
```

---

## 🛠️ 2.2 Update a Single Field
```js
db.users.updateOne(
  { email: "alex@mail.com" },
  { $set: { phone: "123-123-1234" } }
)
```

---

## 🛠️ 2.3 Increment Values
```js
db.products.updateOne(
  { name: "Laptop" },
  { $inc: { stock: -1 } }
)
```

---

## 🛠️ 2.4 Rename a Field
```js
db.users.updateMany(
  {},
  { $rename: { "fullName": "name" } }
)
```

---

## 🛠️ 2.5 Remove a Field (`$unset`)
```js
db.users.updateOne(
  { email: "alex@mail.com" },
  { $unset: { tempField: "" } }
)
```

---

## 🛠️ 2.6 Update Nested Fields
```js
db.users.updateOne(
  { email: "alex@mail.com" },
  { $set: { "address.city": "Toronto" } }
)
```

---

## 🛠️ 2.7 Array Modifications
### Add an element to an array
```js
db.users.updateOne(
  { name: "John" },
  { $push: { skills: "Node.js" } }
)
```

### Add only if unique
```js
db.users.updateOne(
  { name: "John" },
  { $addToSet: { skills: "Node.js" } }
)
```

### Remove a specific element
```js
db.users.updateOne(
  { name: "John" },
  { $pull: { skills: "React" } }
)
```

### Pop from array
```js
db.users.updateOne(
  { name: "John" },
  { $pop: { skills: 1 } }   // 1 = remove last, -1 = remove first
)
```

---

## 🛠️ 2.8 Upsert (Update OR Insert)
```js
db.users.updateOne(
  { email: "newuser@mail.com" },
  { $set: { name: "New User", active: true } },
  { upsert: true }
)
```
If document exists → update. If not → insert.

---

# ❌ 3. ADVANCED DELETE OPERATIONS

## 🗑️ 3.1 Delete One
```js
db.users.deleteOne({ email: "alex@mail.com" })
```

---

## 🗑️ 3.2 Delete Many
```js
db.logs.deleteMany({ status: "expired" })
```

---

## 🗑️ 3.3 Delete Nested Conditions
```js
db.orders.deleteMany({ "payment.status": "failed" })
```

---

## 🗑️ 3.4 Safe Delete Workflow
### Step 1 — Preview
```js
db.users.find({ inactive: true })
```

### Step 2 — Delete
```js
db.users.deleteMany({ inactive: true })
```

---

# 📊 4. AGGREGATION & MULTI-FIELD SEARCH (Simplest Version)
MongoDB aggregation can be kept **very simple**. Here are the easiest ways to do `count`, `sum`, `avg`, and **group by**.

## 📈 4.1 Count Documents (Simplest)
Count all users from Canada:
```js
db.users.find({ country: "Canada" }).count()
```
Count all movies released in 2020:
```js
db.movies.find({ year: 2020 }).count()
```

---

## 📈 4.2 Sum (Simplest Possible Form)
Total order amount:
```js
db.orders.aggregate([
  { $group: { _id: null, totalAmount: { $sum: "$amount" } } }
])
```
> `_id: null` means "treat all documents as one group".

---

## 📈 4.3 Average (Simplest Possible Form)
Average movie rating:
```js
db.movies.aggregate([
  { $group: { _id: null, avgRating: { $avg: "$imdb.rating" } } }
])
```

---

# ⭐ SIMPLE GROUP-BY EXAMPLES (when `_id` is NOT null)
These examples show how to group documents **by a field**.

## 🎬 Group movies by year
```js
db.movies.aggregate([
  {
    $group: {
      _id: "$year",
      totalMovies: { $count: {} }
    }
  }
])
```

## 🛒 Total sales per customer
```js
db.orders.aggregate([
  {
    $group: {
      _id: "$customerId",
      totalSpent: { $sum: "$amount" }
    }
  }
])
```

## ⭐ Average rating per movie
```js
db.reviews.aggregate([
  {
    $group: {
      _id: "$movieId",
      averageRating: { $avg: "$rating" }
    }
  }
])
```

## 💬 Number of comments per movie
```js
db.comments.aggregate([
  {
    $group: {
      _id: "$movie_id",
      totalComments: { $count: {} }
    }
  }
])
```

---

## 🔎 4.4 Search Based on Multiple Fields (AND)
```js
db.users.find({
  country: "Canada",
  active: true,
  age: { $gt: 25 }
})
```

---

## 🔎 4.5 OR Search (Simplest Form)
```js
db.users.find({
  $or: [
    { city: "Toronto" },
    { premium: true }
  ]
})
```

---
# 📝 Assignment
Choose **ONE** dataset from the list below and practice **every querying method** learned today. These datasets come from the MongoDB sample data you already have access to.

## 📁 Available Datasets (Choose ONE)
- **`sample_mflix`** – movies, comments, theaters, users
- **`sample_airbnb`** – listings, pricing, reviews
- **`sample_supplies`** – office supplies sales & inventory
- **`sample_training`** – grades, zips, companies
- **`sample_analytics`** – website user journeys
- **`sample_geospatial`** – geolocation datasets
- **`sample_weatherdata`** – weather metrics & reports

---
## What You Must Practice
Use your chosen dataset to perform **ALL** of the following:

### READ (Find)
- Find using multiple fields (implicit AND)
- OR queries using `$or`
- Comparison operators: `$gt`, `$lt`, `$gte`, `$lte`
- Projection (select specific fields)
- Sorting
- Pagination using `.skip()` + `.limit()`

### COUNT
- Count documents with simple or multiple conditions

### UPDATE
- `$set`
- `$inc` (increment & decrement)
- `$unset`
- Array updates: `$push`, `$pull`, `$addToSet`
- Update using filters like `{ age: { $gt: X } }`

### DELETE
- `deleteOne`
- `deleteMany` with multiple conditions

### AGGREGATION (Simplest Forms)
- `$sum`
- `$avg`
- Group by a field (e.g., year, city, userId)

---

# 🎯 Final Outcome
You gain hands‑on experience with **real‑world MongoDB operations**, preparing them for backend development, REST API building, and full‑stack projects.

