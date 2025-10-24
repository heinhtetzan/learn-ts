I'll help you get started with TypeScript and shadcn/ui! Here's a comprehensive guide:

## 1. Installation and Setup

First, create a new Next.js project with TypeScript:

```bash
npx create-next-app@latest my-app --typescript --tailwind --eslint
cd my-app
```

Then install shadcn/ui:

```bash
npx shadcn-ui@latest init
```

During initialization, you'll be prompted to configure:
- Style: default
- Base color: slate
- CSS variables: yes
- Global CSS: src/app/globals.css
- Components: src/components
- Utils: src/lib/utils

## 2. Basic Component Example

Let's create a simple form component using shadcn/ui with TypeScript:

```typescript
// components/user-form.tsx
'use client';

import { useState } from 'react';
import { Button } from '@/components/ui/button';
import { Input } from '@/components/ui/input';
import { Label } from '@/components/ui/label';
import { Card, CardContent, CardDescription, CardHeader, CardTitle } from '@/components/ui/card';
import { Alert, AlertDescription } from '@/components/ui/alert';

interface UserFormData {
  name: string;
  email: string;
  age: number;
}

export function UserForm() {
  const [formData, setFormData] = useState<UserFormData>({
    name: '',
    email: '',
    age: 0,
  });

  const [errors, setErrors] = useState<Partial<UserFormData>>({});

  const validateForm = (): boolean => {
    const newErrors: Partial<UserFormData> = {};

    if (!formData.name.trim()) {
      newErrors.name = 'Name is required';
    }

    if (!formData.email.includes('@')) {
      newErrors.email = 'Valid email is required';
    }

    if (formData.age < 18) {
      newErrors.age = 'Must be 18 or older';
    }

    setErrors(newErrors);
    return Object.keys(newErrors).length === 0;
  };

  const handleSubmit = (e: React.FormEvent) => {
    e.preventDefault();
    
    if (validateForm()) {
      // Handle form submission
      console.log('Form data:', formData);
    }
  };

  const handleInputChange = (field: keyof UserFormData, value: string) => {
    setFormData(prev => ({
      ...prev,
      [field]: field === 'age' ? parseInt(value) || 0 : value,
    }));
    
    // Clear error when user starts typing
    if (errors[field]) {
      setErrors(prev => ({
        ...prev,
        [field]: undefined,
      }));
    }
  };

  return (
    <Card className="w-full max-w-md">
      <CardHeader>
        <CardTitle>User Registration</CardTitle>
        <CardDescription>Enter your details to create an account.</CardDescription>
      </CardHeader>
      <CardContent>
        <form onSubmit={handleSubmit} className="space-y-4">
          <div className="space-y-2">
            <Label htmlFor="name">Name</Label>
            <Input
              id="name"
              type="text"
              value={formData.name}
              onChange={(e) => handleInputChange('name', e.target.value)}
              className={errors.name ? 'border-red-500' : ''}
            />
            {errors.name && (
              <Alert variant="destructive" className="py-2">
                <AlertDescription>{errors.name}</AlertDescription>
              </Alert>
            )}
          </div>

          <div className="space-y-2">
            <Label htmlFor="email">Email</Label>
            <Input
              id="email"
              type="email"
              value={formData.email}
              onChange={(e) => handleInputChange('email', e.target.value)}
              className={errors.email ? 'border-red-500' : ''}
            />
            {errors.email && (
              <Alert variant="destructive" className="py-2">
                <AlertDescription>{errors.email}</AlertDescription>
              </Alert>
            )}
          </div>

          <div className="space-y-2">
            <Label htmlFor="age">Age</Label>
            <Input
              id="age"
              type="number"
              value={formData.age}
              onChange={(e) => handleInputChange('age', e.target.value)}
              className={errors.age ? 'border-red-500' : ''}
            />
            {errors.age && (
              <Alert variant="destructive" className="py-2">
                <AlertDescription>{errors.age}</AlertDescription>
              </Alert>
            )}
          </div>

          <Button type="submit" className="w-full">
            Submit
          </Button>
        </form>
      </CardContent>
    </Card>
  );
}
```

## 3. Data Table with TypeScript

Here's an example of a typed data table:

