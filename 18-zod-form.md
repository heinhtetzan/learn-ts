I'll create a comprehensive student registration form using React, TypeScript, React Hook Form, and Zod. Here's the complete implementation:

## 1. First, install the required dependencies:

```bash
npm install react-hook-form @hookform/resolvers zod
```

## 2. Define TypeScript interfaces and Zod schema:

```typescript
// types/student.ts
export interface StudentFormData {
  firstName: string;
  lastName: string;
  email: string;
  phone: string;
  dateOfBirth: string;
  gender: 'male' | 'female' | 'other';
  address: string;
  city: string;
  state: string;
  zipCode: string;
  country: string;
  course: string;
  semester: number;
  emergencyContactName: string;
  emergencyContactPhone: string;
  termsAccepted: boolean;
}

export interface StudentFormProps {
  onSubmit: (data: StudentFormData) => void;
  isLoading?: boolean;
}
```

## 3. Create the Zod validation schema:

```typescript
// schemas/studentSchema.ts
import { z } from 'zod';

export const studentSchema = z.object({
  firstName: z.string()
    .min(2, 'First name must be at least 2 characters')
    .max(50, 'First name must be less than 50 characters')
    .regex(/^[a-zA-Z\s]+$/, 'First name can only contain letters and spaces'),
  
  lastName: z.string()
    .min(2, 'Last name must be at least 2 characters')
    .max(50, 'Last name must be less than 50 characters')
    .regex(/^[a-zA-Z\s]+$/, 'Last name can only contain letters and spaces'),
  
  email: z.string()
    .email('Please enter a valid email address'),
  
  phone: z.string()
    .min(10, 'Phone number must be at least 10 digits')
    .regex(/^\+?[\d\s-]+$/, 'Please enter a valid phone number'),
  
  dateOfBirth: z.string()
    .refine((date) => {
      const birthDate = new Date(date);
      const today = new Date();
      const age = today.getFullYear() - birthDate.getFullYear();
      return age >= 16 && age <= 100;
    }, 'Student must be between 16 and 100 years old'),
  
  gender: z.enum(['male', 'female', 'other'], {
    required_error: 'Please select a gender',
  }),
  
  address: z.string()
    .min(5, 'Address must be at least 5 characters')
    .max(100, 'Address must be less than 100 characters'),
  
  city: z.string()
    .min(2, 'City must be at least 2 characters')
    .max(50, 'City must be less than 50 characters'),
  
  state: z.string()
    .min(2, 'State must be at least 2 characters')
    .max(50, 'State must be less than 50 characters'),
  
  zipCode: z.string()
    .regex(/^\d{5}(-\d{4})?$/, 'Please enter a valid ZIP code'),
  
  country: z.string()
    .min(2, 'Country must be at least 2 characters')
    .max(50, 'Country must be less than 50 characters'),
  
  course: z.string()
    .min(1, 'Please select a course'),
  
  semester: z.number()
    .min(1, 'Semester must be at least 1')
    .max(12, 'Semester must be at most 12'),
  
  emergencyContactName: z.string()
    .min(2, 'Emergency contact name must be at least 2 characters')
    .max(50, 'Emergency contact name must be less than 50 characters'),
  
  emergencyContactPhone: z.string()
    .min(10, 'Emergency contact phone must be at least 10 digits')
    .regex(/^\+?[\d\s-]+$/, 'Please enter a valid phone number'),
  
  termsAccepted: z.boolean()
    .refine((val) => val === true, 'You must accept the terms and conditions'),
});

export type StudentFormData = z.infer<typeof studentSchema>;
```

## 4. Create the main form component:

