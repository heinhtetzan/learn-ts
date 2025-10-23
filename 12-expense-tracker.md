I'll create a vanilla TypeScript expense tracker with Tailwind CSS and Vite. Here's the complete implementation:

## Project Structure
```
expense-tracker/
├── src/
│   ├── styles/
│   │   └── main.css
│   ├── types/
│   │   └── expense.ts
│   ├── utils/
│   │   └── storage.ts
│   ├── components/
│   │   ├── ExpenseForm.ts
│   │   ├── ExpenseList.ts
│   │   └── ExpenseSummary.ts
│   └── main.ts
├── index.html
├── package.json
├── tailwind.config.js
├── tsconfig.json
└── vite.config.ts
```

## 1. Package.json
```json
{
  "name": "expense-tracker",
  "private": true,
  "version": "1.0.0",
  "type": "module",
  "scripts": {
    "dev": "vite",
    "build": "tsc && vite build",
    "preview": "vite preview"
  },
  "devDependencies": {
    "@types/node": "^20.0.0",
    "autoprefixer": "^10.4.14",
    "postcss": "^8.4.24",
    "tailwindcss": "^3.3.0",
    "typescript": "^5.0.0",
    "vite": "^4.4.0"
  }
}
```

## 2. Type Definitions (src/types/expense.ts)
```typescript
export interface Expense {
  id: string;
  description: string;
  amount: number;
  category: string;
  date: string;
}

export interface ExpenseFormData {
  description: string;
  amount: string;
  category: string;
  date: string;
}
```

## 3. Storage Utility (src/utils/storage.ts)
```typescript
import { Expense } from '../types/expense.js';

const STORAGE_KEY = 'expenses';

export class StorageService {
  static getExpenses(): Expense[] {
    const expenses = localStorage.getItem(STORAGE_KEY);
    return expenses ? JSON.parse(expenses) : [];
  }

  static saveExpenses(expenses: Expense[]): void {
    localStorage.setItem(STORAGE_KEY, JSON.stringify(expenses));
  }

  static addExpense(expense: Omit<Expense, 'id'>): Expense {
    const expenses = this.getExpenses();
    const newExpense: Expense = {
      ...expense,
      id: Date.now().toString()
    };
    expenses.push(newExpense);
    this.saveExpenses(expenses);
    return newExpense;
  }

  static updateExpense(id: string, updates: Partial<Expense>): Expense | null {
    const expenses = this.getExpenses();
    const index = expenses.findIndex(exp => exp.id === id);
    
    if (index !== -1) {
      expenses[index] = { ...expenses[index], ...updates };
      this.saveExpenses(expenses);
      return expenses[index];
    }
    return null;
  }

  static deleteExpense(id: string): boolean {
    const expenses = this.getExpenses();
    const filteredExpenses = expenses.filter(exp => exp.id !== id);
    
    if (filteredExpenses.length !== expenses.length) {
      this.saveExpenses(filteredExpenses);
      return true;
    }
    return false;
  }
}
```

## 4. Expense Form Component (src/components/ExpenseForm.ts)
```typescript
import { Expense, ExpenseFormData } from '../types/expense.js';
import { StorageService } from '../utils/storage.js';

export class ExpenseForm {
  private form: HTMLFormElement;
  private onSubmitCallback: (expense: Expense) => void;
  private editingExpense: Expense | null = null;

  constructor(
    formId: string,
    onSubmit: (expense: Expense) => void
  ) {
    this.form = document.getElementById(formId) as HTMLFormElement;
    this.onSubmitCallback = onSubmit;
    this.init();
  }

  private init(): void {
    this.form.addEventListener('submit', this.handleSubmit.bind(this));
    this.setMinDate();
  }

  private setMinDate(): void {
    const dateInput = this.form.querySelector('#date') as HTMLInputElement;
    const today = new Date().toISOString().split('T')[0];
    dateInput.min = today;
  }

  private handleSubmit(e: Event): void {
    e.preventDefault();
    const formData = new FormData(this.form);
    
    const expenseData: ExpenseFormData = {
      description: formData.get('description') as string,
      amount: formData.get('amount') as string,
      category: formData.get('category') as string,
      date: formData.get('date') as string
    };

    if (this.validateForm(expenseData)) {
      const expense: Omit<Expense, 'id'> = {
        description: expenseData.description.trim(),
        amount: parseFloat(expenseData.amount),
        category: expenseData.category,
        date: expenseData.date
      };

      let savedExpense: Expense;
      
      if (this.editingExpense) {
        savedExpense = StorageService.updateExpense(this.editingExpense.id, expense)!;
        this.editingExpense = null;
      } else {
        savedExpense = StorageService.addExpense(expense);
      }

      this.onSubmitCallback(savedExpense);
      this.resetForm();
    }
  }

  private validateForm(data: ExpenseFormData): boolean {
    if (!data.description.trim()) {
      alert('Please enter a description');
      return false;
    }

    if (!data.amount || parseFloat(data.amount) <= 0) {
      alert('Please enter a valid amount');
      return false;
    }

    if (!data.category) {
      alert('Please select a category');
      return false;
    }

    if (!data.date) {
      alert('Please select a date');
      return false;
    }

    return true;
  }

  public editExpense(expense: Expense): void {
    this.editingExpense = expense;
    
    (this.form.querySelector('#description') as HTMLInputElement).value = expense.description;
    (this.form.querySelector('#amount') as HTMLInputElement).value = expense.amount.toString();
    (this.form.querySelector('#category') as HTMLSelectElement).value = expense.category;
    (this.form.querySelector('#date') as HTMLInputElement).value = expense.date;

    const submitButton = this.form.querySelector('button[type="submit"]') as HTMLButtonElement;
    submitButton.textContent = 'Update Expense';
    submitButton.className = 'bg-yellow-500 hover:bg-yellow-600 text-white font-bold py-2 px-4 rounded focus:outline-none focus:shadow-outline';
  }

  public resetForm(): void {
    this.form.reset();
    this.editingExpense = null;
    
    const submitButton = this.form.querySelector('button[type="submit"]') as HTMLButtonElement;
    submitButton.textContent = 'Add Expense';
    submitButton.className = 'bg-blue-500 hover:bg-blue-600 text-white font-bold py-2 px-4 rounded focus:outline-none focus:shadow-outline';
  }
}
```

