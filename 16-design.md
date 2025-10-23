# Software Design in React using TypeScript

## **Architectural Patterns & Principles**

### 1. **Component Design Patterns**

#### **Container/Presentational Pattern**

**Definition**: Separates concerns by splitting components into:
- **Container Components**: Handle business logic, data fetching, state management
- **Presentational Components**: Handle UI rendering, styling, user interactions

**Usage**: When you want to separate business logic from UI concerns for better reusability and testability.

```tsx
// Container (Logic Component)
interface User {
  id: number;
  name: string;
  email: string;
}

interface UsersContainerProps {
  userId?: number;
}

// Definition: Handles data logic and state management
const UsersContainer: React.FC<UsersContainerProps> = ({ userId }) => {
  const [users, setUsers] = useState<User[]>([]);
  const [loading, setLoading] = useState(false);
  const [error, setError] = useState<string | null>(null);

  useEffect(() => {
    fetchUsers();
  }, [userId]);

  const fetchUsers = async () => {
    setLoading(true);
    setError(null);
    try {
      const response = await api.getUsers(userId);
      setUsers(response.data);
    } catch (err) {
      setError('Failed to fetch users');
    } finally {
      setLoading(false);
    }
  };

  // Usage: Connects data logic with presentational component
  return (
    <UsersList 
      users={users} 
      loading={loading}
      error={error}
      onRefresh={fetchUsers}
    />
  );
};

// Presentational (UI Component)
interface UsersListProps {
  users: User[];
  loading: boolean;
  error: string | null;
  onRefresh: () => void;
}

// Definition: Pure UI component, no business logic
const UsersList: React.FC<UsersListProps> = ({ 
  users, 
  loading, 
  error, 
  onRefresh 
}) => {
  if (loading) return <LoadingSpinner />;
  if (error) return <ErrorMessage message={error} />;
  
  return (
    <div className="users-container">
      <button onClick={onRefresh} className="refresh-btn">
        Refresh
      </button>
      <div className="users-grid">
        {users.map(user => (
          <UserCard key={user.id} user={user} />
        ))}
      </div>
    </div>
  );
};
```

#### **Compound Components Pattern**

**Definition**: A pattern where multiple components work together sharing implicit state through context, providing a flexible and expressive API.

**Usage**: When building complex components like forms, menus, or data tables where child components need to share state and coordinate behavior.

```tsx
import React, { createContext, useContext, useState } from 'react';

// Context Definition
interface FormContextType {
  values: Record<string, any>;
  errors: Record<string, string>;
  setValue: (name: string, value: any) => void;
  validateField: (name: string) => void;
}

const FormContext = createContext<FormContextType | undefined>(undefined);

// Hook for context usage
const useForm = () => {
  const context = useContext(FormContext);
  if (!context) {
    throw new Error('useForm must be used within a Form component');
  }
  return context;
};

// Main Form Component Definition
interface FormProps {
  children: React.ReactNode;
  onSubmit: (values: Record<string, any>) => void;
  initialValues?: Record<string, any>;
}

// Usage: Provides form state management to child components
const Form: React.FC<FormProps> & {
  Input: typeof Input;
  Submit: typeof Submit;
  Error: typeof ErrorMessage;
} = ({ children, onSubmit, initialValues = {} }) => {
  const [values, setValues] = useState(initialValues);
  const [errors, setErrors] = useState<Record<string, string>>({});

  const setValue = (name: string, value: any) => {
    setValues(prev => ({ ...prev, [name]: value }));
    // Clear error when user starts typing
    if (errors[name]) {
      setErrors(prev => ({ ...prev, [name]: '' }));
    }
  };

  const validateField = (name: string) => {
    // Simple validation logic
    if (!values[name]) {
      setErrors(prev => ({ ...prev, [name]: 'This field is required' }));
    }
  };

  const handleSubmit = (e: React.FormEvent) => {
    e.preventDefault();
    // Validate all fields
    Object.keys(values).forEach(validateField);
    
    if (Object.values(errors).every(error => !error)) {
      onSubmit(values);
    }
  };

  const contextValue: FormContextType = {
    values,
    errors,
    setValue,
    validateField
  };

  return (
    <FormContext.Provider value={contextValue}>
      <form onSubmit={handleSubmit}>{children}</form>
    </FormContext.Provider>
  );
};

// Input Component Definition
interface InputProps {
  name: string;
  type?: 'text' | 'email' | 'password';
  placeholder?: string;
  required?: boolean;
}

// Usage: Individual form input that shares form state
const Input: React.FC<InputProps> = ({ name, type = 'text', placeholder, required }) => {
  const { values, errors, setValue, validateField } = useForm();

  return (
    <div className="form-input">
      <input
        type={type}
        value={values[name] || ''}
        onChange={(e) => setValue(name, e.target.value)}
        onBlur={() => required && validateField(name)}
        placeholder={placeholder}
        className={errors[name] ? 'error' : ''}
      />
      {errors[name] && <span className="error-message">{errors[name]}</span>}
    </div>
  );
};

// Submit Component Definition
interface SubmitProps {
  children: React.ReactNode;
}

// Usage: Form submit button
const Submit: React.FC<SubmitProps> = ({ children }) => {
  return (
    <button type="submit" className="submit-btn">
      {children}
    </button>
  );
};

// Error Component Definition
interface ErrorProps {
  name: string;
}

// Usage: Display error for specific field
const ErrorMessage: React.FC<ErrorProps> = ({ name }) => {
  const { errors } = useForm();
  
  if (!errors[name]) return null;
  
  return <span className="field-error">{errors[name]}</span>;
};

// Attach compound components
Form.Input = Input;
Form.Submit = Submit;
Form.Error = ErrorMessage;

// Usage Example of Compound Pattern
const UserRegistrationForm: React.FC = () => {
  const handleSubmit = (values: Record<string, any>) => {
    console.log('Form submitted:', values);
  };

  return (
    <Form onSubmit={handleSubmit} initialValues={{ username: '', email: '' }}>
      <Form.Input name="username" placeholder="Username" required />
      <Form.Error name="username" />
      
      <Form.Input name="email" type="email" placeholder="Email" required />
      <Form.Error name="email" />
      
      <Form.Submit>Register</Form.Submit>
    </Form>
  );
};
```

