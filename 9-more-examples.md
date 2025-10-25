Here are 5 common and useful examples of Union and Intersection Types in TypeScript:

## 1. **API Response with Success/Error States**

```typescript
// Union type for different API response states
type SuccessResponse<T> = {
  status: 'success';
  data: T;
  timestamp: Date;
};

type ErrorResponse = {
  status: 'error';
  error: string;
  code: number;
};

type LoadingResponse = {
  status: 'loading';
};

type ApiResponse<T> = SuccessResponse<T> | ErrorResponse | LoadingResponse;

// Usage
function handleResponse<T>(response: ApiResponse<T>): void {
  if (response.status === 'success') {
    console.log('Data:', response.data); // ✅ TypeScript knows response has 'data'
  } else if (response.status === 'error') {
    console.log('Error:', response.error); // ✅ TypeScript knows response has 'error'
  } else {
    console.log('Loading...'); // ✅ TypeScript knows it's loading state
  }
}
```

## 2. **Component Props with Variants**

```typescript
// Intersection type for base props + variant-specific props
type BaseButtonProps = {
  onClick: () => void;
  disabled?: boolean;
  className?: string;
};

type PrimaryButtonProps = {
  variant: 'primary';
  icon?: string;
};

type DangerButtonProps = {
  variant: 'danger';
  confirmMessage?: string;
};

type LinkButtonProps = {
  variant: 'link';
  href: string;
  target?: '_blank' | '_self';
};

type ButtonProps = BaseButtonProps & (
  | PrimaryButtonProps
  | DangerButtonProps
  | LinkButtonProps
);

// Usage
const primaryButton: ButtonProps = {
  variant: 'primary',
  onClick: () => console.log('clicked'),
  icon: 'plus' // ✅ Only available for primary variant
};

const linkButton: ButtonProps = {
  variant: 'link',
  onClick: () => console.log('clicked'),
  href: '/home' // ✅ Required for link variant
};
```

## 3. **Form Validation with Different Field Types**

```typescript
// Union types for different form field validations
type TextFieldValidation = {
  type: 'text';
  required: boolean;
  minLength?: number;
  maxLength?: number;
  pattern?: RegExp;
};

type NumberFieldValidation = {
  type: 'number';
  required: boolean;
  min?: number;
  max?: number;
  integer?: boolean;
};

type EmailFieldValidation = {
  type: 'email';
  required: boolean;
  domain?: string;
};

type FieldValidation = TextFieldValidation | NumberFieldValidation | EmailFieldValidation;

// Usage with intersection for complete field definition
type FormField = {
  name: string;
  label: string;
  value: any;
} & FieldValidation;

function validateField(field: FormField): boolean {
  switch (field.type) {
    case 'text':
      return field.value.length >= (field.minLength || 0); // ✅ TypeScript knows minLength exists
    case 'number':
      return field.value >= (field.min || 0); // ✅ TypeScript knows min exists
    case 'email':
      return field.value.includes('@'); // ✅ Type-specific validation
  }
}
```

## 4. **Event System with Different Event Types**

```typescript
// Union type for different event types
type ClickEvent = {
  type: 'click';
  x: number;
  y: number;
  button: 'left' | 'right' | 'middle';
};

type KeyboardEvent = {
  type: 'keydown' | 'keyup';
  key: string;
  ctrlKey: boolean;
  shiftKey: boolean;
};

type CustomEvent = {
  type: 'custom';
  data: any;
  source: string;
};

type AppEvent = ClickEvent | KeyboardEvent | CustomEvent;

// Intersection type for event handler with metadata
type EventHandler = {
  priority: number;
  once?: boolean;
} & (
  | { event: ClickEvent; handler: (e: ClickEvent) => void }
  | { event: KeyboardEvent; handler: (e: KeyboardEvent) => void }
  | { event: CustomEvent; handler: (e: CustomEvent) => void }
);

// Usage
function handleEvent(handlerConfig: EventHandler): void {
  const { event, handler } = handlerConfig;
  handler(event); // ✅ Type-safe - handler and event types match perfectly
}
```

## 5. **Database Entities with Relationships**

