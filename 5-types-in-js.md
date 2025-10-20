Here are the main types in JavaScript, categorized by their characteristics:

## Primitive Types (Immutable)

### 1. **String**
```javascript
let name = "John";
let message = 'Hello';
let template = `Hello ${name}`;
```

### 2. **Number**
```javascript
let integer = 42;
let float = 3.14;
let scientific = 1e5; // 100000
let infinity = Infinity;
let nan = NaN; // Not a Number
```

### 3. **Boolean**
```javascript
let isActive = true;
let isCompleted = false;
```

### 4. **Undefined**
```javascript
let undefinedVar; // undefined
let explicitlyUndefined = undefined;
```

### 5. **Null**
```javascript
let emptyValue = null;
```

### 6. **Symbol** (ES6)
```javascript
let sym1 = Symbol("description");
let sym2 = Symbol("description");
console.log(sym1 === sym2); // false - each Symbol is unique
```

### 7. **BigInt** (ES2020)
```javascript
let bigNumber = 123456789012345678901234567890n;
let bigInt = BigInt("12345678901234567890");
```

## Object Types (Mutable)

### 8. **Object**
```javascript
let person = {
    name: "John",
    age: 30,
    isStudent: false
};
```

### 9. **Array**
```javascript
let numbers = [1, 2, 3, 4, 5];
let mixed = [1, "hello", true, null];
```

### 10. **Function**
```javascript
function greet(name) {
    return `Hello ${name}`;
}

let multiply = function(a, b) {
    return a * b;
};
```

### 11. **Date**
```javascript
let now = new Date();
let specificDate = new Date("2023-12-25");
```

### 12. **RegExp**
```javascript
let pattern = /hello/i;
let regex = new RegExp("world", "g");
```

## Special Types

### 13. **Map** (ES6)
```javascript
let map = new Map();
map.set("name", "John");
map.set(1, "one");
```

### 14. **Set** (ES6)
```javascript
let set = new Set([1, 2, 3, 3, 4]); // {1, 2, 3, 4}
```

## Type Checking Methods

### **typeof Operator**
```javascript
console.log(typeof "hello");        // "string"
console.log(typeof 42);             // "number"
console.log(typeof true);           // "boolean"
console.log(typeof undefined);      // "undefined"
console.log(typeof null);           // "object" (historical bug)
console.log(typeof Symbol());       // "symbol"
console.log(typeof 123n);           // "bigint"
console.log(typeof {});             // "object"
console.log(typeof []);             // "object"
console.log(typeof function(){});   // "function"
```

### **Better Type Checking**
```javascript
// Check for array
Array.isArray([]); // true

// Check for null
let value = null;
console.log(value === null); // true

// Instance checking
[] instanceof Array; // true
new Date() instanceof Date; // true
```

## Type Conversion

### **Explicit Conversion**
```javascript
// String conversion
String(123); // "123"

// Number conversion
Number("42"); // 42
Number("hello"); // NaN

// Boolean conversion
Boolean(0); // false
Boolean(1); // true
```

### **Implicit Conversion (Coercion)**
```javascript
"5" + 2; // "52" (string concatenation)
"5" - 2; // 3 (numeric subtraction)
"5" * "2"; // 10 (numeric multiplication)
```

## Key Points to Remember

1. **Primitives are immutable**, objects are mutable
2. **`typeof null` returns "object"** (historical JavaScript bug)
3. **Arrays are objects** with special array methods
4. **Functions are objects** that can be called
5. **Use `===` for strict equality** to avoid type coercion surprises

Understanding JavaScript types is crucial for writing robust code and avoiding common pitfalls!