### 2. **Custom Hooks Pattern**

**Definition**: Extract and reuse stateful logic into reusable functions that can be shared across components.

**Usage**: When you have complex state logic, side effects, or data fetching that needs to be reused across multiple components.

```tsx
// Custom Hook Definition
interface UseApiResult<T> {
  data: T | null;
  loading: boolean;
  error: string | null;
  refetch: () => Promise<void>;
}

// Definition: Reusable API call logic
function useApi<T>(url: string, initialData: T | null = null): UseApiResult<T> {
  const [data, setData] = useState<T | null>(initialData);
  const [loading, setLoading] = useState(false);
  const [error, setError] = useState<string | null>(null);

  const fetchData = async () => {
    setLoading(true);
    setError(null);
    
    try {
      const response = await fetch(url);
      if (!response.ok) throw new Error('API request failed');
      
      const result = await response.json();
      setData(result);
    } catch (err) {
      setError(err instanceof Error ? err.message : 'An error occurred');
    } finally {
      setLoading(false);
    }
  };

  useEffect(() => {
    fetchData();
  }, [url]);

  return { data, loading, error, refetch: fetchData };
}

// Usage in Component
interface User {
  id: number;
  name: string;
  email: string;
}

const UserProfile: React.FC<{ userId: number }> = ({ userId }) => {
  // Usage: Clean component with extracted logic
  const { data: user, loading, error, refetch } = useApi<User>(`/api/users/${userId}`);

  if (loading) return <div>Loading...</div>;
  if (error) return <div>Error: {error}</div>;
  if (!user) return <div>No user found</div>;

  return (
    <div>
      <h1>{user.name}</h1>
      <p>{user.email}</p>
      <button onClick={refetch}>Reload</button>
    </div>
  );
};
```

### 3. **Higher-Order Component (HOC) Pattern**

**Definition**: A function that takes a component and returns a new component with additional props or functionality.

**Usage**: When you need to share cross-cutting concerns like authentication, logging, or data fetching across multiple components.

```tsx
// HOC Definition
interface WithLoadingProps {
  loading: boolean;
}

// Definition: Adds loading functionality to any component
function withLoading<P extends object>(
  WrappedComponent: React.ComponentType<P & WithLoadingProps>
): React.FC<P & { isLoading?: boolean }> {
  return function WithLoadingComponent({ isLoading = false, ...props }: P & { isLoading?: boolean }) {
    return <WrappedComponent {...(props as P)} loading={isLoading} />;
  };
}

// Usage: Enhance existing components with loading state
interface UserCardProps {
  user: User;
  loading: boolean;
}

const UserCard: React.FC<UserCardProps> = ({ user, loading }) => {
  if (loading) return <div className="skeleton-loader">Loading...</div>;
  
  return (
    <div className="user-card">
      <h3>{user.name}</h3>
      <p>{user.email}</p>
    </div>
  );
};

// Enhanced component with loading capability
const UserCardWithLoading = withLoading(UserCard);

// Usage in parent component
const UserList: React.FC = () => {
  const [users, setUsers] = useState<User[]>([]);
  const [loading, setLoading] = useState(true);

  return (
    <div>
      {users.map(user => (
        <UserCardWithLoading 
          key={user.id} 
          user={user} 
          isLoading={loading}
        />
      ))}
    </div>
  );
};
```

### 4. **Render Props Pattern**

**Definition**: A technique for sharing code between components using a prop whose value is a function that returns a React element.