```typescript
// Base entity properties
type EntityBase = {
  id: string;
  createdAt: Date;
  updatedAt: Date;
};

// Different entity types
type UserEntity = EntityBase & {
  type: 'user';
  email: string;
  name: string;
  role: 'admin' | 'user' | 'moderator';
};

type ProductEntity = EntityBase & {
  type: 'product';
  name: string;
  price: number;
  category: string;
  inStock: boolean;
};

type OrderEntity = EntityBase & {
  type: 'order';
  userId: string;
  productIds: string[];
  total: number;
  status: 'pending' | 'completed' | 'cancelled';
};

// Union type for all entities
type Entity = UserEntity | ProductEntity | OrderEntity;

// Generic repository with type guards
class Repository {
  private entities: Entity[] = [];

  add<T extends Entity>(entity: T): void {
    this.entities.push(entity);
  }

  // Type-safe filtering
  findByType<T extends Entity['type']>(type: T): Extract<Entity, { type: T }>[] {
    return this.entities.filter(
      (entity): entity is Extract<Entity, { type: T }> => entity.type === type
    );
  }

  // Type-safe single entity find
  findUserById(id: string): UserEntity | undefined {
    return this.entities.find(
      (entity): entity is UserEntity => entity.type === 'user' && entity.id === id
    );
  }
}

// Usage
const repo = new Repository();
repo.add({
  type: 'user',
  id: '1',
  email: 'john@example.com',
  name: 'John',
  role: 'admin',
  createdAt: new Date(),
  updatedAt: new Date()
});

const users = repo.findByType('user'); // ✅ Type: UserEntity[]
const firstUser = users[0];
console.log(firstUser.email); // ✅ TypeScript knows this is a UserEntity
```

## **Key Benefits Demonstrated:**

1. **Type Safety**: Compiler ensures you only access properties that exist
2. **Autocomplete**: IDE provides relevant suggestions based on the type
3. **Runtime Safety**: Type guards prevent accessing wrong properties
4. **Maintainability**: Clear, self-documenting code structure
5. **Flexibility**: Easy to extend with new types while maintaining safety

These patterns are commonly used in real-world applications for handling different states, component variants, form validation, event systems, and database operations.


You're right! Let me focus specifically on **Intersection Types** with 5 practical examples:

## 1. **Combining Multiple Interfaces for Complex Objects**

```typescript
// Base properties
type Identifiable = {
  id: string;
  createdAt: Date;
};

type Timestampable = {
  updatedAt: Date;
  deletedAt?: Date;
};

type SoftDeletable = {
  isDeleted: boolean;
};

// Intersection combines ALL properties
type BaseEntity = Identifiable & Timestampable & SoftDeletable;

type User = BaseEntity & {
  name: string;
  email: string;
};

// Usage - must satisfy ALL types
const user: User = {
  id: "1",
  createdAt: new Date(),
  updatedAt: new Date(),
  isDeleted: false,
  name: "John",
  email: "john@example.com"
  // ✅ Must have all properties from all intersected types
};
```

## 2. **Function Composition with Multiple Capabilities**

```typescript
type Logger = {
  log: (message: string) => void;
  error: (error: Error) => void;
};

type Cache = {
  get: (key: string) => any;
  set: (key: string, value: any) => void;
};

type Validator = {
  validate: (data: any) => boolean;
};

// Service has ALL capabilities
type DataService = Logger & Cache & Validator;

const dataService: DataService = {
  log: (message) => console.log(message),
  error: (error) => console.error(error),
  get: (key) => localStorage.getItem(key),
  set: (key, value) => localStorage.setItem(key, value),
  validate: (data) => data !== null && data !== undefined
  // ✅ Must implement ALL methods from ALL types
};
```

## 3. **Component Props with Required and Optional Mixins**

```typescript
// Base props every component needs
type Stylable = {
  className?: string;
  style?: React.CSSProperties;
};

// Accessibility props
type Accessible = {
  ariaLabel?: string;
  role?: string;
  tabIndex?: number;
};

// Focus management
type Focusable = {
  autoFocus?: boolean;
  onFocus?: () => void;
  onBlur?: () => void;
};

// Button-specific props
type ButtonSpecific = {
  variant: 'primary' | 'secondary' | 'danger';
  size: 'sm' | 'md' | 'lg';
  onClick: () => void;
};

// Intersection creates complete button props
type ButtonProps = Stylable & Accessible & Focusable & ButtonSpecific;

const buttonProps: ButtonProps = {
  variant: 'primary',
  size: 'md',
  onClick: () => console.log('clicked'),
  className: 'btn-primary',
  ariaLabel: 'Submit form',
  autoFocus: true,
  // ✅ Must include onClick, variant, size from ButtonSpecific
  // ✅ Can optionally include any from Stylable, Accessible, Focusable
};
```

## 4. **API Request Configuration Builder**

