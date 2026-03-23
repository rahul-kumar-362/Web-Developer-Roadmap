# 🚀 Next.js Development Guide

> **Master Server-Side Rendering, Static Generation, and Modern React Development**

---

## 📋 **Table of Contents**

1. [Next.js Fundamentals](#nextjs-fundamentals)
2. [Pages & Routing](#pages--routing)
3. [Server-Side Rendering](#server-side-rendering)
4. [Static Generation](#static-generation)
5. [API Routes](#api-routes)
6. [Data Fetching](#data-fetching)
7. [Image Optimization](#image-optimization)
8. [Deployment](#deployment)

---

## 🎯 **Next.js Fundamentals**

### **What is Next.js?**

Next.js is a React framework that provides:
- **Server-Side Rendering (SSR)**
- **Static Site Generation (SSG)**
- **API Routes**
- **File-based Routing**
- **Image Optimization**
- **Code Splitting**

### **Setting Up Next.js**

```bash
# Create Next.js app
npx create-next-app@latest my-app

# With TypeScript
npx create-next-app@latest my-app --typescript

# With Tailwind CSS
npx create-next-app@latest my-app --tailwind

# Start development server
cd my-app
npm run dev
```

### **Project Structure**

```
my-app/
├── app/                    # App Router (Next.js 13+)
│   ├── layout.tsx         # Root layout
│   ├── page.tsx           # Home page
│   ├── about/
│   │   └── page.tsx       # About page
│   └── api/               # API routes
│       └── hello/
│           └── route.ts    # API endpoint
├── public/                # Static files
├── components/            # Reusable components
├── lib/                   # Utility functions
└── styles/                # Global styles
```

---

## 📄 **Pages & Routing**

### **File-Based Routing**

```tsx
// app/page.tsx - Home page
export default function Home() {
    return (
        <main>
            <h1>Welcome to Next.js</h1>
        </main>
    );
}

// app/about/page.tsx - About page
export default function About() {
    return (
        <main>
            <h1>About Us</h1>
        </main>
    );
}

// app/blog/[slug]/page.tsx - Dynamic route
export default function BlogPost({ params }: { params: { slug: string } }) {
    return (
        <main>
            <h1>Blog Post: {params.slug}</h1>
        </main>
    );
}
```

### **Layouts**

```tsx
// app/layout.tsx - Root layout
import './globals.css';

export default function RootLayout({
    children,
}: {
    children: React.ReactNode;
}) {
    return (
        <html lang="en">
            <body>
                <nav>
                    <a href="/">Home</a>
                    <a href="/about">About</a>
                </nav>
                {children}
                <footer>© 2024</footer>
            </body>
        </html>
    );
}

// app/dashboard/layout.tsx - Nested layout
export default function DashboardLayout({
    children,
}: {
    children: React.ReactNode;
}) {
    return (
        <div className="dashboard">
            <aside>Sidebar</aside>
            <main>{children}</main>
        </div>
    );
}
```

### **Navigation**

```tsx
import Link from 'next/link';
import { useRouter } from 'next/navigation';

export default function Navigation() {
    const router = useRouter();

    const handleClick = () => {
        router.push('/about');
    };

    return (
        <nav>
            <Link href="/">Home</Link>
            <Link href="/about">About</Link>
            <button onClick={handleClick}>Go to About</button>
        </nav>
    );
}
```

---

## 🖥️ **Server-Side Rendering (SSR)**

### **Dynamic Rendering**

```tsx
// app/products/[id]/page.tsx
async function getProduct(id: string) {
    const res = await fetch(`https://api.example.com/products/${id}`);
    if (!res.ok) throw new Error('Failed to fetch product');
    return res.json();
}

export default async function ProductPage({
    params,
}: {
    params: { id: string };
}) {
    const product = await getProduct(params.id);

    return (
        <div>
            <h1>{product.name}</h1>
            <p>{product.description}</p>
            <p>Price: ${product.price}</p>
        </div>
    );
}
```

### **Force Dynamic Rendering**

```tsx
// Force dynamic rendering (no caching)
export const dynamic = 'force-dynamic';

export default async function DynamicPage() {
    const data = await fetch('https://api.example.com/data', {
        cache: 'no-store'
    });
    const json = await data.json();

    return <div>{JSON.stringify(json)}</div>;
}
```

---

## 📦 **Static Generation (SSG)**

### **Static Pages**

```tsx
// app/blog/page.tsx - Static page
export default async function BlogPage() {
    const posts = await getPosts();

    return (
        <div>
            {posts.map(post => (
                <article key={post.id}>
                    <h2>{post.title}</h2>
                    <p>{post.excerpt}</p>
                </article>
            ))}
        </div>
    );
}

// Force static generation
export const dynamic = 'force-static';
```

### **Static Params**

```tsx
// app/blog/[slug]/page.tsx - Static generation with params
export async function generateStaticParams() {
    const posts = await getPosts();
    return posts.map((post) => ({
        slug: post.slug,
    }));
}

export default async function BlogPost({
    params,
}: {
    params: { slug: string };
}) {
    const post = await getPost(params.slug);

    return (
        <article>
            <h1>{post.title}</h1>
            <div>{post.content}</div>
        </article>
    );
}
```

### **Incremental Static Regeneration (ISR)**

```tsx
// Revalidate every 60 seconds
export const revalidate = 60;

export default async function BlogPage() {
    const posts = await getPosts();

    return (
        <div>
            {posts.map(post => (
                <article key={post.id}>
                    <h2>{post.title}</h2>
                </article>
            ))}
        </div>
    );
}
```

---

## 🔌 **API Routes**

### **GET Request**

```tsx
// app/api/hello/route.ts
import { NextResponse } from 'next/server';

export async function GET() {
    return NextResponse.json({
        message: 'Hello, World!',
        timestamp: new Date().toISOString(),
    });
}
```

### **POST Request**

```tsx
// app/api/users/route.ts
import { NextResponse } from 'next/server';

export async function POST(request: Request) {
    const body = await request.json();
    const { name, email } = body;

    // Validate input
    if (!name || !email) {
        return NextResponse.json(
            { error: 'Name and email are required' },
            { status: 400 }
        );
    }

    // Create user
    const user = await createUser({ name, email });

    return NextResponse.json(user, { status: 201 });
}
```

### **Dynamic API Routes**

```tsx
// app/api/users/[id]/route.ts
import { NextResponse } from 'next/server';

export async function GET(
    request: Request,
    { params }: { params: { id: string } }
) {
    const user = await getUser(params.id);

    if (!user) {
        return NextResponse.json(
            { error: 'User not found' },
            { status: 404 }
        );
    }

    return NextResponse.json(user);
}

export async function PUT(
    request: Request,
    { params }: { params: { id: string } }
) {
    const body = await request.json();
    const user = await updateUser(params.id, body);

    return NextResponse.json(user);
}

export async function DELETE(
    request: Request,
    { params }: { params: { id: string } }
) {
    await deleteUser(params.id);

    return NextResponse.json({ message: 'User deleted' });
}
```

---

## 📊 **Data Fetching**

### **Server Components**

```tsx
// Server component (default)
async function UserList() {
    const users = await fetch('https://api.example.com/users').then(res =>
        res.json()
    );

    return (
        <ul>
            {users.map((user: any) => (
                <li key={user.id}>{user.name}</li>
            ))}
        </ul>
    );
}
```

### **Client Components**

```tsx
'use client';

import { useState, useEffect } from 'react';

export default function UserList() {
    const [users, setUsers] = useState([]);
    const [loading, setLoading] = useState(true);

    useEffect(() => {
        async function fetchUsers() {
            const res = await fetch('https://api.example.com/users');
            const data = await res.json();
            setUsers(data);
            setLoading(false);
        }

        fetchUsers();
    }, []);

    if (loading) return <div>Loading...</div>;

    return (
        <ul>
            {users.map((user: any) => (
                <li key={user.id}>{user.name}</li>
            ))}
        </ul>
    );
}
```

### **Server Actions**

```tsx
'use server';

import { revalidatePath } from 'next/cache';

export async function createUser(formData: FormData) {
    const name = formData.get('name') as string;
    const email = formData.get('email') as string;

    // Create user in database
    await db.users.create({ data: { name, email } });

    // Revalidate the page
    revalidatePath('/users');

    return { success: true };
}
```

---

## 🖼️ **Image Optimization**

```tsx
import Image from 'next/image';

export default function ImageExample() {
    return (
        <div>
            {/* Local image */}
            <Image
                src="/hero.jpg"
                alt="Hero image"
                width={800}
                height={600}
                priority
            />

            {/* Remote image */}
            <Image
                src="https://example.com/image.jpg"
                alt="Remote image"
                width={400}
                height={300}
            />

            {/* Responsive image */}
            <Image
                src="/responsive.jpg"
                alt="Responsive image"
                fill
                sizes="(max-width: 768px) 100vw, (max-width: 1200px) 50vw, 33vw"
            />
        </div>
    );
}
```

---

## 🚀 **Deployment**

### **Vercel Deployment**

```bash
# Install Vercel CLI
npm i -g vercel

# Deploy
vercel

# Deploy to production
vercel --prod
```

### **Environment Variables**

```bash
# .env.local
DATABASE_URL=postgresql://...
NEXT_PUBLIC_API_URL=https://api.example.com
```

```tsx
// Using environment variables
const apiUrl = process.env.NEXT_PUBLIC_API_URL;
const dbUrl = process.env.DATABASE_URL;
```

---

## 📚 **Learning Resources**

### **Documentation**
- [Next.js Documentation](https://nextjs.org/docs)
- [Next.js Learn](https://nextjs.org/learn)
- [App Router Guide](https://nextjs.org/docs/app)

### **Courses**
- [Next.js Course](https://www.udemy.com/course/nextjs-react-the-complete-guide/)
- [Full-Stack Next.js](https://frontendmasters.com/courses/fullstack-nextjs/)

### **Examples**
- [Next.js Examples](https://github.com/vercel/next.js/tree/canary/examples)

---

**Ready to master Next.js? Start building server-rendered apps!** 🚀

---

*Last Updated: 2024*