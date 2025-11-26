### The Two Axes of Type Systems

We can categorize type systems along two independent dimensions:

1.  **Static vs. Dynamic** (When are types checked? At compile-time or run-time?)
2.  **Strong vs. Weak** (How strictly are types enforced? Can types be implicitly converted?)

It's crucial to understand that "Static" is not the same as "Strong," and "Dynamic" is not the same as "Weak." A language can be any combination of these.

---

### Axis 1: Static Typing vs. Dynamic Typing

This axis is about *when* type information is acquired and checked.

#### Static Typing

*   **Core Idea:** Types are checked **at compile-time**, before the program runs. Variables have a type that is explicitly declared or inferred by the compiler, and it cannot change.
*   **Analogy:** Building a car from a blueprint. The blueprint (the code) specifies that the engine must be a V8, the wheels must be 18-inch alloys, etc. You check everything against the blueprint *before* you start building. If you try to put a bicycle wheel where an car wheel should be, the blueprint inspector (the compiler) stops you.
*   **How it Works:** You must declare the type of a variable (`int count;`). The compiler then verifies that you only perform operations valid for that type throughout your code.
*   **Benefits:**
    *   **Early Error Detection:** Catches type-related bugs before the software is even run.
    *   **Performance:** The compiler knows the exact types, allowing for more optimized machine code.
    *   **Tooling & IDE Support:** Enables powerful features like autocompletion, safe refactoring, and better navigation.
*   **Examples:** Java, C, C++, Go, Rust, Swift, Kotlin.

```java
// Java (Statically Typed)
String name = "Alice";
int number = 42;

name = 100; // COMPILE-TIME ERROR! The compiler says "You can't assign an int to a String variable."
number = "Bob"; // COMPILE-TIME ERROR!
```

#### Dynamic Typing

*   **Core Idea:** Types are checked **at run-time**, while the program is executing. Variables can hold values of any type, and their type is determined by the value they currently hold.
*   **Analogy:** A reality TV show. You don't know what a contestant will do until the moment it happens (runtime). A variable might be a chef one moment (cooking a meal) and a singer the next (performing a song). The producer (the interpreter) only finds out if it's valid when it happens.
*   **How it Works:** You don't declare types. The interpreter keeps track of the type "tag" associated with each value and checks if an operation is valid at the moment you perform it.
*   **Benefits:**
    *   **Flexibility & Rapid Prototyping:** Easier to write generic code and quickly change things.
    *   **Less Boilerplate:** Code can be more concise without type declarations.
*   **Drawbacks:**
    *   **Runtime Errors:** Type errors only surface when a specific line of code is executed, which might be in production.
    *   **Performance Overhead:** The interpreter must constantly check and manage types at runtime.
*   **Examples:** Python, JavaScript, Ruby, PHP.

```python
# Python (Dynamically Typed)
name = "Alice"   # name is now a string
number = 42      # number is now an integer

name = 100       # This is ALLOWED! 'name' is now an integer.
# The program will run fine until it tries to do something string-specific with 'name'
# while it holds an integer.
```

---

### Axis 2: Strong Typing vs. Weak Typing

This axis is about *how strictly* types are enforced and whether implicit conversions happen.

#### Strong Typing

*   **Core Idea:** The language is strict about type operations. It will **not** automatically convert between unrelated types. If an operation is not explicitly defined for the given types, it results in an error.
*   **Analogy:** A strict librarian. You can't just check out a "music CD" with a "book library card." You need the correct, specific card for the type of media you're borrowing. No exceptions.
*   **How it Works:** The language enforces a strict set of rules about what operations are allowed between different types.
*   **Examples:** Python, Java, Go, C#, Rust.

```python
# Python (Strongly Typed)
result = "5" + 10
# This throws a TypeError: can only concatenate str (not "int") to str
# Python does not automatically convert the integer 10 to a string.
```

#### Weak Typing

*   **Core Idea:** The language is permissive and will often **implicitly convert** (coerce) between types to make an operation work, even if it might lead to unexpected results.
*   **Analogy:** A flexible friend. If you ask for a coffee but they only have tea, they might just give you tea and say "close enough." This can sometimes be helpful, but sometimes it's not what you wanted.
*   **How it Works:** The language has a set of rules for automatically converting one type to another in certain contexts (e.g., in a mathematical operation, a string like `"5"` might be converted to the number `5`).
*   **Examples:** JavaScript, PHP, C (in certain contexts).

```javascript
// JavaScript (Weakly Typed)
let result = "5" + 10;
console.log(result); // "510" (The number 10 was converted to a string)

let anotherResult = "5" * 10;
console.log(anotherResult); // 50 (The string "5" was converted to a number)
```

