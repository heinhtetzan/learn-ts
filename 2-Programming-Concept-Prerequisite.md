## Compilation

**What it is:** The entire source code is converted to machine code (or bytecode) **before** execution. This creates a separate executable file.

### Example: C Language

**Source Code (`hello.c`):**
```c
#include <stdio.h>

int main() {
    printf("Hello, Compiled World!\n");
    for(int i = 0; i < 3; i++) {
        printf("Count: %d\n", i);
    }
    return 0;
}
```

**Steps:**
1. **Write code** in `hello.c`
2. **Compile** with GCC: `gcc hello.c -o hello`
3. **Compiler** checks syntax, optimizes, generates machine code
4. **Output** creates executable file `hello` (or `hello.exe` on Windows)
5. **Run** the executable: `./hello`

**Result:**
```
Hello, Compiled World!
Count: 0
Count: 1
Count: 2
```

**Key Point:** The compilation happens once, then you can run the executable many times without recompiling.

---

## Interpretation

**What it is:** Code is executed **line by line** at runtime. No separate executable is created.

### Example: Python

**Source Code (`hello.py`):**
```python
print("Hello, Interpreted World!")

for i in range(3):
    print(f"Count: {i}")

def greet(name):
    return f"Hello, {name}!"

print(greet("Python"))
```

**Steps:**
1. **Write code** in `hello.py`
2. **Run directly** with interpreter: `python hello.py`
3. **Python interpreter:**
   - Reads first line, converts to bytecode, executes
   - Reads second line, converts to bytecode, executes
   - Continues line by line

**Result:**
```
Hello, Interpreted World!
Count: 0
Count: 1
Count: 2
Hello, Python!
```

**Key Point:** Code is translated and executed simultaneously each time you run it.

---

## JavaScript: A Hybrid Approach

**What it is:** Modern JavaScript uses **Just-In-Time (JIT) compilation**, combining both approaches.

### Example: JavaScript in Node.js

**Source Code (`hello.js`):**
```javascript
console.log("Hello, JIT World!");

function calculate(x, y) {
    return x * y + 10;
}

for(let i = 0; i < 3; i++) {
    console.log(`Result ${i}: ${calculate(i, 5)}`);
}

// Dynamic typing example
let dynamic = 42;
console.log(`Type: ${typeof dynamic}, Value: ${dynamic}`);
dynamic = "now I'm a string";
console.log(`Type: ${typeof dynamic}, Value: ${dynamic}`);
```

**Steps:**
1. **Write code** in `hello.js`
2. **Run with Node.js**: `node hello.js`
3. **V8 Engine (Node.js):**
   - **Interprets** code initially for quick startup
   - **Profiles** "hot" functions that run frequently
   - **Compiles** hot functions to optimized machine code
   - **Executes** optimized code for better performance

**Result:**
```
Hello, JIT World!
Result 0: 10
Result 1: 15
Result 2: 20
Type: number, Value: 42
Type: string, Value: now I'm a string
```

---

## TypeScript: Compilation to JavaScript

**What it is:** TypeScript is **compiled** (transpiled) to JavaScript, which then runs in an interpreter/JIT compiler.

### Example: TypeScript

**Source Code (`hello.ts`):**
```typescript
// Type annotations - checked during compilation
function greet(name: string): string {
    return `Hello, ${name}!`;
}

// Interface - exists only at compile time
interface User {
    id: number;
    username: string;
}

const currentUser: User = { id: 1, username: "alice" };

console.log(greet("TypeScript"));
console.log(`User: ${currentUser.username}`);

// This would cause a compile-time error:
// const error = greet(42); // Error: Argument of type 'number' is not assignable to parameter of type 'string'
```

**Steps:**
1. **Write TypeScript** in `hello.ts`
2. **Compile with TypeScript compiler**: `tsc hello.ts`
3. **TypeScript compiler:**
   - Checks types and syntax
   - Removes TypeScript-specific syntax (interfaces, type annotations)
   - Generates plain JavaScript (`hello.js`)
4. **Run JavaScript** with Node.js: `node hello.js`

**Generated JavaScript (`hello.js`):**
```javascript
function greet(name) {
    return "Hello, " + name + "!";
}
var currentUser = { id: 1, username: "alice" };
console.log(greet("TypeScript"));
console.log("User: " + currentUser.username);
```

**Result:**
```
Hello, TypeScript!
User: alice
```

---

## Comparison Table

| Aspect | Compilation (C/C++) | Interpretation (Python) | JIT Compilation (JavaScript) | Transpilation (TypeScript) |
|--------|---------------------|-------------------------|------------------------------|---------------------------|
| **Process** | Source → Machine Code | Source → Execute line by line | Source → Bytecode → Optimized Machine Code | TS → JS → Execute |
| **Speed** | Fast execution, slow startup | Slow execution, fast startup | Fast after optimization | Depends on target JS engine |
| **Error Detection** | At compile time | At runtime | Mostly at runtime | Types at compile time, logic at runtime |
| **Executable** | Separate file | No executable | No executable | No executable (generates JS) |
| **Portability** | Platform-specific | Cross-platform | Cross-platform | Cross-platform (via JS) |
| **Debugging** | Harder (machine code) | Easier (source code) | Medium (source maps) | Medium (source maps) |

## Key Takeaways

1. **Compilation** = Translate once, run many times
2. **Interpretation** = Translate and run simultaneously  
3. **JIT Compilation** = Best of both worlds: quick start + optimized execution
4. **TypeScript** = Compile-time type checking, but runs as interpreted JavaScript

This hybrid approach is why TypeScript gives you development-time safety while maintaining JavaScript's runtime flexibility!

