# 🌟 Web Development Best Practices

> **Industry Standards and Patterns for Professional Development**

---

## 📋 **Table of Contents**

1. [Code Quality](#code-quality)
2. [Performance](#performance)
3. [Security](#security)
4. [Accessibility](#accessibility)
5. [SEO](#seo)
6. [Git Best Practices](#git-best-practices)
7. [Testing Best Practices](#testing-best-practices)

---

## 💎 **Code Quality**

### **Clean Code Principles**

```javascript
// ❌ Bad
function d(u) {
    let r = 0;
    for (let i = 0; i < u.length; i++) {
        r += u[i];
    }
    return r;
}

// ✅ Good
function calculateSum(numbers: number[]): number {
    return numbers.reduce((sum, num) => sum + num, 0);
}
```

### **Naming Conventions**

```javascript
// Variables: camelCase
const userName = 'John';
const isLoggedIn = true;

// Constants: UPPER_SNAKE_CASE
const API_URL = 'https://api.example.com';
const MAX_RETRIES = 3;

// Functions: camelCase, descriptive verbs
function getUserById(id: number) { }
function calculateTotalPrice(items: Item[]) { }

// Classes: PascalCase
class UserService { }
class DatabaseConnection { }

// Interfaces: PascalCase, start with 'I' (optional)
interface IUser { }
interface UserRepository { }

// Types: PascalCase
type UserRole = 'admin' | 'user';
type UserStatus = 'active' | 'inactive';
```

### **DRY (Don't Repeat Yourself)**

```javascript
// ❌ Bad - Repeated code
function calculateAreaCircle(radius: number): number {
    return Math.PI * radius * radius;
}

function calculateCircumferenceCircle(radius: number): number {
    return 2 * Math.PI * radius;
}

// ✅ Good - Extract common logic
const PI = Math.PI;

function calculateCircleMeasurement(radius: number, type: 'area' | 'circumference'): number {
    return type === 'area' ? PI * radius * radius : 2 * PI * radius;
}
```

### **Single Responsibility Principle**

```javascript
// ❌ Bad - Multiple responsibilities
function processUserData(userData: any) {
    // Validate
    if (!userData.name || !userData.email) {
        throw new Error('Invalid data');
    }

    // Save to database
    const db = connectToDatabase();
    db.save(userData);

    // Send email
    sendEmail(userData.email, 'Welcome!');

    // Log
    console.log('User processed');
}

// ✅ Good - Separate responsibilities
function validateUserData(userData: any): boolean {
    return !!(userData.name && userData.email);
}

function saveUserToDatabase(userData: any): void {
    const db = connectToDatabase();
    db.save(userData);
}

function sendWelcomeEmail(email: string): void {
    sendEmail(email, 'Welcome!');
}

function logUserProcessed(userData: any): void {
    console.log('User processed:', userData.name);
}

function processUserData(userData: any): void {
    if (!validateUserData(userData)) {
        throw new Error('Invalid data');
    }

    saveUserToDatabase(userData);
    sendWelcomeEmail(userData.email);
    logUserProcessed(userData);
}
```

---

## ⚡ **Performance**

### **Frontend Performance**

```javascript
// Lazy loading images
<img src="placeholder.jpg" data-src="actual-image.jpg" loading="lazy" alt="Image">

// Code splitting
const LazyComponent = React.lazy(() => import('./LazyComponent'));

// Debounce function
function debounce(func: Function, wait: number) {
    let timeout: NodeJS.Timeout;
    return function executedFunction(...args: any[]) {
        const later = () => {
            clearTimeout(timeout);
            func(...args);
        };
        clearTimeout(timeout);
        timeout = setTimeout(later, wait);
    };
}

// Memoization
const memoizedValue = useMemo(() => computeExpensiveValue(a, b), [a, b]);
```

### **Backend Performance**

```javascript
// Database indexing
CREATE INDEX idx_users_email ON users(email);

// Query optimization
// ❌ Bad - N+1 problem
const users = await User.find();
for (const user of users) {
    const posts = await Post.find({ userId: user.id });
}

// ✅ Good - Single query with populate
const users = await User.find().populate('posts');

// Caching
const cache = new Map();

async function getUserWithCache(id: string) {
    if (cache.has(id)) {
        return cache.get(id);
    }

    const user = await User.findById(id);
    cache.set(id, user);
    return user;
}

// Pagination
async function getUsersPaginated(page: number, limit: number) {
    const skip = (page - 1) * limit;
    return User.find().skip(skip).limit(limit);
}
```

---

## 🔒 **Security**

### **Input Validation**

```javascript
// Validate user input
import { body, validationResult } from 'express-validator';

app.post('/api/users',
    [
        body('name').trim().notEmpty().withMessage('Name is required'),
        body('email').isEmail().withMessage('Invalid email'),
        body('password').isLength({ min: 8 }).withMessage('Password must be at least 8 characters')
    ],
    (req, res) => {
        const errors = validationResult(req);
        if (!errors.isEmpty()) {
            return res.status(400).json({ errors: errors.array() });
        }
        // Process valid data
    }
);
```

### **SQL Injection Prevention**

```javascript
// ❌ Bad - Vulnerable to SQL injection
const query = `SELECT * FROM users WHERE email = '${email}'`;

// ✅ Good - Parameterized queries
const query = 'SELECT * FROM users WHERE email = $1';
const result = await pool.query(query, [email]);
```

### **XSS Prevention**

```javascript
// Sanitize user input
import DOMPurify from 'dompurify';

const cleanInput = DOMPurify.sanitize(userInput);

// React automatically escapes JSX
const message = <div>{userInput}</div>;
```

### **Authentication Best Practices**

```javascript
// Hash passwords
import bcrypt from 'bcrypt';

const hashedPassword = await bcrypt.hash(password, 10);
const isValid = await bcrypt.compare(password, hashedPassword);

// Use JWT for authentication
import jwt from 'jsonwebtoken';

const token = jwt.sign(
    { userId: user.id },
    process.env.JWT_SECRET,
    { expiresIn: '24h' }
);

// Validate token
const decoded = jwt.verify(token, process.env.JWT_SECRET);
```

---

## ♿ **Accessibility**

### **Semantic HTML**

```html
<!-- Use semantic elements -->
<header>
    <nav>
        <ul>
            <li><a href="/">Home</a></li>
            <li><a href="/about">About</a></li>
        </ul>
    </nav>
</header>

<main>
    <article>
        <h1>Article Title</h1>
        <p>Article content...</p>
    </article>
</main>

<footer>
    <p>&copy; 2024</p>
</footer>
```

### **ARIA Labels**

```html
<!-- Accessible buttons -->
<button aria-label="Close dialog">×</button>

<!-- Accessible forms -->
<label for="email">Email:</label>
<input type="email" id="email" aria-required="true">

<!-- Accessible navigation -->
<nav aria-label="Main navigation">
    <ul>
        <li><a href="/">Home</a></li>
    </ul>
</nav>
```

### **Keyboard Navigation**

```css
/* Focus styles */
button:focus {
    outline: 2px solid #007bff;
    outline-offset: 2px;
}

/* Skip links */
.skip-link {
    position: absolute;
    top: -40px;
    left: 0;
    background: #007bff;
    color: white;
    padding: 8px;
    z-index: 100;
}

.skip-link:focus {
    top: 0;
}
```

---

## 🔍 **SEO**

### **Meta Tags**

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <meta name="description" content="Your page description">
    <meta name="keywords" content="your, keywords, here">
    <meta name="author" content="Your Name">

    <!-- Open Graph -->
    <meta property="og:title" content="Your Page Title">
    <meta property="og:description" content="Your page description">
    <meta property="og:image" content="https://example.com/image.jpg">
    <meta property="og:url" content="https://example.com">

    <!-- Twitter Card -->
    <meta name="twitter:card" content="summary_large_image">
    <meta name="twitter:title" content="Your Page Title">
    <meta name="twitter:description" content="Your page description">
    <meta name="twitter:image" content="https://example.com/image.jpg">

    <title>Your Page Title</title>
</head>
```

### **Structured Data**

```html
<script type="application/ld+json">
{
  "@context": "https://schema.org",
  "@type": "Article",
  "headline": "Your Article Title",
  "author": {
    "@type": "Person",
    "name": "Author Name"
  },
  "datePublished": "2024-01-01",
  "description": "Article description"
}
</script>
```

---

## 📦 **Git Best Practices**

### **Commit Messages**

```bash
# Conventional Commits format
<type>(<scope>): <subject>

<body>

<footer>

# Types
feat:     New feature
fix:      Bug fix
docs:     Documentation changes
style:    Code style changes
refactor: Code refactoring
test:     Test changes
chore:    Maintenance tasks

# Examples
feat(auth): add JWT authentication
fix(api): resolve user creation bug
docs(readme): update installation instructions
```

### **Branch Strategy**

```bash
# Main branches
main      # Production code
develop   # Development code

# Feature branches
feature/user-authentication
feature/payment-integration

# Bugfix branches
fix/login-error
fix/database-connection

# Release branches
release/v1.0.0
```

---

## 🧪 **Testing Best Practices**

### **Test Organization**

```javascript
// Arrange, Act, Assert pattern
describe('UserService', () => {
    describe('createUser', () => {
        it('should create a new user', async () => {
            // Arrange
            const userData = {
                name: 'John',
                email: 'john@example.com'
            };

            // Act
            const user = await userService.createUser(userData);

            // Assert
            expect(user.name).toBe('John');
            expect(user.email).toBe('john@example.com');
            expect(user.id).toBeDefined();
        });
    });
});
```

### **Test Coverage**

```javascript
// Aim for 80%+ coverage
// Test happy paths
// Test error cases
// Test edge cases
// Test integration points
```

---

## 📚 **Learning Resources**

### **Books**
- [Clean Code](https://www.amazon.com/Clean-Code-Handbook-Software-Craftsmanship/dp/0132350882)
- [The Pragmatic Programmer](https://www.amazon.com/Pragmatic-Programmer-Journey-Mastery/dp/020161622X)

### **Articles**
- [12 Factor App](https://12factor.net/)
- [Google Style Guides](https://google.github.io/styleguide/)

---

**Ready to write professional code? Follow these best practices!** 🚀

---

*Last Updated: 2024*