---

### Putting It All Together: Common Language Profiles

| Language | Static/Dynamic | Strong/Weak | Explanation |
| :--- | :--- | :--- | :--- |
| **Java** | **Static** | **Strong** | Types are checked at compile-time, and there are very few implicit conversions. |
| **Python** | **Dynamic** | **Strong** | Types are checked at runtime, but it won't implicitly convert `str` to `int`. |
| **JavaScript** | **Dynamic** | **Weak** | Types are checked at runtime, and it frequently performs implicit conversions (e.g., `"5" * 10` works). |
| **C** | **Static** | **Weak** | Types are checked at compile-time, but the language allows a lot of implicit casting, especially with pointers. |
| **Go** | **Static** | **Strong** | Like Java, types are compile-time checked and enforced strictly. |
| **Ruby** | **Dynamic** | **Strong** | Like Python, types are checked at runtime but are strictly enforced. |

### Summary

*   **Static vs. Dynamic** is about **WHEN** you find out about a type error (compile-time vs. run-time).
*   **Strong vs. Weak** is about **HOW OFTEN** the language automatically converts types for you (rarely vs. frequently).


Absolutely! Let's dive deeper with extensive code examples across multiple languages to illustrate these concepts clearly.

## Axis 1: Static vs Dynamic Typing

### Static Typing Examples

#### Java
```java
public class StaticExample {
    public static void main(String[] args) {
        // Explicit type declarations - checked at compile time
        String name = "Alice";
        int age = 25;
        double salary = 50000.0;
        
        // These would cause COMPILE-TIME errors:
        // name = 30;                    // Error: incompatible types
        // age = "twenty-five";          // Error: incompatible types
        // salary = "fifty thousand";    // Error: incompatible types
        
        // The compiler knows exactly what methods are available
        name.length();        // OK - String has length() method
        // age.length();     // COMPILE ERROR: int doesn't have length()
        
        System.out.println("Name: " + name + ", Age: " + age);
    }
}
```

#### C#
```csharp
using System;

class StaticExample {
    static void Main() {
        // Types are explicitly declared and checked at compile time
        string message = "Hello World";
        int count = 10;
        bool isValid = true;
        
        // Compiler prevents type mismatches
        // message = 42;                 // Error: Cannot convert int to string
        // count = "ten";               // Error: Cannot convert string to int
        
        // Type-specific operations are enforced
        string upper = message.ToUpper();  // OK - string method
        // int upper = count.ToUpper();  // COMPILE ERROR: int has no ToUpper()
        
        Console.WriteLine($"{message} has {message.Length} characters");
    }
}
```

#### TypeScript (Static typing for JavaScript)
```typescript
// Type annotations provide static type checking
interface User {
    name: string;
    age: number;
    email: string;
}

function greetUser(user: User): string {
    return `Hello ${user.name}, you are ${user.age} years old!`;
}

// This works fine
const user1: User = {
    name: "Bob",
    age: 30,
    email: "bob@example.com"
};

// These would cause COMPILE-TIME errors:
// const user2: User = {
//     name: "Alice",
//     age: "twenty"  // ERROR: Type 'string' is not assignable to type 'number'
// };

// const user3: User = {
//     name: "Charlie"  // ERROR: Missing properties 'age', 'email'
// };

console.log(greetUser(user1));
```

### Dynamic Typing Examples

#### Python
```python
# Dynamic typing - no type declarations
def process_data(data):
    # We don't know the type until runtime
    print(f"Processing: {data}, type: {type(data)}")
    return data

# Same variable can hold different types
value = "Hello"          # value is now a string
print(value.upper())     # OK - string method

value = 42               # value is now an integer
print(value * 2)         # OK - math operation

value = [1, 2, 3]        # value is now a list
print(len(value))        # OK - list has length

# Runtime error only occurs when code executes
def risky_operation(x):
    return x.upper()     # This might fail at runtime

# This works fine
risky_operation("hello")

# This fails at RUNTIME, not compile time
try:
    risky_operation(42)  # TypeError: 'int' object has no attribute 'upper'
except TypeError as e:
    print(f"Runtime error caught: {e}")
```

