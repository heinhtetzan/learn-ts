# TypeScript Types - Complete Reference with Rules & Definitions

## 1. **Basic Primitive Types**

### **Definition**: Fundamental data types built into TypeScript

```typescript
// STRING
let name: string = "John";
// 🚨 RULES: Must contain text data, can use '', "", or ``

// NUMBER
let age: number = 30;
let decimal: number = 42.5;
// 🚨 RULES: Can be integer, float, hexadecimal, binary, or octal

// BOOLEAN
let isActive: boolean = true;
// 🚨 RULES: Only true or false, no truthy/falsy values

// NULL
let nothing: null = null;
// 🚨 RULES: Must be explicitly assigned null

// UNDEFINED
let notDefined: undefined = undefined;
// 🚨 RULES: Must be explicitly assigned undefined

// BIGINT
let bigNumber: bigint = 9007199254740991n;
// 🚨 RULES: Must end with 'n', for integers larger than 2^53

// SYMBOL
let symbolValue: symbol = Symbol("id");
// 🚨 RULES: Unique and immutable primitive value
```

---

## 2. **Array Types**

### **Definition**: Ordered collections of values of specified type

```typescript
// Syntax 1: Type[]
let numbers: number[] = [1, 2, 3];
// 🚨 RULES: All elements must be of the specified type

// Syntax 2: Array<Type>
let strings: Array<string> = ["a", "b", "c"];
// 🚨 RULES: Generic syntax, same rules as above

// Readonly Array
const readOnlyNumbers: readonly number[] = [1, 2, 3];
// 🚨 RULES: Cannot modify array after creation

// Union Array
let mixed: (string | number)[] = [1, "hello"];
// 🚨 RULES: Elements can be any of the union types

// Example 1: Array with any type (flexible but loses type safety)
let flexibleArray: any[] = [1, "hello", true, { name: "John" }, [1, 2, 3]];
console.log(flexibleArray); // [1, "hello", true, {name: "John"}, [1, 2, 3]]

// Example 2: 2D array (array of arrays)
let matrix: number[][] = [
  [1, 2, 3],
  [4, 5, 6],
  [7, 8, 9]
];
console.log(matrix[1][2]); // 6

// Example 3: Mixed array with any and specific types
let data: (string | number | any)[] = ["text", 42, { key: "value" }, [1, 2], null];
console.log(data[2]); // {key: "value"}

// Example 5: Array that can contain any type using generic any array
let completelyFlexible: Array<any> = [
  123,
  "string",
  true,
  undefined,
  null,
  { object: true },
  function() { return "I'm a function"; },
  Symbol("symbol")
];
console.log(completelyFlexible.length); // 8

```

---

## 3. **Object Types**

### **Definition**: Structured data with defined properties and types

```typescript
// Inline object type
let person: { name: string; age: number } = { name: "John", age: 30 };
// 🚨 RULES: Must have all required properties with correct types

// Optional properties
let user: { name: string; email?: string } = { name: "John" };
// 🚨 RULES: Optional properties marked with ? can be omitted

// Readonly properties
let config: { readonly apiKey: string } = { apiKey: "123" };
// 🚨 RULES: Readonly properties cannot be modified after assignment

// Index signatures
let dictionary: { [key: string]: number } = { "one": 1, "two": 2 };
// 🚨 RULES: All properties must match the index signature type
```

---

## 4. **Tuple Types**

### **Definition**: Fixed-length arrays with known types at each position

```typescript
// Basic tuple
let coordinates: [number, number] = [10, 20];
// 🚨 RULES: Must have exactly 2 elements, both numbers

// Mixed tuple
let personInfo: [string, number, boolean] = ["John", 30, true];
// 🚨 RULES: Order and types must match exactly

// Optional tuple elements
let optionalTuple: [string, number?] = ["hello"];
// 🚨 RULES: Optional elements must be at the end

// Readonly tuple
const readOnlyTuple: readonly [string, number] = ["test", 42];
// 🚨 RULES: Cannot modify elements after creation
 
// Example 6: Tuple-like array with any positions
let config: [string, any, number] = ["server", { host: "localhost", port: 8080 }, 3];
console.log(config[1].host); // "localhost"

// Example 7: Array that starts with specific types but can have any additional elements
let mixedStart: [string, number, ...any[]] = ["start", 1, true, { extra: "data" }, [4, 5, 6]];
console.log(mixedStart[4]); // [4, 5, 6]
```