```typescript
// components/data-table.tsx
'use client';

import { useState } from 'react';
import {
  Table,
  TableBody,
  TableCell,
  TableHead,
  TableHeader,
  TableRow,
} from '@/components/ui/table';
import { Button } from '@/components/ui/button';
import { Badge } from '@/components/ui/badge';
import { Input } from '@/components/ui/input';

interface User {
  id: string;
  name: string;
  email: string;
  role: 'admin' | 'user' | 'moderator';
  status: 'active' | 'inactive';
}

const mockUsers: User[] = [
  {
    id: '1',
    name: 'John Doe',
    email: 'john@example.com',
    role: 'admin',
    status: 'active',
  },
  {
    id: '2',
    name: 'Jane Smith',
    email: 'jane@example.com',
    role: 'user',
    status: 'active',
  },
  {
    id: '3',
    name: 'Bob Johnson',
    email: 'bob@example.com',
    role: 'moderator',
    status: 'inactive',
  },
];

export function UsersTable() {
  const [users, setUsers] = useState<User[]>(mockUsers);
  const [search, setSearch] = useState('');

  const filteredUsers = users.filter(user =>
    user.name.toLowerCase().includes(search.toLowerCase()) ||
    user.email.toLowerCase().includes(search.toLowerCase())
  );

  const getStatusColor = (status: User['status']) => {
    return status === 'active' ? 'bg-green-500' : 'bg-red-500';
  };

  const getRoleColor = (role: User['role']) => {
    const colors = {
      admin: 'bg-blue-500',
      moderator: 'bg-purple-500',
      user: 'bg-gray-500',
    };
    return colors[role];
  };

  return (
    <div className="space-y-4">
      <Input
        placeholder="Search users..."
        value={search}
        onChange={(e) => setSearch(e.target.value)}
        className="max-w-sm"
      />
      
      <Table>
        <TableHeader>
          <TableRow>
            <TableHead>Name</TableHead>
            <TableHead>Email</TableHead>
            <TableHead>Role</TableHead>
            <TableHead>Status</TableHead>
            <TableHead>Actions</TableHead>
          </TableRow>
        </TableHeader>
        <TableBody>
          {filteredUsers.map((user) => (
            <TableRow key={user.id}>
              <TableCell className="font-medium">{user.name}</TableCell>
              <TableCell>{user.email}</TableCell>
              <TableCell>
                <Badge variant="secondary" className={getRoleColor(user.role)}>
                  {user.role}
                </Badge>
              </TableCell>
              <TableCell>
                <Badge variant="secondary" className={getStatusColor(user.status)}>
                  {user.status}
                </Badge>
              </TableCell>
              <TableCell>
                <Button variant="outline" size="sm">
                  Edit
                </Button>
              </TableCell>
            </TableRow>
          ))}
        </TableBody>
      </Table>
    </div>
  );
}
```

## 4. Custom Hook with TypeScript

Create a custom hook for form management:

```typescript
// hooks/use-form.ts
import { useState, useCallback } from 'react';

interface UseFormOptions<T> {
  initialValues: T;
  validate?: (values: T) => Partial<Record<keyof T, string>>;
  onSubmit: (values: T) => void | Promise<void>;
}

export function useForm<T extends Record<string, any>>({
  initialValues,
  validate,
  onSubmit,
}: UseFormOptions<T>) {
  const [values, setValues] = useState<T>(initialValues);
  const [errors, setErrors] = useState<Partial<Record<keyof T, string>>>({});
  const [isSubmitting, setIsSubmitting] = useState(false);

  const handleChange = useCallback((name: keyof T, value: any) => {
    setValues(prev => ({ ...prev, [name]: value }));
    
    // Clear error when field changes
    if (errors[name]) {
      setErrors(prev => ({ ...prev, [name]: undefined }));
    }
  }, [errors]);

  const handleSubmit = useCallback(async (e: React.FormEvent) => {
    e.preventDefault();
    setIsSubmitting(true);

    // Validate form
    if (validate) {
      const validationErrors = validate(values);
      if (Object.keys(validationErrors).length > 0) {
        setErrors(validationErrors);
        setIsSubmitting(false);
        return;
      }
    }

    try {
      await onSubmit(values);
    } catch (error) {
      console.error('Form submission error:', error);
    } finally {
      setIsSubmitting(false);
    }
  }, [values, validate, onSubmit]);

  return {
    values,
    errors,
    isSubmitting,
    handleChange,
    handleSubmit,
    setValues,
  };
}
```

## 5. Page Component

```typescript
// app/page.tsx
import { UserForm } from '@/components/user-form';
import { UsersTable } from '@/components/data-table';

export default function Home() {
  return (
    <main className="container mx-auto py-8 space-y-8">
      <div className="text-center">
        <h1 className="text-4xl font-bold tracking-tight">
          shadcn/ui + TypeScript
        </h1>
        <p className="text-muted-foreground mt-2">
          Beautifully designed components built with Radix UI and Tailwind CSS
        </p>
      </div>

      <div className="grid grid-cols-1 lg:grid-cols-2 gap-8">
        <UserForm />
        <div className="space-y-4">
          <h2 className="text-2xl font-semibold">Users</h2>
          <UsersTable />
        </div>
      </div>
    </main>
  );
}
```

## Key Benefits of Using TypeScript with shadcn/ui:

1. **Type Safety**: Full type checking for all components and props
2. **Better Autocomplete**: IDE support for component props and utilities
3. **Refactoring**: Easy refactoring with confidence
4. **Error Prevention**: Catch errors at compile time rather than runtime
5. **Better Documentation**: Types serve as built-in documentation

## Useful Commands

Add more shadcn/ui components:
```bash
npx shadcn-ui@latest add dialog
npx shadcn-ui@latest add dropdown-menu
npx shadcn-ui@latest add toast
```

This setup gives you a solid foundation for building type-safe applications with shadcn/ui and TypeScript!