#### JavaScript
```javascript
// Dynamic typing in action
let variable = "I'm a string";
console.log(typeof variable, variable); // "string I'm a string"

variable = 42;
console.log(typeof variable, variable); // "number 42"

variable = true;
console.log(typeof variable, variable); // "boolean true"

variable = { name: "John", age: 25 };
console.log(typeof variable, variable); // "object { name: 'John', age: 25 }"

// Runtime type checking
function processInput(input) {
    // We have to check types at runtime
    if (typeof input === 'string') {
        return input.toUpperCase();
    } else if (typeof input === 'number') {
        return input * 2;
    } else {
        return "Unknown type";
    }
}

console.log(processInput("hello")); // "HELLO"
console.log(processInput(10));      // 20
console.log(processInput(true));    // "Unknown type"
```

#### Ruby
```ruby
# Dynamic typing in Ruby
value = "I'm a string"
puts "#{value} is a #{value.class}"

value = 42
puts "#{value} is a #{value.class}"

value = [1, 2, 3]
puts "#{value} is a #{value.class}"

# Duck typing - we care about behavior, not type
def process_item(item)
  if item.respond_to?(:upcase)
    item.upcase
  elsif item.respond_to?(:abs)
    item.abs
  else
    "Can't process this type"
  end
end

puts process_item("hello")  # "HELLO"
puts process_item(-5)       # 5
puts process_item([1,2,3])  # "Can't process this type"
```

---

## Axis 2: Strong vs Weak Typing

### Strong Typing Examples

#### Python (Dynamic + Strong)
```python
# Python is dynamically but strongly typed
# No implicit conversion between unrelated types

# These cause explicit errors
try:
    result = "5" + 10  # TypeError: can only concatenate str to str
except TypeError as e:
    print(f"Error: {e}")

try:
    result = "hello" * "world"  # TypeError: can't multiply sequence by non-int
except TypeError as e:
    print(f"Error: {e}")

# Explicit conversions are required
result = int("5") + 10      # OK: explicit conversion
print(result)               # 15

result = "5" + str(10)      # OK: explicit conversion  
print(result)               # "510"

# Even with similar-looking types
from decimal import Decimal
price = Decimal("10.5")
tax = 0.07

# result = price * tax     # TypeError: unsupported operand type(s)
result = price * Decimal(str(tax))  # Must convert explicitly
print(result)
```

#### Java (Static + Strong)
```java
public class StrongTyping {
    public static void main(String[] args) {
        String text = "5";
        int number = 10;
        
        // These would not compile - no implicit conversions
        // String result1 = text + number;  // Actually, this WORKS in Java due to operator overloading
        // But generally, Java is strongly typed:
        
        // int result2 = text + number;     // Error: incompatible types
        // boolean result3 = text;          // Error: incompatible types
        
        // Explicit conversions required
        int parsedNumber = Integer.parseInt(text);
        int result = parsedNumber + number;  // OK: explicit conversion
        System.out.println("Result: " + result);
        
        // No truthy/falsy concepts
        int count = 0;
        String name = "";
        
        // if (count) { ... }              // Error: incompatible types
        // if (name) { ... }               // Error: incompatible types
        
        if (count == 0) { ... }           // Must be explicit
        if (name.isEmpty()) { ... }       // Must be explicit
    }
}
```

### Weak Typing Examples

#### JavaScript (Dynamic + Weak)
```javascript
// JavaScript performs lots of implicit type coercion
console.log("5" + 10);          // "510" (number to string)
console.log("5" - 10);          // -5 (string to number)  
console.log("5" * "10");        // 50 (both to numbers)
console.log(10 + null);         // 10 (null becomes 0)
console.log(10 + undefined);    // NaN (undefined becomes NaN)

// Truthy/falsy values (weak boolean conversion)
console.log(!0);                // true (0 is falsy)
console.log(!"hello");          // false (non-empty string is truthy)
console.log(!!"");              // false (empty string is falsy)
console.log(!![]);              // true (empty array is truthy)
console.log(!!{});              // true (empty object is truthy)

// Loose equality with type coercion
console.log("5" == 5);          // true (type coercion)
console.log("5" === 5);         // false (strict equality - no coercion)

console.log(0 == false);        // true
console.log(0 === false);       // false

console.log("" == false);       // true  
console.log("" === false);      // false

// Weird coercions
console.log([] == 0);           // true (array becomes string, then number)
console.log([] == "");          // true
console.log([] == false);       // true
```

