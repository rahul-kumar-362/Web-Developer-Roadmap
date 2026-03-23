# ⚙️ Backend Development Guide

> **Master Server-Side Development, APIs, and Database Integration**

---

## 📋 **Table of Contents**

1. [Node.js Fundamentals](#nodejs-fundamentals)
2. [Express.js Framework](#expressjs-framework)
3. [RESTful APIs](#restful-apis)
4. [Authentication & Authorization](#authentication--authorization)
5. [Database Integration](#database-integration)
6. [Error Handling](#error-handling)
7. [Testing](#testing)
8. [Deployment](#deployment)

---

## 🟢 **Node.js Fundamentals**

### **What is Node.js?**

Node.js is a JavaScript runtime built on Chrome's V8 JavaScript engine. It allows you to run JavaScript on the server side.

### **Basic Node.js Server**

```javascript
const http = require('http');

const server = http.createServer((req, res) => {
    res.writeHead(200, { 'Content-Type': 'text/plain' });
    res.end('Hello, World!');
});

const PORT = 3000;
server.listen(PORT, () => {
    console.log(`Server running on http://localhost:${PORT}`);
});
```

### **Modules and require**

```javascript
// Built-in modules
const fs = require('fs');
const path = require('path');
const http = require('http');

// Custom modules
const myModule = require('./myModule');

// npm packages
const express = require('express');

// ES6 modules (package.json: "type": "module")
import express from 'express';
import { readFile } from 'fs/promises';
```

### **File System Operations**

```javascript
const fs = require('fs');
const fsPromises = require('fs/promises');

// Read file (callback)
fs.readFile('file.txt', 'utf8', (err, data) => {
    if (err) throw err;
    console.log(data);
});

// Read file (Promise)
fsPromises.readFile('file.txt', 'utf8')
    .then(data => console.log(data))
    .catch(err => console.error(err));

// Read file (async/await)
async function readFile() {
    try {
        const data = await fsPromises.readFile('file.txt', 'utf8');
        console.log(data);
    } catch (err) {
        console.error(err);
    }
}

// Write file
fs.writeFile('output.txt', 'Hello, World!', (err) => {
    if (err) throw err;
    console.log('File written successfully');
});
```

### **Asynchronous Programming**

```javascript
// Callbacks
function fetchData(callback) {
    setTimeout(() => {
        callback('Data received');
    }, 1000);
}

fetchData((data) => {
    console.log(data);
});

// Promises
function fetchData() {
    return new Promise((resolve, reject) => {
        setTimeout(() => {
            resolve('Data received');
        }, 1000);
    });
}

fetchData()
    .then(data => console.log(data))
    .catch(err => console.error(err));

// Async/Await
async function getData() {
    try {
        const data = await fetchData();
        console.log(data);
    } catch (err) {
        console.error(err);
    }
}
```

---

## 🚀 **Express.js Framework**

### **Basic Express Server**

```javascript
const express = require('express');
const app = express();

// Middleware
app.use(express.json());
app.use(express.urlencoded({ extended: true }));

// Routes
app.get('/', (req, res) => {
    res.json({ message: 'Hello, World!' });
});

app.post('/api/data', (req, res) => {
    const data = req.body;
    res.json({ received: data });
});

// Start server
const PORT = 3000;
app.listen(PORT, () => {
    console.log(`Server running on http://localhost:${PORT}`);
});
```

### **Routing**

```javascript
const express = require('express');
const router = express.Router();

// GET route
router.get('/users', (req, res) => {
    res.json({ users: [] });
});

// GET with parameter
router.get('/users/:id', (req, res) => {
    const userId = req.params.id;
    res.json({ userId });
});

// POST route
router.post('/users', (req, res) => {
    const userData = req.body;
    res.status(201).json({ created: userData });
});

// PUT route
router.put('/users/:id', (req, res) => {
    const userId = req.params.id;
    const updates = req.body;
    res.json({ userId, updates });
});

// DELETE route
router.delete('/users/:id', (req, res) => {
    const userId = req.params.id;
    res.json({ deleted: userId });
});

module.exports = router;
```

### **Middleware**

```javascript
const express = require('express');
const app = express();

// Custom middleware
const logger = (req, res, next) => {
    console.log(`${req.method} ${req.url}`);
    next();
};

// Error handling middleware
const errorHandler = (err, req, res, next) => {
    console.error(err.stack);
    res.status(500).json({ error: 'Something went wrong!' });
};

// Authentication middleware
const authenticate = (req, res, next) => {
    const token = req.headers.authorization;
    if (!token) {
        return res.status(401).json({ error: 'Unauthorized' });
    }
    // Verify token
    next();
};

// Apply middleware
app.use(logger);
app.use('/protected', authenticate);

// Error handling
app.use(errorHandler);
```

### **Static Files**

```javascript
const express = require('express');
const path = require('path');
const app = express();

// Serve static files
app.use(express.static(path.join(__dirname, 'public')));

// Serve specific directory
app.use('/images', express.static(path.join(__dirname, 'images')));

// Custom static file serving
app.get('/download/:filename', (req, res) => {
    const filename = req.params.filename;
    const filePath = path.join(__dirname, 'files', filename);
    res.download(filePath);
});
```

---

## 🌐 **RESTful APIs**

### **REST Principles**

REST (Representational State Transfer) is an architectural style for designing networked applications.

**Key Principles:**
- **Stateless**: Each request contains all necessary information
- **Resource-Based**: Everything is a resource with a unique identifier
- **Uniform Interface**: Consistent API design
- **Client-Server**: Separation of concerns

### **HTTP Methods**

| Method | Purpose | Example |
|--------|---------|---------|
| GET | Retrieve data | `GET /users` |
| POST | Create data | `POST /users` |
| PUT | Update data (full) | `PUT /users/1` |
| PATCH | Update data (partial) | `PATCH /users/1` |
| DELETE | Delete data | `DELETE /users/1` |

### **API Design Best Practices**

```javascript
const express = require('express');
const app = express();

// Resource naming (plural nouns)
app.get('/users', getUsers);
app.get('/users/:id', getUser);
app.post('/users', createUser);
app.put('/users/:id', updateUser);
app.delete('/users/:id', deleteUser);

// Nested resources
app.get('/users/:id/posts', getUserPosts);
app.post('/users/:id/posts', createUserPost);

// Filtering and sorting
app.get('/users?role=admin&sort=name', getUsers);

// Pagination
app.get('/users?page=2&limit=10', getUsers);

// Versioning
app.get('/api/v1/users', getUsersV1);
app.get('/api/v2/users', getUsersV2);
```

### **API Response Format**

```javascript
// Success response
{
    "success": true,
    "data": {
        "id": 1,
        "name": "John Doe",
        "email": "john@example.com"
    },
    "message": "User created successfully"
}

// Error response
{
    "success": false,
    "error": {
        "code": "USER_NOT_FOUND",
        "message": "User not found",
        "details": {
            "field": "id",
            "value": 999
        }
    }
}

// Paginated response
{
    "success": true,
    "data": [...],
    "pagination": {
        "page": 1,
        "limit": 10,
        "total": 100,
        "pages": 10
    }
}
```

### **Complete REST API Example**

```javascript
const express = require('express');
const app = express();

app.use(express.json());

// In-memory database
let users = [
    { id: 1, name: 'John Doe', email: 'john@example.com' },
    { id: 2, name: 'Jane Smith', email: 'jane@example.com' }
];

// GET all users
app.get('/api/users', (req, res) => {
    const { role, sort } = req.query;
    let result = [...users];

    if (role) {
        result = result.filter(user => user.role === role);
    }

    if (sort) {
        result.sort((a, b) => a[sort].localeCompare(b[sort]));
    }

    res.json({
        success: true,
        data: result,
        count: result.length
    });
});

// GET single user
app.get('/api/users/:id', (req, res) => {
    const user = users.find(u => u.id === parseInt(req.params.id));

    if (!user) {
        return res.status(404).json({
            success: false,
            error: {
                code: 'USER_NOT_FOUND',
                message: 'User not found'
            }
        });
    }

    res.json({
        success: true,
        data: user
    });
});

// POST new user
app.post('/api/users', (req, res) => {
    const { name, email } = req.body;

    if (!name || !email) {
        return res.status(400).json({
            success: false,
            error: {
                code: 'VALIDATION_ERROR',
                message: 'Name and email are required'
            }
        });
    }

    const newUser = {
        id: users.length + 1,
        name,
        email
    };

    users.push(newUser);

    res.status(201).json({
        success: true,
        data: newUser,
        message: 'User created successfully'
    });
});

// PUT update user
app.put('/api/users/:id', (req, res) => {
    const userIndex = users.findIndex(u => u.id === parseInt(req.params.id));

    if (userIndex === -1) {
        return res.status(404).json({
            success: false,
            error: {
                code: 'USER_NOT_FOUND',
                message: 'User not found'
            }
        });
    }

    users[userIndex] = {
        ...users[userIndex],
        ...req.body
    };

    res.json({
        success: true,
        data: users[userIndex],
        message: 'User updated successfully'
    });
});

// DELETE user
app.delete('/api/users/:id', (req, res) => {
    const userIndex = users.findIndex(u => u.id === parseInt(req.params.id));

    if (userIndex === -1) {
        return res.status(404).json({
            success: false,
            error: {
                code: 'USER_NOT_FOUND',
                message: 'User not found'
            }
        });
    }

    users.splice(userIndex, 1);

    res.json({
        success: true,
        message: 'User deleted successfully'
    });
});

const PORT = 3000;
app.listen(PORT, () => {
    console.log(`API server running on http://localhost:${PORT}`);
});
```

---

## 🔐 **Authentication & Authorization**

### **JWT Authentication**

```javascript
const jwt = require('jsonwebtoken');
const bcrypt = require('bcryptjs');

const SECRET_KEY = 'your-secret-key';

// Generate token
function generateToken(user) {
    return jwt.sign(
        { id: user.id, email: user.email },
        SECRET_KEY,
        { expiresIn: '24h' }
    );
}

// Verify token
function verifyToken(token) {
    try {
        return jwt.verify(token, SECRET_KEY);
    } catch (err) {
        return null;
    }
}

// Hash password
async function hashPassword(password) {
    const salt = await bcrypt.genSalt(10);
    return bcrypt.hash(password, salt);
}

// Compare password
async function comparePassword(password, hashedPassword) {
    return bcrypt.compare(password, hashedPassword);
}

// Authentication middleware
function authenticate(req, res, next) {
    const token = req.headers.authorization?.split(' ')[1];

    if (!token) {
        return res.status(401).json({ error: 'No token provided' });
    }

    const decoded = verifyToken(token);

    if (!decoded) {
        return res.status(401).json({ error: 'Invalid token' });
    }

    req.user = decoded;
    next();
}

// Usage
app.post('/api/register', async (req, res) => {
    const { email, password } = req.body;
    const hashedPassword = await hashPassword(password);

    // Save user to database
    const user = { id: 1, email, password: hashedPassword };

    const token = generateToken(user);

    res.json({ token });
});

app.post('/api/login', async (req, res) => {
    const { email, password } = req.body;

    // Find user in database
    const user = { id: 1, email, password: 'hashed_password' };

    const isValid = await comparePassword(password, user.password);

    if (!isValid) {
        return res.status(401).json({ error: 'Invalid credentials' });
    }

    const token = generateToken(user);

    res.json({ token });
});

app.get('/api/protected', authenticate, (req, res) => {
    res.json({ message: 'Protected data', user: req.user });
});
```

### **OAuth 2.0**

```javascript
const express = require('express');
const passport = require('passport');
const GoogleStrategy = require('passport-google-oauth20').Strategy;

passport.use(new GoogleStrategy({
    clientID: process.env.GOOGLE_CLIENT_ID,
    clientSecret: process.env.GOOGLE_CLIENT_SECRET,
    callbackURL: '/auth/google/callback'
},
(accessToken, refreshToken, profile, done) => {
    // Find or create user
    return done(null, profile);
}));

app.get('/auth/google', passport.authenticate('google', { scope: ['profile', 'email'] }));

app.get('/auth/google/callback',
    passport.authenticate('google', { failureRedirect: '/login' }),
    (req, res) => {
        res.redirect('/dashboard');
    }
);
```

---

## 🗄️ **Database Integration**

### **MongoDB with Mongoose**

```javascript
const mongoose = require('mongoose');

// Connect to MongoDB
mongoose.connect('mongodb://localhost:27017/mydatabase', {
    useNewUrlParser: true,
    useUnifiedTopology: true
});

// Define schema
const userSchema = new mongoose.Schema({
    name: { type: String, required: true },
    email: { type: String, required: true, unique: true },
    password: { type: String, required: true },
    role: { type: String, default: 'user' },
    createdAt: { type: Date, default: Date.now }
});

// Create model
const User = mongoose.model('User', userSchema);

// CRUD Operations

// Create
async function createUser(userData) {
    const user = new User(userData);
    await user.save();
    return user;
}

// Read
async function getUserById(id) {
    return User.findById(id);
}

async function getUserByEmail(email) {
    return User.findOne({ email });
}

async function getAllUsers() {
    return User.find();
}

// Update
async function updateUser(id, updates) {
    return User.findByIdAndUpdate(id, updates, { new: true });
}

// Delete
async function deleteUser(id) {
    return User.findByIdAndDelete(id);
}

// Usage in Express
app.post('/api/users', async (req, res) => {
    try {
        const user = await createUser(req.body);
        res.status(201).json(user);
    } catch (err) {
        res.status(400).json({ error: err.message });
    }
});

app.get('/api/users/:id', async (req, res) => {
    try {
        const user = await getUserById(req.params.id);
        if (!user) {
            return res.status(404).json({ error: 'User not found' });
        }
        res.json(user);
    } catch (err) {
        res.status(500).json({ error: err.message });
    }
});
```

### **PostgreSQL with Sequelize**

```javascript
const { Sequelize, DataTypes } = require('sequelize');

// Connect to PostgreSQL
const sequelize = new Sequelize('database', 'username', 'password', {
    host: 'localhost',
    dialect: 'postgres'
});

// Define model
const User = sequelize.define('User', {
    name: {
        type: DataTypes.STRING,
        allowNull: false
    },
    email: {
        type: DataTypes.STRING,
        allowNull: false,
        unique: true
    },
    password: {
        type: DataTypes.STRING,
        allowNull: false
    }
});

// Sync database
sequelize.sync();

// CRUD Operations

// Create
async function createUser(userData) {
    return User.create(userData);
}

// Read
async function getUserById(id) {
    return User.findByPk(id);
}

async function getUserByEmail(email) {
    return User.findOne({ where: { email } });
}

async function getAllUsers() {
    return User.findAll();
}

// Update
async function updateUser(id, updates) {
    const user = await User.findByPk(id);
    return user.update(updates);
}

// Delete
async function deleteUser(id) {
    const user = await User.findByPk(id);
    return user.destroy();
}
```

---

## ⚠️ **Error Handling**

### **Global Error Handler**

```javascript
const express = require('express');
const app = express();

// Custom error class
class AppError extends Error {
    constructor(message, statusCode) {
        super(message);
        this.statusCode = statusCode;
        this.isOperational = true;
    }
}

// Async error wrapper
const asyncHandler = (fn) => (req, res, next) => {
    Promise.resolve(fn(req, res, next)).catch(next);
};

// Error handling middleware
const errorHandler = (err, req, res, next) => {
    console.error(err.stack);

    if (err.isOperational) {
        return res.status(err.statusCode).json({
            success: false,
            error: {
                message: err.message,
                code: err.code
            }
        });
    }

    res.status(500).json({
        success: false,
        error: {
            message: 'Internal server error'
        }
    });
};

// 404 handler
const notFoundHandler = (req, res, next) => {
    const error = new AppError('Route not found', 404);
    next(error);
};

// Usage
app.get('/api/users', asyncHandler(async (req, res) => {
    const users = await getAllUsers();
    res.json(users);
}));

app.use(notFoundHandler);
app.use(errorHandler);
```

### **Validation Errors**

```javascript
const { body, validationResult } = require('express-validator');

app.post('/api/users',
    [
        body('name').notEmpty().withMessage('Name is required'),
        body('email').isEmail().withMessage('Invalid email'),
        body('password').isLength({ min: 6 }).withMessage('Password must be at least 6 characters')
    ],
    (req, res) => {
        const errors = validationResult(req);

        if (!errors.isEmpty()) {
            return res.status(400).json({
                success: false,
                errors: errors.array()
            });
        }

        // Process valid data
        res.json({ message: 'User created' });
    }
);
```

---

## 🧪 **Testing**

### **Unit Testing with Jest**

```javascript
const request = require('supertest');
const app = require('../app');

describe('User API', () => {
    test('GET /api/users should return all users', async () => {
        const response = await request(app)
            .get('/api/users')
            .expect(200);

        expect(response.body.success).toBe(true);
        expect(Array.isArray(response.body.data)).toBe(true);
    });

    test('POST /api/users should create a user', async () => {
        const newUser = {
            name: 'John Doe',
            email: 'john@example.com',
            password: 'password123'
        };

        const response = await request(app)
            .post('/api/users')
            .send(newUser)
            .expect(201);

        expect(response.body.success).toBe(true);
        expect(response.body.data.name).toBe(newUser.name);
    });

    test('GET /api/users/:id should return a user', async () => {
        const response = await request(app)
            .get('/api/users/1')
            .expect(200);

        expect(response.body.data.id).toBe(1);
    });
});
```

---

## 🚀 **Deployment**

### **Environment Variables**

```javascript
require('dotenv').config();

const PORT = process.env.PORT || 3000;
const MONGODB_URI = process.env.MONGODB_URI;
const JWT_SECRET = process.env.JWT_SECRET;

app.listen(PORT, () => {
    console.log(`Server running on port ${PORT}`);
});
```

### **Production Configuration**

```javascript
const express = require('express');
const helmet = require('helmet');
const cors = require('cors');
const compression = require('compression');
const rateLimit = require('express-rate-limit');

const app = express();

// Security middleware
app.use(helmet());
app.use(cors());

// Performance middleware
app.use(compression());

// Rate limiting
const limiter = rateLimit({
    windowMs: 15 * 60 * 1000, // 15 minutes
    max: 100 // limit each IP to 100 requests per windowMs
});

app.use('/api/', limiter);

// Trust proxy (for load balancers)
app.set('trust proxy', 1);

// Production error handling
if (process.env.NODE_ENV === 'production') {
    app.use((err, req, res, next) => {
        console.error(err.stack);
        res.status(500).json({ error: 'Something went wrong!' });
    });
}
```

---

## 📚 **Learning Resources**

### **Documentation**
- [Node.js Documentation](https://nodejs.org/docs/)
- [Express.js Guide](https://expressjs.com/)
- [MongoDB Documentation](https://docs.mongodb.com/)

### **Courses**
- [Node.js Course](https://www.udemy.com/course/nodejs-the-complete-guide/)
- [REST API Course](https://www.udemy.com/course/rest-api-nodejs-express-mongodb/)
- [Backend Development](https://frontendmasters.com/courses/backend/)

### **Practice**
- [REST API Tutorial](https://restfulapi.net/)
- [Node.js Best Practices](https://github.com/goldbergyoni/nodebestpractices)

---

**Ready to master backend development? Start building APIs!** 🚀

---

*Last Updated: 2024*