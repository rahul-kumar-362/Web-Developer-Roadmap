# 🗄️ Database Development Guide

> **Master SQL, NoSQL, and Database Design**

---

## 📋 **Table of Contents**

1. [SQL Fundamentals](#sql-fundamentals)
2. [PostgreSQL](#postgresql)
3. [MongoDB](#mongodb)
4. [Database Design](#database-design)
5. [ORM/ODM](#ormodm)
6. [Performance Optimization](#performance-optimization)

---

## 📊 **SQL Fundamentals**

### **Basic Queries**

```sql
-- SELECT
SELECT * FROM users;
SELECT name, email FROM users;

-- WHERE
SELECT * FROM users WHERE age > 18;
SELECT * FROM users WHERE role = 'admin' AND active = true;

-- ORDER BY
SELECT * FROM users ORDER BY created_at DESC;
SELECT * FROM users ORDER BY name ASC, age DESC;

-- LIMIT
SELECT * FROM users LIMIT 10;
SELECT * FROM users LIMIT 10 OFFSET 20;

-- DISTINCT
SELECT DISTINCT role FROM users;
```

### **INSERT, UPDATE, DELETE**

```sql
-- INSERT
INSERT INTO users (name, email, age) VALUES ('John', 'john@example.com', 30);

-- UPDATE
UPDATE users SET age = 31 WHERE id = 1;
UPDATE users SET active = false WHERE last_login < '2024-01-01';

-- DELETE
DELETE FROM users WHERE id = 1;
DELETE FROM users WHERE active = false;
```

### **JOINs**

```sql
-- INNER JOIN
SELECT users.name, orders.total
FROM users
INNER JOIN orders ON users.id = orders.user_id;

-- LEFT JOIN
SELECT users.name, orders.total
FROM users
LEFT JOIN orders ON users.id = orders.user_id;

-- RIGHT JOIN
SELECT users.name, orders.total
FROM users
RIGHT JOIN orders ON users.id = orders.user_id;

-- Multiple JOINs
SELECT u.name, o.total, p.name as product_name
FROM users u
JOIN orders o ON u.id = o.user_id
JOIN order_items oi ON o.id = oi.order_id
JOIN products p ON oi.product_id = p.id;
```

### **Aggregation**

```sql
-- COUNT
SELECT COUNT(*) FROM users;
SELECT COUNT(*) FROM users WHERE active = true;

-- SUM, AVG, MIN, MAX
SELECT SUM(total) FROM orders;
SELECT AVG(age) FROM users;
SELECT MIN(price), MAX(price) FROM products;

-- GROUP BY
SELECT role, COUNT(*) as count
FROM users
GROUP BY role;

SELECT user_id, SUM(total) as total_spent
FROM orders
GROUP BY user_id
HAVING total_spent > 1000;
```

---

## 🐘 **PostgreSQL**

### **Connection**

```javascript
const { Pool } = require('pg');

const pool = new Pool({
    user: 'your_username',
    host: 'localhost',
    database: 'your_database',
    password: 'your_password',
    port: 5432,
});

async function query(text, params) {
    const start = Date.now();
    try {
        const res = await pool.query(text, params);
        const duration = Date.now() - start;
        console.log('Executed query', { text, duration, rows: res.rowCount });
        return res;
    } catch (error) {
        console.error('Database error:', error);
        throw error;
    }
}

module.exports = { query };
```

### **CRUD Operations**

```javascript
// CREATE
async function createUser(name, email, age) {
    const result = await query(
        'INSERT INTO users (name, email, age) VALUES ($1, $2, $3) RETURNING *',
        [name, email, age]
    );
    return result.rows[0];
}

// READ
async function getUserById(id) {
    const result = await query('SELECT * FROM users WHERE id = $1', [id]);
    return result.rows[0];
}

async function getAllUsers() {
    const result = await query('SELECT * FROM users ORDER BY created_at DESC');
    return result.rows;
}

// UPDATE
async function updateUser(id, updates) {
    const { name, email, age } = updates;
    const result = await query(
        'UPDATE users SET name = $1, email = $2, age = $3 WHERE id = $4 RETURNING *',
        [name, email, age, id]
    );
    return result.rows[0];
}

// DELETE
async function deleteUser(id) {
    const result = await query('DELETE FROM users WHERE id = $1 RETURNING *', [id]);
    return result.rows[0];
}
```

### **Transactions**

```javascript
async function transferMoney(fromUserId, toUserId, amount) {
    const client = await pool.connect();

    try {
        await client.query('BEGIN');

        // Deduct from sender
        await client.query(
            'UPDATE users SET balance = balance - $1 WHERE id = $2',
            [amount, fromUserId]
        );

        // Add to receiver
        await client.query(
            'UPDATE users SET balance = balance + $1 WHERE id = $2',
            [amount, toUserId]
        );

        await client.query('COMMIT');
    } catch (error) {
        await client.query('ROLLBACK');
        throw error;
    } finally {
        client.release();
    }
}
```

---

## 🍃 **MongoDB**

### **Connection**

```javascript
const mongoose = require('mongoose');

mongoose.connect('mongodb://localhost:27017/mydatabase', {
    useNewUrlParser: true,
    useUnifiedTopology: true,
});

mongoose.connection.on('connected', () => {
    console.log('Connected to MongoDB');
});

mongoose.connection.on('error', (err) => {
    console.error('MongoDB connection error:', err);
});
```

### **Schema & Model**

```javascript
const userSchema = new mongoose.Schema({
    name: {
        type: String,
        required: true,
        trim: true
    },
    email: {
        type: String,
        required: true,
        unique: true,
        lowercase: true,
        trim: true
    },
    age: {
        type: Number,
        min: 0,
        max: 150
    },
    role: {
        type: String,
        enum: ['user', 'admin', 'moderator'],
        default: 'user'
    },
    active: {
        type: Boolean,
        default: true
    },
    createdAt: {
        type: Date,
        default: Date.now
    }
});

const User = mongoose.model('User', userSchema);
```

### **CRUD Operations**

```javascript
// CREATE
async function createUser(userData) {
    const user = new User(userData);
    await user.save();
    return user;
}

// READ
async function getUserById(id) {
    return User.findById(id);
}

async function getUserByEmail(email) {
    return User.findOne({ email });
}

async function getAllUsers(filters = {}) {
    return User.find(filters).sort({ createdAt: -1 });
}

// UPDATE
async function updateUser(id, updates) {
    return User.findByIdAndUpdate(id, updates, { new: true });
}

async function updateUserByEmail(email, updates) {
    return User.findOneAndUpdate({ email }, updates, { new: true });
}

// DELETE
async function deleteUser(id) {
    return User.findByIdAndDelete(id);
}
```

### **Advanced Queries**

```javascript
// Complex queries
async function getActiveAdmins() {
    return User.find({ role: 'admin', active: true });
}

async function getUsersByAgeRange(minAge, maxAge) {
    return User.find({ age: { $gte: minAge, $lte: maxAge } });
}

async function searchUsers(searchTerm) {
    return User.find({
        $or: [
            { name: { $regex: searchTerm, $options: 'i' } },
            { email: { $regex: searchTerm, $options: 'i' } }
        ]
    });
}

// Aggregation
async function getUserStats() {
    return User.aggregate([
        {
            $group: {
                _id: '$role',
                count: { $sum: 1 },
                avgAge: { $avg: '$age' }
            }
        }
    ]);
}

// Pagination
async function getUsersPaginated(page = 1, limit = 10) {
    const skip = (page - 1) * limit;
    const users = await User.find()
        .skip(skip)
        .limit(limit)
        .sort({ createdAt: -1 });
    const total = await User.countDocuments();
    return { users, total, page, limit };
}
```

---

## 🎨 **Database Design**

### **Normalization**

```sql
-- Users table
CREATE TABLE users (
    id SERIAL PRIMARY KEY,
    name VARCHAR(100) NOT NULL,
    email VARCHAR(255) UNIQUE NOT NULL,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);

-- Orders table
CREATE TABLE orders (
    id SERIAL PRIMARY KEY,
    user_id INTEGER REFERENCES users(id) ON DELETE CASCADE,
    total DECIMAL(10, 2) NOT NULL,
    status VARCHAR(50) DEFAULT 'pending',
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);

-- Order items table
CREATE TABLE order_items (
    id SERIAL PRIMARY KEY,
    order_id INTEGER REFERENCES orders(id) ON DELETE CASCADE,
    product_id INTEGER REFERENCES products(id) ON DELETE CASCADE,
    quantity INTEGER NOT NULL,
    price DECIMAL(10, 2) NOT NULL
);

-- Products table
CREATE TABLE products (
    id SERIAL PRIMARY KEY,
    name VARCHAR(255) NOT NULL,
    description TEXT,
    price DECIMAL(10, 2) NOT NULL,
    stock INTEGER DEFAULT 0
);
```

### **Indexes**

```sql
-- Create index
CREATE INDEX idx_users_email ON users(email);
CREATE INDEX idx_orders_user_id ON orders(user_id);
CREATE INDEX idx_orders_status ON orders(status);

-- Composite index
CREATE INDEX idx_orders_user_status ON orders(user_id, status);

-- Unique index
CREATE UNIQUE INDEX idx_users_email ON users(email);

-- Drop index
DROP INDEX idx_users_email;
```

---

## 🔧 **ORM/ODM**

### **Prisma (PostgreSQL)**

```javascript
// schema.prisma
model User {
    id        Int      @id @default(autoincrement())
    name      String
    email     String   @unique
    age       Int?
    role      Role     @default(USER)
    active    Boolean  @default(true)
    createdAt DateTime @default(now())
    orders    Order[]
}

model Order {
    id        Int      @id @default(autoincrement())
    userId    Int
    user      User     @relation(fields: [userId], references: [id])
    total     Decimal
    status    String   @default("pending")
    createdAt DateTime @default(now())
}

enum Role {
    USER
    ADMIN
    MODERATOR
}
```

```javascript
// Using Prisma
import { PrismaClient } from '@prisma/client';

const prisma = new PrismaClient();

// CRUD
async function createUser(data) {
    return prisma.user.create({ data });
}

async function getUser(id) {
    return prisma.user.findUnique({ where: { id } });
}

async function updateUser(id, data) {
    return prisma.user.update({ where: { id }, data });
}

async function deleteUser(id) {
    return prisma.user.delete({ where: { id } });
}

// Advanced queries
async function getUsersWithOrders() {
    return prisma.user.findMany({
        include: { orders: true }
    });
}
```

---

## ⚡ **Performance Optimization**

### **Query Optimization**

```sql
-- Use indexes
EXPLAIN ANALYZE SELECT * FROM users WHERE email = 'john@example.com';

-- Avoid SELECT *
SELECT id, name, email FROM users;

-- Use LIMIT for large datasets
SELECT * FROM users LIMIT 100;

-- Use appropriate data types
CREATE TABLE users (
    id SERIAL PRIMARY KEY,
    name VARCHAR(100) NOT NULL,
    email VARCHAR(255) NOT NULL,
    age INTEGER CHECK (age >= 0)
);
```

### **Caching**

```javascript
const NodeCache = require('node-cache');
const cache = new NodeCache({ stdTTL: 600 }); // 10 minutes

async function getUserWithCache(id) {
    const cacheKey = `user:${id}`;

    // Check cache
    const cachedUser = cache.get(cacheKey);
    if (cachedUser) {
        return cachedUser;
    }

    // Fetch from database
    const user = await getUserById(id);

    // Cache the result
    cache.set(cacheKey, user);

    return user;
}
```

---

## 📚 **Learning Resources**

### **Documentation**
- [PostgreSQL Documentation](https://www.postgresql.org/docs/)
- [MongoDB Documentation](https://docs.mongodb.com/)
- [Prisma Documentation](https://www.prisma.io/docs)

### **Courses**
- [SQL Course](https://www.udemy.com/course/the-complete-sql-bootcamp/)
- [MongoDB Course](https://www.udemy.com/course/mongodb-the-complete-developers-guide/)
- [Database Design](https://www.coursera.org/learn/database-design)

---

**Ready to master databases? Start designing schemas!** 🚀

---

*Last Updated: 2024*