#### PHP (Dynamic + Weak)
```php
<?php
// PHP has extensive type juggling (weak typing)

// Implicit conversions in operations
$result = "5" + 10;           // 15 (string to number)
echo $result . "\n";

$result = "5 apples" + "10 oranges"; // 15 (extracts numbers)
echo $result . "\n";

$result = "hello" + "world";  // 0 (no numbers found)
echo $result . "\n";

// Loose comparisons with type juggling
var_dump("5" == 5);           // bool(true)
var_dump("5" === 5);          // bool(false)

var_dump(0 == "hello");       // bool(true) - "hello" becomes 0
var_dump(0 == "");            // bool(true)
var_dump(0 == "0");           // bool(true)

// Truthy/falsy values
if ("hello") {                // true (non-empty string is truthy)
    echo "This runs\n";
}

if (0) {                      // false
    echo "This doesn't run\n";
}

if ("0") {                    // false! (unique to PHP)
    echo "This doesn't run either\n";
}

// Array and string mixing
$count = "3 cats";
$array = array(1, 2, 3);
echo $count + $array[0];      // 4 (extracts 3 from string)
?>
```

#### C (Static + Weak)
```c
#include <stdio.h>

int main() {
    // C allows implicit conversions between many types
    int integer = 10;
    double decimal = 5.5;
    
    // Implicit conversion int to double
    double result1 = integer + decimal;  // 15.5
    printf("int + double = %f\n", result1);
    
    // Implicit conversion double to int (truncation)
    int result2 = integer + decimal;     // 15 - decimal part lost!
    printf("int + double (as int) = %d\n", result2);
    
    // Pointer and integer mixing (dangerous!)
    int arr[3] = {1, 2, 3};
    int *ptr = arr;
    
    printf("arr[0] = %d\n", *ptr);       // 1
    printf("arr[1] = %d\n", *(ptr + 1)); // 2 - pointer arithmetic
    
    // Void pointer can point to anything
    void *generic_ptr;
    generic_ptr = &integer;
    generic_ptr = &decimal;
    generic_ptr = arr;
    
    // You have to cast back to use it
    int *int_ptr = (int*)generic_ptr;
    printf("First element: %d\n", *int_ptr);
    
    return 0;
}
```

---

## Mixed Examples Showing the Combinations

### Go (Static + Strong)
```go
package main

import "fmt"

func main() {
    // Static and strong typing
    var count int = 10
    var price float64 = 19.99
    var name string = "Gopher"
    
    // These would cause compile errors:
    // count = "ten"           // Cannot use "ten" as int value
    // result := count + price // Invalid operation: mismatched types
    
    // Explicit conversions required
    total := float64(count) * price  // Must explicitly convert
    fmt.Printf("Total: $%.2f\n", total)
    
    // No implicit boolean conversion
    if count == 0 {              // Must use explicit comparison
        fmt.Println("Count is zero")
    }
    // if count { ... }         // Non-bool used in if condition
    
    // Strong interface satisfaction
    var s fmt.Stringer
    s = count                    // Error: int doesn't implement Stringer
    // Must implement the interface explicitly
}
```

### TypeScript vs JavaScript Comparison
```typescript
// TypeScript (Static + with optional strong/weak behavior)
function calculate(a: number, b: number): number {
    return a + b;
}

// These cause COMPILE-TIME errors:
// calculate("5", 10);        // Error: Argument of type 'string' is not assignable
// calculate(5, "10");        // Error

// But TypeScript can be compiled to JavaScript which is weak:
const jsCode = `
// JavaScript (Dynamic + Weak) - compiled output
function calculate(a, b) {
    return a + b;
}

console.log(calculate("5", 10));  // "510" at runtime
console.log(calculate(5, "10"));  // "510" at runtime  
`;
```

## Summary Table with More Languages

| Language | Static/Dynamic | Strong/Weak | Key Characteristics |
|----------|----------------|-------------|---------------------|
| **Java** | Static | Strong | Compile-time checks, minimal implicit conversion |
| **C#** | Static | Strong | Similar to Java, with good type inference |
| **Go** | Static | Strong | No implicit numeric conversions, explicit everything |
| **Rust** | Static | Strong | Extremely strict, no null pointers, memory safety |
| **TypeScript** | Static | Strong | JavaScript with static types, compiles to weak JS |
| **Python** | Dynamic | Strong | Runtime types, but no implicit type coercion |
| **Ruby** | Dynamic | Strong | Duck typing, but explicit about types |
| **JavaScript** | Dynamic | Weak | Extensive type coercion, truthy/falsy values |
| **PHP** | Dynamic | Weak | Extensive type juggling, loose comparisons |
| **C** | Static | Weak | Implicit numeric conversions, pointer arithmetic |
| **C++** | Static | Medium | Can be strong or weak depending on usage |

The key takeaway is that **static/dynamic** is about **when** types are checked (compile-time vs runtime), while **strong/weak** is about **how strictly** types are enforced and whether implicit conversions happen. Most modern languages tend toward the strong typing side for reliability, while dynamic typing offers more flexibility during development.