---

## 5. **Enum Types**

### **Definition**: Named constants that can be numeric or string-based

```typescript
// Numeric enum (auto-incrementing)
enum Color { Red, Green, Blue } // 0, 1, 2
let color: Color = Color.Red;
// 🚨 RULES: Values auto-increment from 0 if not specified

// String enum
enum Direction { Up = "UP", Down = "DOWN" }
let dir: Direction = Direction.Up;
// 🚨 RULES: All values must be explicitly initialized

// Const enum (removed during compilation)
const enum Size { Small, Medium, Large }
// 🚨 RULES: Cannot be used as types, only values
```

---

## 6. **Special Types**

### **Definition**: Types for specific use cases

```typescript
// ANY - opt out of type checking
let flexible: any = "could be anything";
flexible = 42; // ✅ Allowed
// 🚨 RULES: Can be assigned to/from any type, disables type checking

// UNKNOWN - type-safe counterpart of any
let uncertain: unknown = "might be anything";
// uncertain.toUpperCase(); // ❌ Error - needs type checking
// 🚨 RULES: Cannot be used without type narrowing

// NEVER - represents unreachable code
let neverValue: never = (() => { throw new Error() })();
// 🚨 RULES: Cannot have any value, used for functions that always throw

// VOID - absence of value
function logMessage(): void { console.log("hello"); }
// 🚨 RULES: Can only be undefined or null (with strictNullChecks)
```

---

## 7. **Union and Intersection Types**

### **Definition**: Combine multiple types

```typescript
// UNION - can be one of several types
let id: string | number = "123";
id = 456; // ✅ Allowed
// 🚨 RULES: Value must satisfy at least one of the types

// INTERSECTION - combines multiple types
type Name = { name: string };
type Age = { age: number };
type Person = Name & Age;

let person: Person = { name: "John", age: 30 };
// 🚨 RULES: Must satisfy ALL combined types
```

---

## 8. **Function Types**

### **Definition**: Specify parameter types and return type of functions

```typescript
// Function declaration
function greet(name: string): string {
    return `Hello, ${name}`;
}
// 🚨 RULES: Parameters and return type must match

// Function type expression
type GreetFunction = (name: string) => string;
// 🚨 RULES: Arrow syntax for type definitions

// Optional parameters
function log(message: string, prefix?: string): void {
    console.log(prefix ? `${prefix}: ${message}` : message);
}
// 🚨 RULES: Optional parameters must be at the end

// Rest parameters
function sum(...numbers: number[]): number {
    return numbers.reduce((a, b) => a + b, 0);
}
// 🚨 RULES: Must be array type, must be last parameter
```

---

## 9. **Type Aliases**

### **Definition**: Create a new name for a type

```typescript
type UserID = string | number;
// 🚨 RULES: Can represent any type, including primitives, objects, unions

type User = {
    id: UserID;
    name: string;
    email?: string;
};
// 🚨 RULES: Can be extended using intersection types

type Point = [number, number];
// 🚨 RULES: Can represent tuple types
```

---

## 10. **Interface**

### **Definition**: Declare the shape of objects

```typescript
interface Person {
    name: string;
    age: number;
}
// 🚨 RULES: All properties must be present in implementation

interface Employee extends Person {
    employeeId: number;
    department: string;
}
// 🚨 RULES: Inherits all properties from parent interface

interface Dictionary {
    [key: string]: number;
}
// 🚨 RULES: Index signature must be consistent

// 🚨 INTERFACE vs TYPE ALIAS RULES:
// - Interfaces can be extended and implemented
// - Interfaces support declaration merging
// - Type aliases can represent union/intersection primitives
```

---

## 11. **Generic Types**

### **Definition**: Create reusable components that work with multiple types