```tsx
// components/StudentRegistrationForm.tsx
import React from 'react';
import { useForm } from 'react-hook-form';
import { zodResolver } from '@hookform/resolvers/zod';
import { studentSchema, StudentFormData } from '../schemas/studentSchema';

interface StudentFormProps {
  onSubmit: (data: StudentFormData) => void;
  isLoading?: boolean;
}

const StudentRegistrationForm: React.FC<StudentFormProps> = ({ 
  onSubmit, 
  isLoading = false 
}) => {
  const {
    register,
    handleSubmit,
    formState: { errors, isSubmitting },
    watch,
    reset
  } = useForm<StudentFormData>({
    resolver: zodResolver(studentSchema),
    defaultValues: {
      gender: 'male',
      semester: 1,
      country: 'United States',
      termsAccepted: false
    }
  });

  const courses = [
    'Computer Science',
    'Electrical Engineering',
    'Mechanical Engineering',
    'Civil Engineering',
    'Business Administration',
    'Medicine',
    'Law',
    'Arts and Humanities'
  ];

  const handleFormSubmit = async (data: StudentFormData) => {
    await onSubmit(data);
    reset();
  };

  const isSubmittingForm = isSubmitting || isLoading;

  return (
    <div className="max-w-4xl mx-auto p-6 bg-white rounded-lg shadow-lg">
      <h2 className="text-2xl font-bold text-gray-800 mb-6 text-center">
        Student Registration Form
      </h2>

      <form onSubmit={handleSubmit(handleFormSubmit)} className="space-y-6">
        {/* Personal Information Section */}
        <section className="border-b pb-6">
          <h3 className="text-lg font-semibold text-gray-700 mb-4">
            Personal Information
          </h3>
          
          <div className="grid grid-cols-1 md:grid-cols-2 gap-4">
            <div>
              <label htmlFor="firstName" className="block text-sm font-medium text-gray-700 mb-1">
                First Name *
              </label>
              <input
                {...register('firstName')}
                type="text"
                id="firstName"
                className="w-full px-3 py-2 border border-gray-300 rounded-md focus:outline-none focus:ring-2 focus:ring-blue-500 focus:border-transparent"
                placeholder="Enter first name"
              />
              {errors.firstName && (
                <p className="mt-1 text-sm text-red-600">{errors.firstName.message}</p>
              )}
            </div>

            <div>
              <label htmlFor="lastName" className="block text-sm font-medium text-gray-700 mb-1">
                Last Name *
              </label>
              <input
                {...register('lastName')}
                type="text"
                id="lastName"
                className="w-full px-3 py-2 border border-gray-300 rounded-md focus:outline-none focus:ring-2 focus:ring-blue-500 focus:border-transparent"
                placeholder="Enter last name"
              />
              {errors.lastName && (
                <p className="mt-1 text-sm text-red-600">{errors.lastName.message}</p>
              )}
            </div>
          </div>

          <div className="grid grid-cols-1 md:grid-cols-2 gap-4 mt-4">
            <div>
              <label htmlFor="email" className="block text-sm font-medium text-gray-700 mb-1">
                Email Address *
              </label>
              <input
                {...register('email')}
                type="email"
                id="email"
                className="w-full px-3 py-2 border border-gray-300 rounded-md focus:outline-none focus:ring-2 focus:ring-blue-500 focus:border-transparent"
                placeholder="student@example.com"
              />
              {errors.email && (
                <p className="mt-1 text-sm text-red-600">{errors.email.message}</p>
              )}
            </div>

            <div>
              <label htmlFor="phone" className="block text-sm font-medium text-gray-700 mb-1">
                Phone Number *
              </label>
              <input
                {...register('phone')}
                type="tel"
                id="phone"
                className="w-full px-3 py-2 border border-gray-300 rounded-md focus:outline-none focus:ring-2 focus:ring-blue-500 focus:border-transparent"
                placeholder="+1 (555) 123-4567"
              />
              {errors.phone && (
                <p className="mt-1 text-sm text-red-600">{errors.phone.message}</p>
              )}
            </div>
          </div>

          <div className="grid grid-cols-1 md:grid-cols-2 gap-4 mt-4">
            <div>
              <label htmlFor="dateOfBirth" className="block text-sm font-medium text-gray-700 mb-1">
                Date of Birth *
              </label>
              <input
                {...register('dateOfBirth')}
                type="date"
                id="dateOfBirth"
                className="w-full px-3 py-2 border border-gray-300 rounded-md focus:outline-none focus:ring-2 focus:ring-blue-500 focus:border-transparent"
              />
              {errors.dateOfBirth && (
                <p className="mt-1 text-sm text-red-600">{errors.dateOfBirth.message}</p>
              )}
            </div>

            <div>
              <label htmlFor="gender" className="block text-sm font-medium text-gray-700 mb-1">
                Gender *
              </label>
              <select
                {...register('gender')}
                id="gender"
                className="w-full px-3 py-2 border border-gray-300 rounded-md focus:outline-none focus:ring-2 focus:ring-blue-500 focus:border-transparent"
              >
                <option value="male">Male</option>
                <option value="female">Female</option>
                <option value="other">Other</option>
              </select>
              {errors.gender && (
                <p className="mt-1 text-sm text-red-600">{errors.gender.message}</p>
              )}
            </div>
          </div>
        </section>

        {/* Address Information Section */}
        <section className="border-b pb-6">
          <h3 className="text-lg font-semibold text-gray-700 mb-4">
            Address Information
          </h3>
          
          <div className="mb-4">
            <label htmlFor="address" className="block text-sm font-medium text-gray-700 mb-1">
              Street Address *
            </label>
            <input
              {...register('address')}
              type="text"
              id="address"
              className="w-full px-3 py-2 border border-gray-300 rounded-md focus:outline-none focus:ring-2 focus:ring-blue-500 focus:border-transparent"
              placeholder="123 Main Street"
            />
            {errors.address && (
              <p className="mt-1 text-sm text-red-600">{errors.address.message}</p>
            )}
          </div>

          <div className="grid grid-cols-1 md:grid-cols-4 gap-4">
            <div className="md:col-span-2">
              <label htmlFor="city" className="block text-sm font-medium text-gray-700 mb-1">
                City *
              </label>
              <input
                {...register('city')}
                type="text"
                id="city"
                className="w-full px-3 py-2 border border-gray-300 rounded-md focus:outline-none focus:ring-2 focus:ring-blue-500 focus:border-transparent"
                placeholder="New York"
              />
              {errors.city && (
                <p className="mt-1 text-sm text-red-600">{errors.city.message}</p>
              )}
            </div>

            <div>
              <label htmlFor="state" className="block text-sm font-medium text-gray-700 mb-1">
                State *
              </label>
              <input
                {...register('state')}
                type="text"
                id="state"
                className="w-full px-3 py-2 border border-gray-300 rounded-md focus:outline-none focus:ring-2 focus:ring-blue-500 focus:border-transparent"
                placeholder="NY"
              />
              {errors.state && (
                <p className="mt-1 text-sm text-red-600">{errors.state.message}</p>
              )}
            </div>

            <div>
              <label htmlFor="zipCode" className="block text-sm font-medium text-gray-700 mb-1">
                ZIP Code *
              </label>
              <input
                {...register('zipCode')}
                type="text"
                id="zipCode"
                className="w-full px-3 py-2 border border-gray-300 rounded-md focus:outline-none focus:ring-2 focus:ring-blue-500 focus:border-transparent"
                placeholder="10001"
              />
              {errors.zipCode && (
                <p className="mt-1 text-sm text-red-600">{errors.zipCode.message}</p>
              )}
            </div>
          </div>

          <div className="mt-4">
            <label htmlFor="country" className="block text-sm font-medium text-gray-700 mb-1">
              Country *
            </label>
            <input
              {...register('country')}
              type="text"
              id="country"
              className="w-full px-3 py-2 border border-gray-300 rounded-md focus:outline-none focus:ring-2 focus:ring-blue-500 focus:border-transparent"
              placeholder="United States"
            />
            {errors.country && (
              <p className="mt-1 text-sm text-red-600">{errors.country.message}</p>
            )}
          </div>
        </section>

        {/* Academic Information Section */}
        <section className="border-b pb-6">
          <h3 className="text-lg font-semibold text-gray-700 mb-4">
            Academic Information
          </h3>
          
          <div className="grid grid-cols-1 md:grid-cols-2 gap-4">
            <div>
              <label htmlFor="course" className="block text-sm font-medium text-gray-700 mb-1">
                Course/Program *
              </label>
              <select
                {...register('course')}
                id="course"
                className="w-full px-3 py-2 border border-gray-300 rounded-md focus:outline-none focus:ring-2 focus:ring-blue-500 focus:border-transparent"
              >
                <option value="">Select a course</option>
                {courses.map((course) => (
                  <option key={course} value={course}>
                    {course}
                  </option>
                ))}
              </select>
              {errors.course && (
                <p className="mt-1 text-sm text-red-600">{errors.course.message}</p>
              )}
            </div>

            <div>
              <label htmlFor="semester" className="block text-sm font-medium text-gray-700 mb-1">
                Semester *
              </label>
              <select
                {...register('semester', { valueAsNumber: true })}
                id="semester"
                className="w-full px-3 py-2 border border-gray-300 rounded-md focus:outline-none focus:ring-2 focus:ring-blue-500 focus:border-transparent"
              >
                {Array.from({ length: 12 }, (_, i) => i + 1).map((sem) => (
                  <option key={sem} value={sem}>
                    Semester {sem}
                  </option>
                ))}
              </select>
              {errors.semester && (
                <p className="mt-1 text-sm text-red-600">{errors.semester.message}</p>
              )}
            </div>
          </div>
        </section>

        {/* Emergency Contact Section */}
        <section className="border-b pb-6">
          <h3 className="text-lg font-semibold text-gray-700 mb-4">
            Emergency Contact
          </h3>
          
          <div className="grid grid-cols-1 md:grid-cols-2 gap-4">
            <div>
              <label htmlFor="emergencyContactName" className="block text-sm font-medium text-gray-700 mb-1">
                Emergency Contact Name *
              </label>
              <input
                {...register('emergencyContactName')}
                type="text"
                id="emergencyContactName"
                className="w-full px-3 py-2 border border-gray-300 rounded-md focus:outline-none focus:ring-2 focus:ring-blue-500 focus:border-transparent"
                placeholder="John Doe"
              />
              {errors.emergencyContactName && (
                <p className="mt-1 text-sm text-red-600">{errors.emergencyContactName.message}</p>
              )}
            </div>

            <div>
              <label htmlFor="emergencyContactPhone" className="block text-sm font-medium text-gray-700 mb-1">
                Emergency Contact Phone *
              </label>
              <input
                {...register('emergencyContactPhone')}
                type="tel"
                id="emergencyContactPhone"
                className="w-full px-3 py-2 border border-gray-300 rounded-md focus:outline-none focus:ring-2 focus:ring-blue-500 focus:border-transparent"
                placeholder="+1 (555) 987-6543"
              />
              {errors.emergencyContactPhone && (
                <p className="mt-1 text-sm text-red-600">{errors.emergencyContactPhone.message}</p>
              )}
            </div>
          </div>
        </section>

        {/* Terms and Conditions */}
        <section>
          <div className="flex items-start space-x-3">
            <input
              {...register('termsAccepted')}
              type="checkbox"
              id="termsAccepted"
              className="mt-1 w-4 h-4 text-blue-600 border-gray-300 rounded focus:ring-blue-500"
            />
            <label htmlFor="termsAccepted" className="text-sm text-gray-700">
              I hereby declare that the information provided is true and correct to the best of my knowledge. 
              I agree to the terms and conditions of the institution. *
            </label>
          </div>
          {errors.termsAccepted && (
            <p className="mt-1 text-sm text-red-600">{errors.termsAccepted.message}</p>
          )}
        </section>

        {/* Submit Button */}
        <div className="flex justify-end space-x-4 pt-4">
          <button
            type="button"
            onClick={() => reset()}
            className="px-6 py-2 border border-gray-300 text-gray-700 rounded-md hover:bg-gray-50 focus:outline-none focus:ring-2 focus:ring-gray-500 focus:border-transparent transition-colors"
            disabled={isSubmittingForm}
          >
            Reset
          </button>
          <button
            type="submit"
            disabled={isSubmittingForm}
            className="px-6 py-2 bg-blue-600 text-white rounded-md hover:bg-blue-700 focus:outline-none focus:ring-2 focus:ring-blue-500 focus:ring-offset-2 disabled:opacity-50 disabled:cursor-not-allowed transition-colors"
          >
            {isSubmittingForm ? 'Submitting...' : 'Register Student'}
          </button>
        </div>
      </form>
    </div>
  );
};

export default StudentRegistrationForm;
```