## 5. Expense List Component (src/components/ExpenseList.ts)
```typescript
import { Expense } from '../types/expense.js';
import { StorageService } from '../utils/storage.js';

export class ExpenseList {
  private container: HTMLElement;
  private onEdit: (expense: Expense) => void;
  private onDelete: (id: string) => void;

  constructor(
    containerId: string,
    onEdit: (expense: Expense) => void,
    onDelete: (id: string) => void
  ) {
    this.container = document.getElementById(containerId)!;
    this.onEdit = onEdit;
    this.onDelete = onDelete;
  }

  public render(expenses: Expense[]): void {
    if (expenses.length === 0) {
      this.container.innerHTML = `
        <div class="text-center py-8 text-gray-500">
          <p>No expenses found. Add your first expense above!</p>
        </div>
      `;
      return;
    }

    const expensesHTML = expenses.map(expense => this.createExpenseCard(expense)).join('');
    this.container.innerHTML = `
      <div class="grid gap-4 md:grid-cols-2 lg:grid-cols-3">
        ${expensesHTML}
      </div>
    `;
  }

  private createExpenseCard(expense: Expense): string {
    return `
      <div class="bg-white rounded-lg shadow-md p-4 border-l-4 border-blue-500">
        <div class="flex justify-between items-start mb-2">
          <h3 class="font-semibold text-lg text-gray-800">${expense.description}</h3>
          <span class="text-red-600 font-bold">$${expense.amount.toFixed(2)}</span>
        </div>
        
        <div class="text-sm text-gray-600 mb-3">
          <div class="flex justify-between">
            <span>Category: ${expense.category}</span>
            <span>Date: ${new Date(expense.date).toLocaleDateString()}</span>
          </div>
        </div>
        
        <div class="flex justify-end space-x-2">
          <button 
            class="edit-btn bg-yellow-500 hover:bg-yellow-600 text-white px-3 py-1 rounded text-sm"
            data-id="${expense.id}"
          >
            Edit
          </button>
          <button 
            class="delete-btn bg-red-500 hover:bg-red-600 text-white px-3 py-1 rounded text-sm"
            data-id="${expense.id}"
          >
            Delete
          </button>
        </div>
      </div>
    `;
  }

  public bindEvents(): void {
    this.container.addEventListener('click', (e) => {
      const target = e.target as HTMLElement;
      
      if (target.classList.contains('edit-btn')) {
        const id = target.getAttribute('data-id')!;
        const expenses = StorageService.getExpenses();
        const expense = expenses.find(exp => exp.id === id);
        if (expense) this.onEdit(expense);
      }
      
      if (target.classList.contains('delete-btn')) {
        const id = target.getAttribute('data-id')!;
        if (confirm('Are you sure you want to delete this expense?')) {
          this.onDelete(id);
        }
      }
    });
  }
}
```

