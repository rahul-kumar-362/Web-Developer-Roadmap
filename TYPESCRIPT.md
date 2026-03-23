# 🔷 TypeScript Development Guide

> **Master Type-Safe JavaScript Development**

---

## 📋 **Table of Contents**

1. [TypeScript Fundamentals](#typescript-fundamentals)
2. [Basic Types](#basic-types)
3. [Interfaces & Types](#interfaces--types)
4. [Classes](#classes)
5. [Generics](#generics)
6. [Utility Types](#utility-types)
7. [TypeScript with React](#typescript-with-react)
8. [TypeScript with Node.js](#typescript-with-nodejs)

---

## 🎯 **TypeScript Fundamentals**

### **What is TypeScript?**

TypeScript is a typed superset of JavaScript that compiles to plain JavaScript. It adds static type definitions and provides better tooling and error checking.

### **Setting Up TypeScript**

```bash
# Install TypeScript
npm install -g typescript

# Initialize TypeScript project
tsc --init

# Install TypeScript in project
npm install --save-dev typescript @types/node @types/react

# Compile TypeScript
tsc

# Watch mode
tsc --watch
```

### **tsconfig.json**

```json
{
  "compilerOptions": {
    "target": "ES2020",
    "module": "commonjs",
    "lib": ["ES2020"],
    "outDir": "./dist",
    "rootDir": "./src",
    "strict": true,
    "esModuleInterop": true,
    "skipLibCheck": true,
    "forceConsistentCasingInFileNames": true,
    "resolveJsonModule": true,
    "moduleResolution": "node",
    "jsx": "react"
  },
  "include": ["src/**/*"],
  "exclude": ["node_modules"]
}
```

---

## 📝 **Basic Types**

### **Primitive Types**

```typescript
// String
let name: string = "John";
let greeting: string = `Hello, ${name}`;

// Number
let age: number = 30;
let price: number = 19.99;

// Boolean
let isActive: boolean = true;
let hasPermission: boolean = false;

// Null and Undefined
let nothing: null = null;
let notDefined: undefined = undefined;

// Any (avoid using)
let anything: any = "could be anything";

// Unknown (better than any)
let value: unknown = "could be anything";
if (typeof value === "string") {
    console.log(value.toUpperCase());
}

// Void
function log(message: string): void {
    console.log(message);
}

// Never
function throwError(message: string): never {
    throw new Error(message);
}
```

### **Array Types**

```typescript
// Array of strings
let names: string[] = ["John", "Jane", "Bob"];
let names2: Array<string> = ["John", "Jane", "Bob"];

// Array of numbers
let numbers: number[] = [1, 2, 3, 4, 5];

// Mixed array
let mixed: (string | number)[] = [1, "two", 3, "four"];

// Readonly array
const readonlyArray: readonly number[] = [1, 2, 3];
// readonlyArray.push(4); // Error

// Tuple
let tuple: [string, number] = ["John", 30];
let tuple2: [string, number, boolean] = ["Jane", 25, true];
```

### **Object Types**

```typescript
// Object type
let user: {
    name: string;
    age: number;
    email?: string; // Optional property
} = {
    name: "John",
    age: 30
};

// Readonly properties
let config: {
    readonly apiUrl: string;
    timeout: number;
} = {
    apiUrl: "https://api.example.com",
    timeout: 5000
};

// Index signature
let dictionary: {
    [key: string]: number;
} = {
    one: 1,
    two: 2
};
```

---

## 🎨 **Interfaces & Types**

### **Interfaces**

```typescript
// Basic interface
interface User {
    id: number;
    name: string;
    email: string;
    age?: number; // Optional
}

// Using interface
const user: User = {
    id: 1,
    name: "John",
    email: "john@example.com"
};

// Interface with methods
interface Animal {
    name: string;
    speak(): void;
    eat(food: string): void;
}

class Dog implements Animal {
    name: string;

    constructor(name: string) {
        this.name = name;
    }

    speak(): void {
        console.log(`${this.name} says: Woof!`);
    }

    eat(food: string): void {
        console.log(`${this.name} is eating ${food}`);
    }
}

// Extending interfaces
interface Person {
    name: string;
    age: number;
}

interface Employee extends Person {
    employeeId: number;
    department: string;
}

const employee: Employee = {
    name: "John",
    age: 30,
    employeeId: 123,
    department: "Engineering"
};
```

### **Type Aliases**

```typescript
// Type alias
type ID = number;
type Name = string;
type User = {
    id: ID;
    name: Name;
    email: string;
};

// Union types
type Status = "active" | "inactive" | "pending";
type Role = "user" | "admin" | "moderator";

type UserWithStatus = User & {
    status: Status;
    role: Role;
};

// Using union types
function setStatus(status: Status): void {
    console.log(`Status set to: ${status}`);
}

setStatus("active"); // OK
// setStatus("unknown"); // Error

// Intersection types
type Person = {
    name: string;
    age: number;
};

type Employee = {
    employeeId: number;
    department: string;
};

type EmployeePerson = Person & Employee;

const employee: EmployeePerson = {
    name: "John",
    age: 30,
    employeeId: 123,
    department: "Engineering"
};
```

---

## 🏗️ **Classes**

```typescript
// Basic class
class Person {
    name: string;
    age: number;

    constructor(name: string, age: number) {
        this.name = name;
        this.age = age;
    }

    greet(): string {
        return `Hello, my name is ${this.name}`;
    }
}

// Access modifiers
class BankAccount {
    private balance: number;
    protected accountNumber: string;
    public owner: string;

    constructor(owner: string, accountNumber: string) {
        this.owner = owner;
        this.accountNumber = accountNumber;
        this.balance = 0;
    }

    public deposit(amount: number): void {
        this.balance += amount;
    }

    public withdraw(amount: number): void {
        if (amount <= this.balance) {
            this.balance -= amount;
        }
    }

    private validateAmount(amount: number): boolean {
        return amount > 0;
    }
}

// Inheritance
class Animal {
    name: string;

    constructor(name: string) {
        this.name = name;
    }

    speak(): void {
        console.log(`${this.name} makes a sound`);
    }
}

class Dog extends Animal {
    breed: string;

    constructor(name: string, breed: string) {
        super(name);
        this.breed = breed;
    }

    speak(): void {
        console.log(`${this.name} barks`);
    }

    fetch(): void {
        console.log(`${this.name} fetches the ball`);
    }
}

// Abstract classes
abstract class Shape {
    abstract area(): number;
    abstract perimeter(): number;

    describe(): void {
        console.log("This is a shape");
    }
}

class Rectangle extends Shape {
    width: number;
    height: number;

    constructor(width: number, height: number) {
        super();
        this.width = width;
        this.height = height;
    }

    area(): number {
        return this.width * this.height;
    }

    perimeter(): number {
        return 2 * (this.width + this.height);
    }
}
```

---

## 🔧 **Generics**

```typescript
// Generic function
function identity<T>(arg: T): T {
    return arg;
}

const num = identity<number>(42);
const str = identity<string>("Hello");

// Generic interface
interface Box<T> {
    value: T;
}

const numberBox: Box<number> = { value: 42 };
const stringBox: Box<string> = { value: "Hello" };

// Generic class
class Storage<T> {
    private items: T[] = [];

    add(item: T): void {
        this.items.push(item);
    }

    get(index: number): T | undefined {
        return this.items[index];
    }

    getAll(): T[] {
        return [...this.items];
    }
}

const numberStorage = new Storage<number>();
numberStorage.add(1);
numberStorage.add(2);

const stringStorage = new Storage<string>();
stringStorage.add("Hello");
stringStorage.add("World");

// Generic constraints
interface Lengthwise {
    length: number;
}

function getLength<T extends Lengthwise>(arg: T): number {
    return arg.length;
}

getLength("Hello"); // OK
getLength([1, 2, 3]); // OK
// getLength(42); // Error

// Multiple type parameters
function pair<T, U>(first: T, second: U): [T, U] {
    return [first, second];
}

const p1 = pair<string, number>("Hello", 42);
const p2 = pair<boolean, string>(true, "World");
```

---

## 🛠️ **Utility Types**

```typescript
// Partial - makes all properties optional
interface User {
    id: number;
    name: string;
    email: string;
}

type PartialUser = Partial<User>;
const partialUser: PartialUser = {
    name: "John"
};

// Required - makes all properties required
type RequiredUser = Required<PartialUser>;

// Readonly - makes all properties readonly
type ReadonlyUser = Readonly<User>;

// Pick - pick specific properties
type UserBasicInfo = Pick<User, "id" | "name">;

// Omit - omit specific properties
type UserWithoutEmail = Omit<User, "email">;

// Record - create object type with specific keys
type UserRecord = Record<string, User>;

// Exclude - exclude types from union
type Status = "active" | "inactive" | "pending";
type ActiveStatus = Exclude<Status, "inactive" | "pending">;

// Extract - extract types from union
type StringOrNumber = string | number;
type StringType = Extract<StringOrNumber, string>;

// NonNullable - remove null and undefined
type NonNullableString = NonNullable<string | null | undefined>;

// ReturnType - get return type of function
function getUser(): User {
    return { id: 1, name: "John", email: "john@example.com" };
}

type UserReturnType = ReturnType<typeof getUser>;

// Parameters - get parameter types of function
function createUser(name: string, email: string): User {
    return { id: 1, name, email };
}

type CreateUserParams = Parameters<typeof createUser>;
```

---

## ⚛️ **TypeScript with React**

### **Component Props**

```typescript
// Basic props
interface ButtonProps {
    text: string;
    onClick: () => void;
    disabled?: boolean;
}

function Button({ text, onClick, disabled = false }: ButtonProps) {
    return (
        <button onClick={onClick} disabled={disabled}>
            {text}
        </button>
    );
}

// Props with children
interface CardProps {
    title: string;
    children: React.ReactNode;
}

function Card({ title, children }: CardProps) {
    return (
        <div className="card">
            <h2>{title}</h2>
            {children}
        </div>
    );
}

// Generic component
interface ListProps<T> {
    items: T[];
    renderItem: (item: T) => React.ReactNode;
}

function List<T>({ items, renderItem }: ListProps<T>) {
    return (
        <ul>
            {items.map((item, index) => (
                <li key={index}>{renderItem(item)}</li>
            ))}
        </ul>
    );
}

// Using generic component
interface User {
    id: number;
    name: string;
}

function UserList() {
    const users: User[] = [
        { id: 1, name: "John" },
        { id: 2, name: "Jane" }
    ];

    return (
        <List
            items={users}
            renderItem={(user) => <span>{user.name}</span>}
        />
    );
}
```

### **Hooks with TypeScript**

```typescript
// useState with type
const [count, setCount] = useState<number>(0);
const [user, setUser] = useState<User | null>(null);

// useEffect with type
useEffect(() => {
    const timer = setInterval(() => {
        console.log("Timer");
    }, 1000);

    return () => clearInterval(timer);
}, []);

// useRef with type
const inputRef = useRef<HTMLInputElement>(null);

// Custom hook with type
function useFetch<T>(url: string): {
    data: T | null;
    loading: boolean;
    error: string | null;
} {
    const [data, setData] = useState<T | null>(null);
    const [loading, setLoading] = useState(true);
    const [error, setError] = useState<string | null>(null);

    useEffect(() => {
        async function fetchData() {
            try {
                const response = await fetch(url);
                const json = await response.json();
                setData(json);
            } catch (err) {
                setError(err instanceof Error ? err.message : "Unknown error");
            } finally {
                setLoading(false);
            }
        }

        fetchData();
    }, [url]);

    return { data, loading, error };
}

// Using custom hook
interface User {
    id: number;
    name: string;
}

function UserProfile() {
    const { data: user, loading, error } = useFetch<User>("/api/user/1");

    if (loading) return <div>Loading...</div>;
    if (error) return <div>Error: {error}</div>;
    if (!user) return <div>No user found</div>;

    return <div>{user.name}</div>;
}
```

---

## 🟢 **TypeScript with Node.js**

### **Express with TypeScript**

```typescript
import express, { Request, Response } from 'express';

const app = express();

interface User {
    id: number;
    name: string;
    email: string;
}

// GET route
app.get('/api/users', (req: Request, res: Response) => {
    const users: User[] = [
        { id: 1, name: "John", email: "john@example.com" },
        { id: 2, name: "Jane", email: "jane@example.com" }
    ];
    res.json(users);
});

// POST route
app.post('/api/users', (req: Request, res: Response) => {
    const { name, email }: { name: string; email: string } = req.body;

    const newUser: User = {
        id: Date.now(),
        name,
        email
    };

    res.status(201).json(newUser);
});

// Dynamic route
app.get('/api/users/:id', (req: Request, res: Response) => {
    const id: number = parseInt(req.params.id);
    const user: User | undefined = users.find(u => u.id === id);

    if (!user) {
        return res.status(404).json({ error: "User not found" });
    }

    res.json(user);
});

app.listen(3000, () => {
    console.log('Server running on port 3000');
});
```

---

## 📚 **Learning Resources**

### **Documentation**
- [TypeScript Handbook](https://www.typescriptlang.org/docs/)
- [TypeScript Deep Dive](https://basarat.gitbook.io/typescript/)
- [TypeScript with React](https://react-typescript-cheatsheet.netlify.app/)

### **Courses**
- [TypeScript Course](https://www.udemy.com/course/understanding-typescript/)
- [Advanced TypeScript](https://frontendmasters.com/courses/typescript/)

---

**Ready to master TypeScript? Start adding types to your code!** 🚀

---

*Last Updated: 2024*