Here's a comprehensive breakdown of which languages are typically compiled, interpreted, or use a hybrid approach:

## Purely Compiled Languages

These languages are translated entirely to machine code before execution.

### **Native Compiled Languages**
- **C** - Compiled to machine code
- **C++** - Compiled to machine code
- **Rust** - Compiled to machine code
- **Go** - Compiled to machine code
- **Fortran** - Scientific computing
- **Pascal** - Academic/legacy systems
- **Ada** - Aerospace/mission-critical systems

**Example - C compilation:**
```c
// math.c
#include <stdio.h>

int square(int x) {
    return x * x;
}

int main() {
    printf("Square: %d\n", square(5));
    return 0;
}
```
**Compilation:** `gcc math.c -o math` → Creates `math` executable

---

## Purely Interpreted Languages

These languages are executed line-by-line without prior compilation to machine code.

### **Traditional Interpreted Languages**
- **Python** (CPython implementation)
- **Ruby** (MRI implementation)
- **PHP**
- **JavaScript** (in browsers, before JIT)
- **Bash/Shell scripts**
- **Lua** (typically)
- **Perl** (typically)
- **R** (statistical computing)

**Example - Python interpretation:**
```python
# script.py
def greet(name):
    return f"Hello, {name}!"

print(greet("World"))
for i in range(3):
    print(f"Count: {i}")
```
**Execution:** `python script.py` → Interpreter reads and executes line by line

---

## Hybrid Approaches

### **Bytecode-Compiled + Virtual Machine**
These compile to intermediate bytecode, then run on a VM that may use interpretation or JIT compilation.

- **Java** → Bytecode → JVM (JIT compilation)
- **C#** → IL → .NET CLR (JIT compilation)
- **Kotlin** → Bytecode → JVM
- **Scala** → Bytecode → JVM

**Example - Java:**
```java
// Hello.java
public class Hello {
    public static void main(String[] args) {
        System.out.println("Hello Java World!");
        for (int i = 0; i < 3; i++) {
            System.out.println("Number: " + i);
        }
    }
}
```
**Process:** `javac Hello.java` → `Hello.class` (bytecode) → `java Hello` (JVM executes)

---

### **Just-In-Time (JIT) Compiled**
These start with interpretation but compile hot code paths to machine code during execution.

- **JavaScript** (V8 - Chrome/Node.js, SpiderMonkey - Firefox)
- **Python** (PyPy implementation)
- **C#** (in some scenarios)
- **Java** (modern JVMs)

**Example - JavaScript in Node.js:**
```javascript
// app.js
function heavyCalculation(n) {
    let result = 0;
    for (let i = 0; i < n; i++) {
        result += Math.sqrt(i);
    }
    return result;
}

// First runs interpreted, then JIT compiles if called frequently
console.log(heavyCalculation(1000000));
```

---

### **Transpiled Languages**
These compile to another high-level language rather than machine code.

- **TypeScript** → JavaScript
- **CoffeeScript** → JavaScript
- **Dart** → JavaScript (for web)
- **JSX** → JavaScript (React)
- **Sass/SCSS** → CSS
- **Elm** → JavaScript

**Example - TypeScript:**
```typescript
// app.ts
interface User {
    name: string;
    age: number;
}

const user: User = { name: "Alice", age: 30 };
console.log(`Hello ${user.name}`);
```
**Transpilation:** `tsc app.ts` → `app.js` (plain JavaScript)

---

## Language-Specific Details

### **Python**
- **CPython** (reference): Interpreted
- **PyPy**: JIT compiled
- **Cython**: Compiled to C
- **Jython**: Compiled to Java bytecode

### **JavaScript**
- **Early browsers**: Interpreted
- **Modern engines (V8, SpiderMonkey)**: JIT compiled
- **Node.js**: JIT compiled via V8

### **Java**
- **Compilation**: `javac` → bytecode (.class files)
- **Execution**: JVM → JIT compilation to machine code
- **AOT compilation**: Available in newer versions

---

## Quick Reference Table

| Category | Languages | Process |
|----------|-----------|---------|
| **Native Compiled** | C, C++, Rust, Go, Fortran | Source → Machine Code |
| **Interpreted** | Python, Ruby, PHP, Bash | Source → Execute directly |
| **Bytecode + VM** | Java, C#, Kotlin, Scala | Source → Bytecode → VM (JIT) |
| **JIT Compiled** | JavaScript, PyPy, LuaJIT | Source → Bytecode → JIT → Machine Code |
| **Transpiled** | TypeScript, Dart, CoffeeScript | Source → Another High-level Language |

---

## Modern Trends

### **Ahead-of-Time (AOT) Compilation**
- **Java**: GraalVM native image
- **C#**: .NET Native
- **JavaScript**: WebAssembly (WASM)
- **Dart**: Flutter AOT compilation

### **WebAssembly**
- **C/C++/Rust** → WASM → Run in browsers
- Provides near-native performance in web environments

---

## Important Notes

1. **Implementation Matters**: A language's behavior depends on its implementation:
   - Python (CPython) is interpreted, but PyPy is JIT compiled
   - JavaScript was interpreted, now is JIT compiled

2. **Hybrid is Common**: Most modern languages use hybrid approaches for balance between startup time and execution speed

3. **Transpilation vs Compilation**: 
   - **Compilation**: High-level → Low-level (machine code/bytecode)
   - **Transpilation**: High-level → High-level (TypeScript → JavaScript)

This explains why TypeScript is called a "compiled" language even though it outputs JavaScript - it performs static analysis and type checking like a compiler, even though the output is another high-level language.