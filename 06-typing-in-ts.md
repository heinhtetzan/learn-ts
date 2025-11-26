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