## 5. Create a parent component to use the form:

```tsx
// components/StudentRegistrationPage.tsx
import React, { useState } from 'react';
import StudentRegistrationForm from './StudentRegistrationForm';
import { StudentFormData } from '../schemas/studentSchema';

const StudentRegistrationPage: React.FC = () => {
  const [submittedData, setSubmittedData] = useState<StudentFormData | null>(null);
  const [isLoading, setIsLoading] = useState(false);

  const handleSubmit = async (data: StudentFormData) => {
    setIsLoading(true);
    
    // Simulate API call
    await new Promise(resolve => setTimeout(resolve, 2000));
    
    console.log('Form submitted:', data);
    setSubmittedData(data);
    setIsLoading(false);
    
    // Here you would typically send the data to your backend
    // await api.registerStudent(data);
  };

  return (
    <div className="min-h-screen bg-gray-50 py-8">
      <div className="container mx-auto px-4">
        <StudentRegistrationForm 
          onSubmit={handleSubmit} 
          isLoading={isLoading} 
        />
        
        {submittedData && (
          <div className="mt-8 p-6 bg-green-50 border border-green-200 rounded-lg">
            <h3 className="text-lg font-semibold text-green-800 mb-2">
              Registration Successful!
            </h3>
            <pre className="text-sm text-green-700 whitespace-pre-wrap">
              {JSON.stringify(submittedData, null, 2)}
            </pre>
          </div>
        )}
      </div>
    </div>
  );
};

export default StudentRegistrationPage;
```

## 6. Usage in your main App component:

```tsx
// App.tsx
import React from 'react';
import StudentRegistrationPage from './components/StudentRegistrationPage';

function App() {
  return (
    <div className="App">
      <StudentRegistrationPage />
    </div>
  );
}

export default App;
```

## Key Features:

1. **Type Safety**: Full TypeScript support with proper interfaces
2. **Validation**: Comprehensive validation using Zod schema
3. **Form Management**: Efficient form handling with React Hook Form
4. **Error Handling**: Clear error messages for each field
5. **Accessibility**: Proper labels and ARIA attributes
6. **Responsive Design**: Mobile-friendly layout
7. **Loading States**: Proper loading and disabled states
8. **Form Reset**: Ability to reset the form

## Validation Rules:

- Required fields validation
- Email format validation
- Phone number format validation
- Age restriction (16-100 years)
- ZIP code format validation
- Character length limits
- Terms and conditions acceptance required

This form provides a complete student registration system with proper TypeScript types, validation, and user experience considerations.