```typescript
// Generic function
function identity<T>(value: T): T {
    return value;
}
// 🚨 RULES: Type parameter must be used in parameters or return type

// Generic interface
interface ApiResponse<T> {
    data: T;
    status: number;
}
// 🚨 RULES: Type parameter must be provided when used

// Generic constraints
function getProperty<T, K extends keyof T>(obj: T, key: K): T[K] {
    return obj[key];
}
// 🚨 RULES: Constraints use 'extends' keyword

// Generic classes
class Container<T> {
    constructor(public value: T) {}
}
// 🚨 RULES: Type parameter available throughout class
```

---

## 12. **Literal Types**

### **Definition**: Exact values as types

```typescript
// String literals
let direction: "left" | "right" | "up" | "down" = "left";
// 🚨 RULES: Value must exactly match one of the literal strings

// Numeric literals
let status: 200 | 404 | 500 = 200;
// 🚨 RULES: Value must exactly match one of the literal numbers

// Boolean literals
let isTrue: true = true;
// 🚨 RULES: Value must be exactly true or false

// Template literal types
type Email = `${string}@${string}.${string}`;
// 🚨 RULES: Must match the template pattern
```

---

## 13. **Mapped Types**

### **Definition**: Create new types by transforming properties of existing types

```typescript
// Make all properties optional
type Optional<T> = {
    [P in keyof T]?: T[P];
};
// 🚨 RULES: Uses 'in' keyword to iterate over keys

// Make all properties readonly
type Readonly<T> = {
    readonly [P in keyof T]: T[P];
};
// 🚨 RULES: Can add or remove modifiers

// Filter properties
type StringProperties<T> = {
    [K in keyof T as T[K] extends string ? K : never]: T[K]
};
// 🚨 RULES: 'as' clause can filter keys
```

---

## 14. **Conditional Types**

### **Definition**: Types that depend on conditions

```typescript
// Basic conditional
type IsString<T> = T extends string ? true : false;
type A = IsString<string>; // true
type B = IsString<number>; // false
// 🚨 RULES: Uses ternary syntax with 'extends'

// Infer keyword
type GetReturnType<T> = T extends (...args: any[]) => infer R ? R : never;
// 🚨 RULES: 'infer' captures type in conditional branch

// Distributed conditionals
type ToArray<T> = T extends any ? T[] : never;
type StringsOrNumbers = ToArray<string | number>; // string[] | number[]
// 🚨 RULES: Distributes over union types
```

---

## 15. **Utility Types (Built-in)**

### **Definition**: Predefined type transformations

```typescript
interface User {
    name: string;
    age: number;
    email: string;
}

// Partial<T> - all properties optional
type PartialUser = Partial<User>;
// 🚨 RULES: Adds ? modifier to all properties

// Required<T> - all properties required
type RequiredUser = Required<User>;
// 🚨 RULES: Removes ? modifier from all properties

// Readonly<T> - all properties readonly
type ReadonlyUser = Readonly<User>;
// 🚨 RULES: Adds readonly modifier to all properties

// Pick<T, K> - pick specific properties
type UserName = Pick<User, "name">;
// 🚨 RULES: K must be keys of T

// Omit<T, K> - omit specific properties
type UserWithoutEmail = Omit<User, "email">;
// 🚨 RULES: K must be keys of T

// Record<K, T> - object with specific key type and value type
type PageInfo = Record<string, { title: string }>;
// 🚨 RULES: K must be string | number | symbol
```

---

## **Key TypeScript Rules Summary**

1. **Strict Typing**: Once declared, type cannot change
2. **Structural Compatibility**: Types are compatible if they have the same structure
3. **Type Inference**: TypeScript infers types when not explicitly provided
4. **Readonly Modifier**: Prevents reassignment of properties/variables
5. **Optional Properties**: Marked with ? and can be undefined
6. **Generic Constraints**: Use 'extends' to limit generic types
7. **Type Narrowing**: Use typeof, instanceof, or type guards to narrow types
8. **Declaration Merging**: Interfaces with same name are merged

This comprehensive reference includes all TypeScript types with their definitions and mandatory rules for proper usage.