```typescript
type HTTPConfig = {
  baseURL: string;
  timeout: number;
};

type AuthConfig = {
  apiKey?: string;
  token?: string;
  credentials?: 'include' | 'omit';
};

type CacheConfig = {
  cache: 'no-cache' | 'force-cache';
  revalidate?: number;
};

type RequestConfig = HTTPConfig & AuthConfig & CacheConfig;

function createApiClient(config: RequestConfig) {
  // config has ALL properties from all three types
  return {
    baseURL: config.baseURL,
    timeout: config.timeout,
    apiKey: config.apiKey,
    cache: config.cache
    // ✅ Can access any property from the intersection
  };
}

const client = createApiClient({
  baseURL: 'https://api.example.com',
  timeout: 5000,
  cache: 'no-cache',
  // ✅ Must provide required from HTTPConfig and CacheConfig
  // ✅ Optional from AuthConfig
});
```

## 5. **User Permissions and Roles System**

```typescript
type UserProfile = {
  userId: string;
  username: string;
  email: string;
};

type Permissions = {
  canRead: boolean;
  canWrite: boolean;
  canDelete: boolean;
};

type RoleBased = {
  role: 'admin' | 'moderator' | 'user';
  permissions: string[];
};

type SessionInfo = {
  loggedInAt: Date;
  lastActive: Date;
  sessionId: string;
};

// Intersection creates complete user session object
type AuthenticatedUser = UserProfile & Permissions & RoleBased & SessionInfo;

const adminUser: AuthenticatedUser = {
  // From UserProfile
  userId: "123",
  username: "admin",
  email: "admin@example.com",
  
  // From Permissions
  canRead: true,
  canWrite: true,
  canDelete: true,
  
  // From RoleBased
  role: "admin",
  permissions: ["read:all", "write:all", "delete:all"],
  
  // From SessionInfo
  loggedInAt: new Date(),
  lastActive: new Date(),
  sessionId: "session-123"
  // ✅ Must satisfy ALL properties from ALL four types
};

function checkPermission(user: AuthenticatedUser, action: string): boolean {
  // We can access ANY property from the intersection
  if (user.role === 'admin') return true;
  return user.permissions.includes(action) && user.canWrite;
}
```

## **Key Intersection Type Characteristics:**

1. **"AND" Logic**: Must satisfy **ALL** combined types
2. **Property Merging**: Combines all properties into one type
3. **Required All**: All required properties from all types become required
4. **Optional Preserved**: Optional properties remain optional
5. **Type Safety**: Compiler ensures you implement everything

## **Common Use Cases:**
- **Mixin patterns**: Combining multiple behaviors
- **Configuration objects**: Building complex configs from simple parts
- **Entity composition**: Creating rich objects from multiple concerns
- **Plugin systems**: Combining multiple capabilities
- **Builder patterns**: Step-by-step object construction

Intersection types are perfect when you need to **combine capabilities** rather than choose between alternatives (which is what union types are for).


Here are more comprehensive examples of TypeScript type aliases:

## 1. **Primitive and Union Types**
```typescript
// Primitive aliases for better semantics
type ID = string;
type Email = string;
type Percentage = number;
type Timestamp = number;

// Union types
type Status = "pending" | "approved" | "rejected";
type Size = "small" | "medium" | "large";
type UserRole = "admin" | "user" | "guest";
```

## 2. **Object Types**
```typescript
// Basic object shapes
type Address = {
    street: string;
    city: string;
    country: string;
    postalCode: string;
};

type Product = {
    id: ID;
    name: string;
    price: number;
    inStock: boolean;
    categories: string[];
};

// Nested object types
type Company = {
    name: string;
    address: Address;
    employees: number;
};
```

## 3. **Function Types**
```typescript
// Function signatures
type ClickHandler = (event: MouseEvent) => void;
type Comparator<T> = (a: T, b: T) => number;
type AsyncFetcher<T> = (url: string) => Promise<T>;
type EventCallback = (data: unknown) => void;

// Usage example
type ApiResponseHandler = {
    onSuccess: (data: any) => void;
    onError: (error: Error) => void;
};
```

## 4. **Complex Union and Intersection Types**
```typescript
// Discriminated unions
type Shape = 
    | { kind: "circle"; radius: number }
    | { kind: "rectangle"; width: number; height: number }
    | { kind: "triangle"; base: number; height: number };

// Intersection types
type Identifiable = { id: string };
type Timestamped = { createdAt: Date; updatedAt: Date };
type SoftDeletable = { deletedAt: Date | null };

type BaseEntity = Identifiable & Timestamped & SoftDeletable;

// Conditional union
type ApiResponse<T> = 
    | { status: "success"; data: T }
    | { status: "error"; message: string };
```

