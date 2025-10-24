I'll show you how to create an invoice state management system using Zustand with TypeScript. Here's a comprehensive example:

## 1. First, install the required dependencies:

```bash
npm install zustand
```

## 2. Define Types

```typescript
// types/invoice.ts
export interface InvoiceItem {
  id: string;
  description: string;
  quantity: number;
  price: number;
  total: number;
}

export interface Invoice {
  id: string;
  number: string;
  date: string;
  dueDate: string;
  client: {
    name: string;
    email: string;
    address: string;
  };
  items: InvoiceItem[];
  subtotal: number;
  tax: number;
  total: number;
  status: 'draft' | 'sent' | 'paid' | 'overdue';
  notes?: string;
}

export interface InvoiceState {
  invoices: Invoice[];
  currentInvoice: Invoice | null;
  isLoading: boolean;
  error: string | null;
}
```

## 3. Create the Store

```typescript
// stores/invoiceStore.ts
import { create } from 'zustand';
import { Invoice, InvoiceItem, InvoiceState } from '../types/invoice';

interface InvoiceActions {
  // Invoice management
  setInvoices: (invoices: Invoice[]) => void;
  addInvoice: (invoice: Invoice) => void;
  updateInvoice: (id: string, invoice: Partial<Invoice>) => void;
  deleteInvoice: (id: string) => void;
  
  // Current invoice operations
  setCurrentInvoice: (invoice: Invoice | null) => void;
  createNewInvoice: () => void;
  addItemToCurrentInvoice: (item: Omit<InvoiceItem, 'id' | 'total'>) => void;
  updateItemInCurrentInvoice: (itemId: string, updates: Partial<InvoiceItem>) => void;
  removeItemFromCurrentInvoice: (itemId: string) => void;
  calculateCurrentInvoiceTotals: () => void;
  
  // Utility
  setLoading: (loading: boolean) => void;
  setError: (error: string | null) => void;
  clearError: () => void;
}

const initialInvoiceState: Invoice = {
  id: '',
  number: '',
  date: new Date().toISOString().split('T')[0],
  dueDate: new Date(Date.now() + 30 * 24 * 60 * 60 * 1000).toISOString().split('T')[0],
  client: {
    name: '',
    email: '',
    address: '',
  },
  items: [],
  subtotal: 0,
  tax: 0,
  total: 0,
  status: 'draft',
  notes: '',
};

export const useInvoiceStore = create<InvoiceState & InvoiceActions>((set, get) => ({
  // Initial state
  invoices: [],
  currentInvoice: null,
  isLoading: false,
  error: null,

  // Actions
  setInvoices: (invoices) => set({ invoices }),

  addInvoice: (invoice) => 
    set((state) => ({ 
      invoices: [...state.invoices, invoice],
      currentInvoice: null 
    })),

  updateInvoice: (id, updates) =>
    set((state) => ({
      invoices: state.invoices.map((invoice) =>
        invoice.id === id ? { ...invoice, ...updates } : invoice
      ),
      currentInvoice: state.currentInvoice?.id === id 
        ? { ...state.currentInvoice, ...updates } 
        : state.currentInvoice,
    })),

  deleteInvoice: (id) =>
    set((state) => ({
      invoices: state.invoices.filter((invoice) => invoice.id !== id),
      currentInvoice: state.currentInvoice?.id === id ? null : state.currentInvoice,
    })),

  setCurrentInvoice: (invoice) => set({ currentInvoice: invoice }),

  createNewInvoice: () => {
    const newInvoice: Invoice = {
      ...initialInvoiceState,
      id: `invoice-${Date.now()}`,
      number: `INV-${Date.now()}`,
    };
    set({ currentInvoice: newInvoice });
  },

  addItemToCurrentInvoice: (itemData) => {
    const state = get();
    if (!state.currentInvoice) return;

    const newItem: InvoiceItem = {
      ...itemData,
      id: `item-${Date.now()}`,
      total: itemData.quantity * itemData.price,
    };

    const updatedInvoice = {
      ...state.currentInvoice,
      items: [...state.currentInvoice.items, newItem],
    };

    set({ currentInvoice: updatedInvoice });
    get().calculateCurrentInvoiceTotals();
  },

  updateItemInCurrentInvoice: (itemId, updates) => {
    const state = get();
    if (!state.currentInvoice) return;

    const updatedItems = state.currentInvoice.items.map((item) =>
      item.id === itemId ? { ...item, ...updates } : item
    );

    // Recalculate total if quantity or price changes
    if (updates.quantity !== undefined || updates.price !== undefined) {
      const item = updatedItems.find((i) => i.id === itemId);
      if (item) {
        item.total = item.quantity * item.price;
      }
    }

    const updatedInvoice = {
      ...state.currentInvoice,
      items: updatedItems,
    };

    set({ currentInvoice: updatedInvoice });
    get().calculateCurrentInvoiceTotals();
  },

  removeItemFromCurrentInvoice: (itemId) => {
    const state = get();
    if (!state.currentInvoice) return;

    const updatedInvoice = {
      ...state.currentInvoice,
      items: state.currentInvoice.items.filter((item) => item.id !== itemId),
    };

    set({ currentInvoice: updatedInvoice });
    get().calculateCurrentInvoiceTotals();
  },

  calculateCurrentInvoiceTotals: () => {
    const state = get();
    if (!state.currentInvoice) return;

    const subtotal = state.currentInvoice.items.reduce(
      (sum, item) => sum + item.total,
      0
    );
    
    const tax = subtotal * 0.1; // 10% tax for example
    const total = subtotal + tax;

    set({
      currentInvoice: {
        ...state.currentInvoice,
        subtotal,
        tax,
        total,
      },
    });
  },

  setLoading: (loading) => set({ isLoading: loading }),
  setError: (error) => set({ error }),
  clearError: () => set({ error: null }),
}));
```

