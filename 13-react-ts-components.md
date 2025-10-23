Here are different ways to write React components with TypeScript using Function Components (FC) and arrow functions:

## 1. Basic Arrow Function Component with FC

```tsx
import React from 'react';

interface UserProps {
  name: string;
  age: number;
  email?: string;
}

const User: React.FC<UserProps> = ({ name, age, email }) => {
  return (
    <div>
      <h2>{name}</h2>
      <p>Age: {age}</p>
      {email && <p>Email: {email}</p>}
    </div>
  );
};

export default User;
```

## 2. Arrow Function Component without FC (Recommended)

```tsx
import React from 'react';

interface ButtonProps {
  children: React.ReactNode;
  onClick: () => void;
  variant?: 'primary' | 'secondary';
  disabled?: boolean;
}

const Button = ({ 
  children, 
  onClick, 
  variant = 'primary', 
  disabled = false 
}: ButtonProps) => {
  return (
    <button
      className={`btn btn-${variant}`}
      onClick={onClick}
      disabled={disabled}
    >
      {children}
    </button>
  );
};

export default Button;
```

## 3. Component with Children

```tsx
import React from 'react';

interface CardProps {
  title: string;
  children: React.ReactNode;
  footer?: React.ReactNode;
}

const Card: React.FC<CardProps> = ({ title, children, footer }) => {
  return (
    <div className="card">
      <div className="card-header">
        <h3>{title}</h3>
      </div>
      <div className="card-body">
        {children}
      </div>
      {footer && (
        <div className="card-footer">
          {footer}
        </div>
      )}
    </div>
  );
};

export default Card;
```

## 4. Component with Event Handlers

```tsx
import React, { ChangeEvent } from 'react';

interface InputProps {
  value: string;
  onChange: (value: string) => void;
  placeholder?: string;
  type?: 'text' | 'email' | 'password';
}

const Input = ({ 
  value, 
  onChange, 
  placeholder = '', 
  type = 'text' 
}: InputProps) => {
  const handleChange = (e: ChangeEvent<HTMLInputElement>) => {
    onChange(e.target.value);
  };

  return (
    <input
      type={type}
      value={value}
      onChange={handleChange}
      placeholder={placeholder}
      className="input-field"
    />
  );
};

export default Input;
```

## 5. Component with useState and useEffect

```tsx
import React, { useState, useEffect } from 'react';

interface CounterProps {
  initialValue?: number;
  step?: number;
}

const Counter: React.FC<CounterProps> = ({ 
  initialValue = 0, 
  step = 1 
}) => {
  const [count, setCount] = useState<number>(initialValue);

  useEffect(() => {
    document.title = `Count: ${count}`;
  }, [count]);

  const increment = () => setCount(prev => prev + step);
  const decrement = () => setCount(prev => prev - step);
  const reset = () => setCount(initialValue);

  return (
    <div className="counter">
      <h2>Count: {count}</h2>
      <div className="counter-buttons">
        <button onClick={decrement}>-</button>
        <button onClick={reset}>Reset</button>
        <button onClick={increment}>+</button>
      </div>
    </div>
  );
};

export default Counter;
```

## 6. Component with Generic Props

```tsx
import React from 'react';

interface ListProps<T> {
  items: T[];
  renderItem: (item: T) => React.ReactNode;
  keyExtractor: (item: T) => string;
  className?: string;
}

const List = <T,>({ 
  items, 
  renderItem, 
  keyExtractor, 
  className = '' 
}: ListProps<T>) => {
  return (
    <div className={`list ${className}`}>
      {items.map(item => (
        <div key={keyExtractor(item)} className="list-item">
          {renderItem(item)}
        </div>
      ))}
    </div>
  );
};

export default List;
```

## 7. Component with Default Props

```tsx
import React from 'react';

interface AvatarProps {
  src?: string;
  alt: string;
  size?: 'sm' | 'md' | 'lg';
  fallback?: string;
}

const Avatar: React.FC<AvatarProps> = ({ 
  src, 
  alt, 
  size = 'md', 
  fallback = '👤' 
}) => {
  const [imgError, setImgError] = React.useState(false);

  const handleError = () => setImgError(true);

  if (!src || imgError) {
    return (
      <div className={`avatar avatar-${size} avatar-fallback`}>
        {fallback}
      </div>
    );
  }

  return (
    <img
      src={src}
      alt={alt}
      onError={handleError}
      className={`avatar avatar-${size}`}
    />
  );
};

export default Avatar;
```

## 8. Component with Forward Ref

```tsx
import React, { forwardRef } from 'react';

interface CustomInputProps {
  label: string;
  error?: string;
}

const CustomInput = forwardRef<HTMLInputElement, CustomInputProps>(
  ({ label, error, ...props }, ref) => {
    return (
      <div className="form-group">
        <label>{label}</label>
        <input
          ref={ref}
          {...props}
          className={`form-input ${error ? 'error' : ''}`}
        />
        {error && <span className="error-message">{error}</span>}
      </div>
    );
  }
);

CustomInput.displayName = 'CustomInput';

export default CustomInput;
```

## 9. Compound Components Pattern

```tsx
import React, { createContext, useContext } from 'react';

interface FormContextType {
  values: Record<string, string>;
  setValue: (name: string, value: string) => void;
}

const FormContext = createContext<FormContextType | undefined>(undefined);

interface FormProps {
  children: React.ReactNode;
  onSubmit: (values: Record<string, string>) => void;
}

const Form: React.FC<FormProps> & {
  Field: typeof FormField;
} = ({ children, onSubmit }) => {
  const [values, setValues] = React.useState<Record<string, string>>({});

  const setValue = (name: string, value: string) => {
    setValues(prev => ({ ...prev, [name]: value }));
  };

  const handleSubmit = (e: React.FormEvent) => {
    e.preventDefault();
    onSubmit(values);
  };

  return (
    <FormContext.Provider value={{ values, setValue }}>
      <form onSubmit={handleSubmit}>
        {children}
      </form>
    </FormContext.Provider>
  );
};

interface FormFieldProps {
  name: string;
  children: React.ReactNode;
}

const FormField: React.FC<FormFieldProps> = ({ name, children }) => {
  const context = useContext(FormContext);
  
  if (!context) {
    throw new Error('FormField must be used within a Form');
  }

  const { setValue } = context;

  const enhancedChildren = React.Children.map(children, child => {
    if (React.isValidElement(child)) {
      return React.cloneElement(child, {
        onChange: (value: string) => setValue(name, value),
        value: context.values[name] || ''
      } as any);
    }
    return child;
  });

  return <div className="form-field">{enhancedChildren}</div>;
};

Form.Field = FormField;

export default Form;
```

## Key Points:

1. **React.FC vs Regular Props**: 
   - `React.FC` includes `children` implicitly and has better default generic behavior
   - Regular props are more explicit and preferred in modern React

2. **Type Safety**: Always define interfaces for props
3. **Optional Props**: Use `?` for optional properties
4. **Event Types**: Use React's built-in event types (`ChangeEvent`, `MouseEvent`, etc.)
5. **Generic Components**: Use generics for reusable components with different data types

Choose the pattern that best fits your use case! The non-FC approach is generally recommended for new projects as it's more explicit and flexible.