## 6. Expense Summary Component (src/components/ExpenseSummary.ts)
```typescript
import { Expense } from '../types/expense.js';

export class ExpenseSummary {
  private container: HTMLElement;

  constructor(containerId: string) {
    this.container = document.getElementById(containerId)!;
  }

  public render(expenses: Expense[]): void {
    const total = expenses.reduce((sum, expense) => sum + expense.amount, 0);
    const categoryTotals = this.calculateCategoryTotals(expenses);

    this.container.innerHTML = `
      <div class="bg-white rounded-lg shadow-md p-6">
        <h2 class="text-xl font-bold mb-4 text-gray-800">Expense Summary</h2>
        
        <div class="grid grid-cols-1 md:grid-cols-2 gap-4">
          <div class="bg-blue-50 p-4 rounded-lg">
            <h3 class="font-semibold text-blue-800">Total Expenses</h3>
            <p class="text-2xl font-bold text-blue-600">$${total.toFixed(2)}</p>
          </div>
          
          <div class="bg-green-50 p-4 rounded-lg">
            <h3 class="font-semibold text-green-800">Number of Expenses</h3>
            <p class="text-2xl font-bold text-green-600">${expenses.length}</p>
          </div>
        </div>

        ${expenses.length > 0 ? `
          <div class="mt-6">
            <h3 class="font-semibold mb-3 text-gray-700">By Category</h3>
            <div class="space-y-2">
              ${Object.entries(categoryTotals)
                .map(([category, amount]) => `
                  <div class="flex justify-between items-center">
                    <span class="text-gray-600">${category}</span>
                    <span class="font-semibold">$${amount.toFixed(2)}</span>
                  </div>
                `).join('')}
            </div>
          </div>
        ` : ''}
      </div>
    `;
  }

  private calculateCategoryTotals(expenses: Expense[]): Record<string, number> {
    return expenses.reduce((acc, expense) => {
      acc[expense.category] = (acc[expense.category] || 0) + expense.amount;
      return acc;
    }, {} as Record<string, number>);
  }
}
```

## 7. Main Application (src/main.ts)
```typescript
import { Expense } from './types/expense.js';
import { StorageService } from './utils/storage.js';
import { ExpenseForm } from './components/ExpenseForm.js';
import { ExpenseList } from './components/ExpenseList.js';
import { ExpenseSummary } from './components/ExpenseSummary.js';

class ExpenseTrackerApp {
  private expenseForm: ExpenseForm;
  private expenseList: ExpenseList;
  private expenseSummary: ExpenseSummary;
  private expenses: Expense[] = [];

  constructor() {
    this.init();
  }

  private init(): void {
    this.loadExpenses();
    this.initializeComponents();
    this.render();
  }

  private loadExpenses(): void {
    this.expenses = StorageService.getExpenses();
  }

  private initializeComponents(): void {
    // Initialize form
    this.expenseForm = new ExpenseForm(
      'expense-form',
      (expense: Expense) => {
        this.loadExpenses();
        this.render();
      }
    );

    // Initialize list
    this.expenseList = new ExpenseList(
      'expense-list',
      (expense: Expense) => {
        this.expenseForm.editExpense(expense);
      },
      (id: string) => {
        if (StorageService.deleteExpense(id)) {
          this.loadExpenses();
          this.render();
        }
      }
    );

    // Initialize summary
    this.expenseSummary = new ExpenseSummary('expense-summary');
  }

  private render(): void {
    this.expenseList.render(this.expenses);
    this.expenseList.bindEvents();
    this.expenseSummary.render(this.expenses);
  }
}

// Initialize app when DOM is loaded
document.addEventListener('DOMContentLoaded', () => {
  new ExpenseTrackerApp();
});
```

## 8. Main CSS (src/styles/main.css)
```css
@tailwind base;
@tailwind components;
@tailwind utilities;

body {
  font-family: 'Inter', sans-serif;
}

@layer components {
  .btn-primary {
    @apply bg-blue-500 hover:bg-blue-600 text-white font-bold py-2 px-4 rounded focus:outline-none focus:shadow-outline transition duration-200;
  }
  
  .btn-danger {
    @apply bg-red-500 hover:bg-red-600 text-white font-bold py-2 px-4 rounded focus:outline-none focus:shadow-outline transition duration-200;
  }
}
```