## 5. **Tuple and Array Types**
```typescript
// Tuple types
type Coordinate = [number, number];
type RGBColor = [number, number, number];
type HttpStatus = [number, string];

// Array types
type Matrix = number[][];
type StringArray = Array<string>;
type KeyValuePairs = [string, any][];
```

## 6. **Utility and Template Literal Types**
```typescript
// Template literal types
type CSSPosition = `${"top" | "bottom"}-${"left" | "right"}`;
type CSSSize = `${number}px` | `${number}em` | `${number}rem` | `${number}%`;

// Index signatures
type Dictionary<T> = {
    [key: string]: T;
};

type NumericArray = {
    [index: number]: number;
};
```

## 7. **Generic Type Aliases**
```typescript
// Generic containers
type Result<T, E = Error> = {
    success: boolean;
    data?: T;
    error?: E;
};

type PaginatedResponse<T> = {
    items: T[];
    total: number;
    page: number;
    pageSize: number;
    hasNext: boolean;
};

type Optional<T, K extends keyof T> = Omit<T, K> & Partial<Pick<T, K>>;
```

## 8. **Real-world Examples**
```typescript
// API and data modeling
type ApiConfig = {
    baseURL: string;
    timeout: number;
    headers: Record<string, string>;
};

type User = {
    id: ID;
    email: Email;
    profile: {
        firstName: string;
        lastName: string;
        avatar?: string;
    };
    preferences: {
        theme: "light" | "dark";
        notifications: boolean;
    };
    addresses: Address[];
};

type Order = {
    id: ID;
    userId: ID;
    products: Array<{
        productId: ID;
        quantity: number;
        price: number;
    }>;
    status: "created" | "paid" | "shipped" | "delivered" | "cancelled";
    total: number;
    createdAt: Timestamp;
};
```

## 9. **Advanced Patterns**
```typescript
// Recursive types
type TreeNode<T> = {
    value: T;
    children: TreeNode<T>[];
};

type JSONValue = 
    | string 
    | number 
    | boolean 
    | null 
    | JSONValue[] 
    | { [key: string]: JSONValue };

// Mapped types
type ReadonlyProduct = Readonly<Product>;
type PartialUser = Partial<User>;
type RequiredUser = Required<User>;

// Conditional types with aliases
type NonNullableUser = NonNullable<User | null | undefined>;
type UserKeys = keyof User;
```

## 10. **Usage Examples**
```typescript
// Using the type aliases
const user: User = {
    id: "user-123",
    email: "john@example.com",
    profile: {
        firstName: "John",
        lastName: "Doe"
    },
    preferences: {
        theme: "dark",
        notifications: true
    },
    addresses: []
};

const apiResult: Result<User> = {
    success: true,
    data: user
};

const position: CSSPosition = "top-left";
const size: CSSSize = "100px";

// Function using type aliases
const handleApiResponse: AsyncFetcher<User> = async (url) => {
    const response = await fetch(url);
    return response.json();
};
```

These examples demonstrate the versatility of type aliases in creating readable, reusable, and maintainable type definitions throughout your TypeScript codebase.


## Generic Types: From Fundamentals to Advanced Patterns

### 1. **Generic Functions - Core Concept**

**Basic Example:**
```typescript
// Simple generic function
function identity<T>(value: T): T {
    return value;
}

// Usage - TypeScript infers the type
const numberResult = identity(42);        // T is number
const stringResult = identity("hello");   // T is string
const arrayResult = identity([1, 2, 3]);  // T is number[]
```

**Advanced Pattern: Function Composition**
```typescript
// Generic function chaining with different types
function pipe<T, U, V>(
  value: T,
  fn1: (input: T) => U,
  fn2: (input: U) => V
): V {
  return fn2(fn1(value));
}

// Usage with type inference
const result = pipe(
  "hello",                    // T = string
  (str) => str.length,        // U = number  
  (num) => num * 2           // V = number
); // result: 10
```

### 2. **Generic Interfaces - Real World Applications**

**Basic API Response:**
```typescript
interface ApiResponse<T> {
  data: T;
  status: number;
  timestamp: Date;
}

// Usage with explicit types
const userResponse: ApiResponse<{ id: number; name: string }> = {
  data: { id: 1, name: "John" },
  status: 200,
  timestamp: new Date()
};
```

**Advanced: Recursive Generic Types**
```typescript
// Tree structure with generics
interface TreeNode<T> {
  value: T;
  children: TreeNode<T>[];
}

// Usage with complex types
const categoryTree: TreeNode<{ name: string; count: number }> = {
  value: { name: "Electronics", count: 150 },
  children: [
    {
      value: { name: "Laptops", count: 75 },
      children: []
    }
  ]
};
```