**Usage**: When you need to share dynamic or complex state logic while giving the consumer component full control over rendering.

```tsx
// Render Props Component Definition
interface DataFetcherProps<T> {
  url: string;
  children: (props: {
    data: T | null;
    loading: boolean;
    error: string | null;
    refetch: () => void;
  }) => React.ReactNode;
}

// Definition: Provides data fetching logic with render prop
function DataFetcher<T>({ url, children }: DataFetcherProps<T>): React.ReactElement {
  const [data, setData] = useState<T | null>(null);
  const [loading, setLoading] = useState(true);
  const [error, setError] = useState<string | null>(null);

  const fetchData = async () => {
    setLoading(true);
    setError(null);
    
    try {
      const response = await fetch(url);
      const result = await response.json();
      setData(result);
    } catch (err) {
      setError('Failed to fetch data');
    } finally {
      setLoading(false);
    }
  };

  useEffect(() => {
    fetchData();
  }, [url]);

  return <>{children({ data, loading, error, refetch: fetchData })}</>;
}

// Usage: Consumer controls rendering with data provided
const UserProfileWithRenderProps: React.FC<{ userId: number }> = ({ userId }) => {
  return (
    <DataFetcher<User> url={`/api/users/${userId}`}>
      {({ data: user, loading, error, refetch }) => {
        if (loading) return <div>Loading user...</div>;
        if (error) return <div>Error: {error}</div>;
        if (!user) return <div>No user found</div>;

        return (
          <div className="user-profile">
            <h2>{user.name}</h2>
            <p>Email: {user.email}</p>
            <button onClick={refetch}>Reload Data</button>
          </div>
        );
      }}
    </DataFetcher>
  );
};
```

### 5. **Provider Pattern**

**Definition**: Uses React Context to provide global state and functionality to components deep in the tree without prop drilling.

**Usage**: For global state management, theme configuration, user authentication, or any data that needs to be accessible across many components.

```tsx
// Theme Context Definition
interface Theme {
  mode: 'light' | 'dark';
  colors: {
    primary: string;
    secondary: string;
    background: string;
    text: string;
  };
}

interface ThemeContextType {
  theme: Theme;
  toggleTheme: () => void;
}

const ThemeContext = createContext<ThemeContextType | undefined>(undefined);

// Theme Provider Definition
interface ThemeProviderProps {
  children: React.ReactNode;
}

// Definition: Provides theme context to entire app
export const ThemeProvider: React.FC<ThemeProviderProps> = ({ children }) => {
  const [theme, setTheme] = useState<Theme>(lightTheme);

  const toggleTheme = () => {
    setTheme(current => current.mode === 'light' ? darkTheme : lightTheme);
  };

  return (
    <ThemeContext.Provider value={{ theme, toggleTheme }}>
      {children}
    </ThemeContext.Provider>
  );
};

// Custom hook for theme usage
export const useTheme = (): ThemeContextType => {
  const context = useContext(ThemeContext);
  if (!context) {
    throw new Error('useTheme must be used within a ThemeProvider');
  }
  return context;
};

// Usage in components
const ThemedButton: React.FC = () => {
  const { theme, toggleTheme } = useTheme();

  return (
    <button
      onClick={toggleTheme}
      style={{
        backgroundColor: theme.colors.primary,
        color: theme.colors.text
      }}
    >
      Toggle {theme.mode === 'light' ? 'Dark' : 'Light'} Mode
    </button>
  );
};

// App usage
const App: React.FC = () => {
  return (
    <ThemeProvider>
      <ThemedButton />
      {/* Other components have access to theme */}
    </ThemeProvider>
  );
};
```

## **When to Use Each Pattern:**

| Pattern | Use Case | Pros | Cons |
|---------|----------|------|------|
| **Container/Presentational** | Separating logic from UI | Clean separation, reusable UI | More files, potential over-engineering |
| **Compound Components** | Complex interactive components | Flexible API, implicit state sharing | Learning curve, context dependency |
| **Custom Hooks** | Reusable stateful logic | Composition, testability, reuse | Can become complex if overused |
| **HOC** | Cross-cutting concerns | Code reuse, composition | Prop collisions, debugging complexity |
| **Render Props** | Dynamic rendering logic | Flexibility, explicit control | Callback hell, nesting complexity |
| **Provider Pattern** | Global state/configuration | Avoids prop drilling, centralized state | Re-render concerns, overuse can complicate app |

## **Best Practices:**

1. **Start Simple**: Begin with basic components and refactor into patterns as complexity grows
2. **Type Everything**: Leverage TypeScript for props, state, and function signatures
3. **Single Responsibility**: Each pattern should solve one specific problem
4. **Composition over Inheritance**: Prefer composing patterns rather than deep inheritance
5. **Testability**: Design patterns should make testing easier, not harder

These patterns help create maintainable, scalable, and type-safe React applications with TypeScript.