## 4. Custom Hooks for Specific Functionality

```typescript
// hooks/useInvoices.ts
import { useInvoiceStore } from '../stores/invoiceStore';

export const useInvoices = () => {
  const invoices = useInvoiceStore((state) => state.invoices);
  const isLoading = useInvoiceStore((state) => state.isLoading);
  const error = useInvoiceStore((state) => state.error);
  
  const { setInvoices, addInvoice, updateInvoice, deleteInvoice } = useInvoiceStore();

  return {
    invoices,
    isLoading,
    error,
    setInvoices,
    addInvoice,
    updateInvoice,
    deleteInvoice,
  };
};

export const useCurrentInvoice = () => {
  const currentInvoice = useInvoiceStore((state) => state.currentInvoice);
  
  const {
    setCurrentInvoice,
    createNewInvoice,
    addItemToCurrentInvoice,
    updateItemInCurrentInvoice,
    removeItemFromCurrentInvoice,
    calculateCurrentInvoiceTotals,
  } = useInvoiceStore();

  return {
    currentInvoice,
    setCurrentInvoice,
    createNewInvoice,
    addItemToCurrentInvoice,
    updateItemInCurrentInvoice,
    removeItemFromCurrentInvoice,
    calculateCurrentInvoiceTotals,
  };
};
```

## 5. Example Usage in React Components

```tsx
// components/InvoiceList.tsx
import React from 'react';
import { useInvoices } from '../hooks/useInvoices';

export const InvoiceList: React.FC = () => {
  const { invoices, deleteInvoice, setLoading } = useInvoices();

  const handleDelete = async (id: string) => {
    setLoading(true);
    try {
      // Simulate API call
      await new Promise(resolve => setTimeout(resolve, 500));
      deleteInvoice(id);
    } catch (error) {
      console.error('Failed to delete invoice:', error);
    } finally {
      setLoading(false);
    }
  };

  return (
    <div className="invoice-list">
      <h2>Invoices</h2>
      {invoices.map((invoice) => (
        <div key={invoice.id} className="invoice-item">
          <span>#{invoice.number} - {invoice.client.name}</span>
          <span>${invoice.total}</span>
          <span className={`status ${invoice.status}`}>{invoice.status}</span>
          <button onClick={() => handleDelete(invoice.id)}>Delete</button>
        </div>
      ))}
    </div>
  );
};
```

```tsx
// components/InvoiceForm.tsx
import React, { useState } from 'react';
import { useCurrentInvoice } from '../hooks/useCurrentInvoice';

export const InvoiceForm: React.FC = () => {
  const { 
    currentInvoice, 
    addItemToCurrentInvoice, 
    removeItemFromCurrentInvoice,
    updateItemInCurrentInvoice,
    calculateCurrentInvoiceTotals 
  } = useCurrentInvoice();

  const [newItem, setNewItem] = useState({
    description: '',
    quantity: 1,
    price: 0,
  });

  const handleAddItem = () => {
    if (newItem.description && newItem.price > 0) {
      addItemToCurrentInvoice(newItem);
      setNewItem({ description: '', quantity: 1, price: 0 });
    }
  };

  if (!currentInvoice) {
    return <div>No invoice selected</div>;
  }

  return (
    <div className="invoice-form">
      <h2>Invoice #{currentInvoice.number}</h2>
      
      <div className="items-section">
        <h3>Items</h3>
        {currentInvoice.items.map((item) => (
          <div key={item.id} className="invoice-item">
            <input
              value={item.description}
              onChange={(e) => 
                updateItemInCurrentInvoice(item.id, { description: e.target.value })
              }
            />
            <input
              type="number"
              value={item.quantity}
              onChange={(e) => 
                updateItemInCurrentInvoice(item.id, { quantity: parseInt(e.target.value) })
              }
            />
            <input
              type="number"
              value={item.price}
              onChange={(e) => 
                updateItemInCurrentInvoice(item.id, { price: parseFloat(e.target.value) })
              }
            />
            <span>${item.total}</span>
            <button onClick={() => removeItemFromCurrentInvoice(item.id)}>
              Remove
            </button>
          </div>
        ))}
        
        <div className="add-item">
          <input
            placeholder="Description"
            value={newItem.description}
            onChange={(e) => setNewItem({ ...newItem, description: e.target.value })}
          />
          <input
            type="number"
            placeholder="Quantity"
            value={newItem.quantity}
            onChange={(e) => setNewItem({ ...newItem, quantity: parseInt(e.target.value) })}
          />
          <input
            type="number"
            placeholder="Price"
            value={newItem.price}
            onChange={(e) => setNewItem({ ...newItem, price: parseFloat(e.target.value) })}
          />
          <button onClick={handleAddItem}>Add Item</button>
        </div>
      </div>

      <div className="totals">
        <p>Subtotal: ${currentInvoice.subtotal.toFixed(2)}</p>
        <p>Tax: ${currentInvoice.tax.toFixed(2)}</p>
        <p><strong>Total: ${currentInvoice.total.toFixed(2)}</strong></p>
      </div>
    </div>
  );
};
```

## Key Features:

1. **Type Safety**: Full TypeScript support with proper interfaces
2. **Immutability**: Zustand handles immutability automatically
3. **Separation of Concerns**: State, actions, and derived state are clearly separated
4. **Computed Values**: Automatic calculation of invoice totals
5. **Error Handling**: Built-in loading and error states
6. **Modular**: Custom hooks for specific functionality

This setup provides a robust foundation for managing invoice state in a React application with TypeScript and Zustand!