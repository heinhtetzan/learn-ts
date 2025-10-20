## Implicit Typing

**Implicit Typing** (also known as **Type Inference**) is a feature where the TypeScript compiler automatically determines and assigns data types to variables, functions, and expressions based on their initial values, usage context, and assigned values, without requiring explicit type annotations from the developer.

### Key Characteristics:
- **Automatic**: TypeScript intelligently infers types without manual intervention
- **Context-based**: Uses initial assignments and usage patterns to determine types
- **Less verbose**: Reduces code clutter by eliminating type annotations
- **Compile-time**: Type checking happens during compilation while maintaining JavaScript's runtime behavior

## Explicit Typing

**Explicit Typing** is the practice of manually specifying and declaring data types for variables, function parameters, return types, and other constructs using type annotations, providing clear, intentional type definitions in the code.

### Key Characteristics:
- **Manual declaration**: Developers explicitly write type annotations
- **Intentional**: Clearly communicates the intended data type
- **Verbose**: Requires more code but offers clarity
- **Enforced**: TypeScript strictly enforces the declared types

## Core Difference

The fundamental difference lies in **who specifies the type**:
- **Implicit**: TypeScript automatically infers types
- **Explicit**: Developer manually declares types

Both approaches provide the same type safety benefits, but implicit typing offers cleaner syntax while explicit typing provides clearer intent and documentation.

## Basic Primitive Types
```typescript
// Strings
const firstName = "John";
const lastName = 'Doe';
const message = `Hello, ${firstName}!`;
const filePath = "src/components/header.tsx";

// Numbers
const age = 25;
const price = 19.99;
const quantity = 100;
const temperature = -5.5;

// Booleans
const isActive = true;
const isLoading = false;
const hasPermission = true;
```

## Arrays
```typescript
// Number arrays
const scores = [95, 87, 92, 78];
const temperatures = [72, 68, 75, 70, 69];
const coordinates = [10.5, 20.3, 15.7];

// String arrays
const fruits = ["apple", "banana", "orange"];
const colors = ["red", "green", "blue", "yellow"];
const usernames = ["alice123", "bob456", "charlie789"];

// Mixed arrays (will be inferred as (string | number)[])
const mixedArray = ["hello", 42, "world", 100];
```

## Objects
```typescript
// Simple objects
const user = {
    id: 1,
    name: "Alice",
    email: "alice@example.com",
    isActive: true
};

const product = {
    title: "Laptop",
    price: 999.99,
    inStock: true,
    specifications: {
        ram: "16GB",
        storage: "512GB SSD",
        display: "15.6 inch"
    }
};

const car = {
    make: "Toyota",
    model: "Camry",
    year: 2023,
    features: ["GPS", "Bluetooth", "Backup Camera"]
};
```

## Nested Objects
```typescript
const company = {
    name: "Tech Corp",
    employees: [
        {
            id: 1,
            name: "John",
            department: "Engineering",
            projects: ["Website", "Mobile App"]
        },
        {
            id: 2,
            name: "Sarah", 
            department: "Marketing",
            projects: ["Campaign 2024"]
        }
    ],
    address: {
        street: "123 Main St",
        city: "San Francisco",
        country: "USA"
    }
};
```

## Functions with Implicit Return Types
```typescript
// Arrow functions
const add = (a: number, b: number) => a + b;
const multiply = (x: number, y: number) => x * y;
const greet = (name: string) => `Hello, ${name}!`;
const isAdult = (age: number) => age >= 18;

// Regular functions
function calculateTotal(prices: number[]) {
    return prices.reduce((sum, price) => sum + price, 0);
}

function formatName(first: string, last: string) {
    return `${first} ${last}`.trim();
}

function getActiveUsers(users: { isActive: boolean }[]) {
    return users.filter(user => user.isActive);
}
```

## Complex Data Structures
```typescript
// Array of objects
const students = [
    { id: 1, name: "John", grade: "A", subjects: ["Math", "Science"] },
    { id: 2, name: "Jane", grade: "B", subjects: ["History", "English"] },
    { id: 3, name: "Bob", grade: "A", subjects: ["Math", "Physics"] }
];

// Nested arrays
const matrix = [
    [1, 2, 3],
    [4, 5, 6],
    [7, 8, 9]
];

const weeklyTemperatures = [
    [72, 68, 70], // Monday
    [75, 72, 74], // Tuesday
    [69, 67, 68]  // Wednesday
];
```

## Real-world Examples
```typescript
// E-commerce cart item
const cartItem = {
    productId: "prod_123",
    name: "Wireless Headphones",
    price: 149.99,
    quantity: 2,
    inStock: true,
    attributes: {
        color: "black",
        wireless: true,
        batteryLife: "20 hours"
    }
};

// Blog post
const blogPost = {
    id: "post_1",
    title: "Getting Started with TypeScript",
    content: "TypeScript is a typed superset of JavaScript...",
    author: {
        name: "Jane Smith",
        avatar: "avatar.jpg"
    },
    tags: ["typescript", "programming", "webdev"],
    publishedAt: new Date("2024-01-15"),
    metadata: {
        readTime: 5,
        wordCount: 1200,
        likes: 42
    }
};

// API response shape
const apiResponse = {
    status: "success",
    data: {
        users: [
            { id: 1, name: "Alice", role: "admin" },
            { id: 2, name: "Bob", role: "user" }
        ],
        pagination: {
            page: 1,
            totalPages: 5,
            totalItems: 45
        }
    },
    timestamp: "2024-01-15T10:30:00Z"
};
```

## Template Literals and Constants
```typescript
// Template literals
const fullName = `${firstName} ${lastName}`;
const welcomeMessage = `Welcome back, ${user.name}!`;
const apiUrl = `https://api.example.com/users/${user.id}`;

// Constants with complex values
const DEFAULT_SETTINGS = {
    theme: "dark",
    language: "en",
    notifications: true,
    fontSize: 16
};

const ERROR_MESSAGES = {
    NOT_FOUND: "Resource not found",
    UNAUTHORIZED: "Access denied", 
    VALIDATION_ERROR: "Invalid input data"
};
```

In all these examples, TypeScript automatically infers the types without any explicit type annotations, making the code cleaner while still maintaining type safety.