## 9. HTML (index.html)
```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Expense Tracker</title>
    <script type="module" src="/src/main.ts"></script>
</head>
<body class="bg-gray-100 min-h-screen">
    <div class="container mx-auto px-4 py-8">
        <header class="text-center mb-8">
            <h1 class="text-4xl font-bold text-gray-800 mb-2">Expense Tracker</h1>
            <p class="text-gray-600">Manage your expenses easily</p>
        </header>

        <main class="max-w-6xl mx-auto">
            <!-- Expense Summary -->
            <section class="mb-8">
                <div id="expense-summary"></div>
            </section>

            <!-- Expense Form -->
            <section class="mb-8">
                <div class="bg-white rounded-lg shadow-md p-6">
                    <h2 class="text-2xl font-bold mb-4 text-gray-800">
                        <span id="form-title">Add New Expense</span>
                    </h2>
                    <form id="expense-form" class="grid grid-cols-1 md:grid-cols-2 lg:grid-cols-4 gap-4">
                        <div>
                            <label for="description" class="block text-sm font-medium text-gray-700 mb-1">
                                Description
                            </label>
                            <input 
                                type="text" 
                                id="description" 
                                name="description"
                                class="w-full px-3 py-2 border border-gray-300 rounded-md focus:outline-none focus:ring-2 focus:ring-blue-500"
                                placeholder="Enter description"
                                required
                            >
                        </div>

                        <div>
                            <label for="amount" class="block text-sm font-medium text-gray-700 mb-1">
                                Amount ($)
                            </label>
                            <input 
                                type="number" 
                                id="amount" 
                                name="amount"
                                step="0.01"
                                min="0.01"
                                class="w-full px-3 py-2 border border-gray-300 rounded-md focus:outline-none focus:ring-2 focus:ring-blue-500"
                                placeholder="0.00"
                                required
                            >
                        </div>

                        <div>
                            <label for="category" class="block text-sm font-medium text-gray-700 mb-1">
                                Category
                            </label>
                            <select 
                                id="category" 
                                name="category"
                                class="w-full px-3 py-2 border border-gray-300 rounded-md focus:outline-none focus:ring-2 focus:ring-blue-500"
                                required
                            >
                                <option value="">Select Category</option>
                                <option value="Food">Food</option>
                                <option value="Transportation">Transportation</option>
                                <option value="Entertainment">Entertainment</option>
                                <option value="Utilities">Utilities</option>
                                <option value="Shopping">Shopping</option>
                                <option value="Healthcare">Healthcare</option>
                                <option value="Other">Other</option>
                            </select>
                        </div>

                        <div>
                            <label for="date" class="block text-sm font-medium text-gray-700 mb-1">
                                Date
                            </label>
                            <input 
                                type="date" 
                                id="date" 
                                name="date"
                                class="w-full px-3 py-2 border border-gray-300 rounded-md focus:outline-none focus:ring-2 focus:ring-blue-500"
                                required
                            >
                        </div>

                        <div class="md:col-span-2 lg:col-span-4">
                            <button 
                                type="submit" 
                                class="bg-blue-500 hover:bg-blue-600 text-white font-bold py-2 px-4 rounded focus:outline-none focus:shadow-outline w-full"
                            >
                                Add Expense
                            </button>
                        </div>
                    </form>
                </div>
            </section>

            <!-- Expense List -->
            <section>
                <div class="bg-white rounded-lg shadow-md p-6">
                    <h2 class="text-2xl font-bold mb-4 text-gray-800">Your Expenses</h2>
                    <div id="expense-list"></div>
                </div>
            </section>
        </main>
    </div>
</body>
</html>
```

## 10. Configuration Files

**tailwind.config.js**
```javascript
/** @type {import('tailwindcss').Config} */
export default {
  content: [
    "./index.html",
    "./src/**/*.{js,ts,jsx,tsx}",
  ],
  theme: {
    extend: {},
  },
  plugins: [],
}
```

**vite.config.ts**
```typescript
import { defineConfig } from 'vite';

export default defineConfig({
  server: {
    port: 3000,
  },
  build: {
    outDir: 'dist',
  },
});
```

**tsconfig.json**
```json
{
  "compilerOptions": {
    "target": "ES2020",
    "useDefineForClassFields": true,
    "module": "ESNext",
    "lib": ["ES2020", "DOM", "DOM.Iterable"],
    "skipLibCheck": true,
    "moduleResolution": "bundler",
    "allowImportingTsExtensions": true,
    "resolveJsonModule": true,
    "isolatedModules": true,
    "noEmit": true,
    "strict": true,
    "noUnusedLocals": true,
    "noUnusedParameters": true,
    "noFallthroughCasesInSwitch": true
  },
  "include": ["src"]
}
```

## 11. PostCSS Configuration (postcss.config.js)
```javascript
export default {
  plugins: {
    tailwindcss: {},
    autoprefixer: {},
  },
}
```

## Installation & Usage

1. **Install dependencies:**
```bash
npm install
```

2. **Run development server:**
```bash
npm run dev
```

3. **Build for production:**
```bash
npm run build
```

## Features

- ✅ **Add Expenses**: Full form validation with categories
- ✅ **Edit Expenses**: Click edit to populate form and update
- ✅ **Delete Expenses**: Confirmation before deletion
- ✅ **Calculate Total**: Real-time total and category breakdown
- ✅ **Local Storage**: Data persists between sessions
- ✅ **Responsive Design**: Works on all screen sizes
- ✅ **TypeScript**: Fully typed for better development experience
- ✅ **Tailwind CSS**: Modern, responsive styling

The application provides a complete expense tracking solution with a clean, modern interface and all the requested functionality!