### 3. **Generic Constraints - Power and Safety**

**Basic Constraints:**
```typescript
// Constraint: T must have length property
function logLength<T extends { length: number }>(item: T): void {
  console.log(item.length);
}

logLength("hello");     // Works - string has length
logLength([1, 2, 3]);   // Works - array has length
// logLength(42);       // Error - number has no length
```

**Advanced: Multiple Constraints with Key Operations**
```typescript
// Complex constraints for database operations
interface Entity {
  id: number;
  createdAt: Date;
}

function updateEntity<
  T extends Entity,
  K extends keyof T
>(
  entity: T,
  field: K,
  value: T[K]
): T {
  return { ...entity, [field]: value };
}

// Usage with type safety
const user = { id: 1, name: "Alice", createdAt: new Date() };
const updated = updateEntity(user, "name", "Bob"); // Valid
// updateEntity(user, "invalid", "value"); // TypeScript error
```

### 4. **Generic Classes - Advanced Patterns**

**Basic Generic Container:**
```typescript
class Container<T> {
  constructor(private value: T) {}
  
  getValue(): T {
    return this.value;
  }
  
  map<U>(fn: (value: T) => U): Container<U> {
    return new Container(fn(this.value));
  }
}
```

**Advanced: Generic Repository Pattern**
```typescript
// Generic repository for different entity types
interface Repository<T, ID = number> {
  findById(id: ID): Promise<T | null>;
  save(entity: T): Promise<T>;
  delete(id: ID): Promise<boolean>;
}

// Implementation with constraints
class InMemoryRepository<T extends { id: ID }, ID = number> 
  implements Repository<T, ID> {
  
  private entities = new Map<ID, T>();

  async findById(id: ID): Promise<T | null> {
    return this.entities.get(id) || null;
  }

  async save(entity: T): Promise<T> {
    this.entities.set(entity.id, entity);
    return entity;
  }

  async delete(id: ID): Promise<boolean> {
    return this.entities.delete(id);
  }
}

// Usage with different entity types
interface User {
  id: number;
  name: string;
}

interface Product {
  id: string;  // Different ID type
  title: string;
  price: number;
}

const userRepo = new InMemoryRepository<User>();
const productRepo = new InMemoryRepository<Product, string>();
```

### 5. **Conditional Types and Advanced Inference**

**Conditional Type Mapping:**
```typescript
// Transform types based on conditions
type Nullable<T> = T | null;
type ArrayOrSingle<T> = T | T[];

// Advanced: Extract array element type
type ArrayElement<T> = T extends (infer U)[] ? U : T;

// Usage examples
type Test1 = ArrayElement<number[]>;        // number
type Test2 = ArrayElement<string>;          // string
type Test3 = ArrayElement<[boolean, number]>; // boolean | number
```

**Mapped Types with Generics:**
```typescript
// Create read-only versions of types
type ReadonlyRecord<K extends keyof any, T> = {
  readonly [P in K]: T;
};

// Make all properties optional and nullable
type PartialNullable<T> = {
  [P in keyof T]?: T[P] | null;
};

// Usage
type User = { id: number; name: string };
type ReadonlyUser = ReadonlyRecord<keyof User, User[keyof User]>;
type FlexibleUser = PartialNullable<User>;
```

### 6. **Real-World Advanced Example: API Client**

```typescript
// Generic API client with error handling
class ApiClient {
  async get<T>(url: string): Promise<{ data: T; status: number }> {
    const response = await fetch(url);
    const data = await response.json() as T;
    return { data, status: response.status };
  }

  async post<T, U>(url: string, body: T): Promise<{ data: U; status: number }> {
    const response = await fetch(url, {
      method: 'POST',
      body: JSON.stringify(body)
    });
    const data = await response.json() as U;
    return { data, status: response.status };
  }
}

// Usage with type safety
interface User { id: number; name: string; }
interface CreateUserDto { name: string; email: string; }

const api = new ApiClient();

// Type-safe API calls
const userResponse = await api.get<User>('/users/1');
const newUser = await api.post<CreateUserDto, User>('/users', {
  name: "John",
  email: "john@example.com"
});
```

### **Key Advanced Concepts:**

1. **Type Inference**: Let TypeScript infer types when possible
2. **Constraints**: Use `extends` to ensure type capabilities
3. **Conditional Types**: Create types that change based on conditions
4. **Mapped Types**: Transform existing types systematically
5. **Generic Composition**: Combine multiple generics for complex scenarios

These patterns show how generics evolve from simple type parameters to powerful tools for building type-safe, reusable systems.