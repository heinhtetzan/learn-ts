Here's a comprehensive example of a React component using SWR with TypeScript:

## Basic Component with SWR

```tsx
import useSWR from 'swr';
import React from 'react';

// Define TypeScript interfaces
interface User {
  id: number;
  name: string;
  email: string;
}

interface ApiResponse {
  users: User[];
  total: number;
}

// Fetcher function
const fetcher = async (url: string): Promise<ApiResponse> => {
  const response = await fetch(url);
  if (!response.ok) {
    throw new Error('Failed to fetch data');
  }
  return response.json();
};

const UsersList: React.FC = () => {
  const { data, error, isLoading, isValidating, mutate } = useSWR<ApiResponse>(
    'https://jsonplaceholder.typicode.com/users',
    fetcher,
    {
      revalidateOnFocus: false,
      refreshInterval: 30000, // Refresh every 30 seconds
    }
  );

  if (error) {
    return (
      <div className="p-4 bg-red-100 border border-red-400 text-red-700 rounded">
        <p>Failed to load users: {error.message}</p>
        <button
          onClick={() => mutate()}
          className="mt-2 px-4 py-2 bg-red-500 text-white rounded hover:bg-red-600"
        >
          Retry
        </button>
      </div>
    );
  }

  if (isLoading) {
    return (
      <div className="p-4">
        <div className="animate-pulse space-y-4">
          {[...Array(5)].map((_, i) => (
            <div key={i} className="h-4 bg-gray-300 rounded"></div>
          ))}
        </div>
      </div>
    );
  }

  if (!data) {
    return <div>No data available</div>;
  }

  return (
    <div className="p-4">
      <div className="flex justify-between items-center mb-4">
        <h1 className="text-2xl font-bold">Users List</h1>
        {isValidating && (
          <span className="text-sm text-blue-500">Updating...</span>
        )}
        <button
          onClick={() => mutate()}
          className="px-4 py-2 bg-blue-500 text-white rounded hover:bg-blue-600"
        >
          Refresh
        </button>
      </div>

      <div className="space-y-2">
        {data.users?.map((user) => (
          <div
            key={user.id}
            className="p-4 border border-gray-200 rounded-lg hover:bg-gray-50"
          >
            <h3 className="font-semibold">{user.name}</h3>
            <p className="text-gray-600">{user.email}</p>
          </div>
        ))}
      </div>
    </div>
  );
};

export default UsersList;
```

## Advanced Example with Parameters

```tsx
import useSWR from 'swr';
import React, { useState } from 'react';

interface Post {
  id: number;
  title: string;
  body: string;
  userId: number;
}

interface PostParams {
  userId?: number;
  page?: number;
}

const fetcher = async (url: string): Promise<Post[]> => {
  const response = await fetch(url);
  if (!response.ok) {
    throw new Error('Failed to fetch posts');
  }
  return response.json();
};

const PostsComponent: React.FC = () => {
  const [userId, setUserId] = useState<number | undefined>();
  const [page, setPage] = useState(1);

  // Construct the URL with parameters
  const url = userId 
    ? `https://jsonplaceholder.typicode.com/posts?userId=${userId}&_page=${page}&_limit=10`
    : `https://jsonplaceholder.typicode.com/posts?_page=${page}&_limit=10`;

  const { data: posts, error, isLoading, mutate } = useSWR<Post[]>(
    url,
    fetcher,
    {
      keepPreviousData: true, // Keep previous data while loading new data
    }
  );

  const handleRefresh = async () => {
    // Manually revalidate the data
    await mutate();
  };

  const handleAddPost = async () => {
    const newPost: Post = {
      id: Date.now(), // Temporary ID
      title: 'New Post',
      body: 'This is a new post',
      userId: 1,
    };

    // Optimistic update
    await mutate([newPost, ...(posts || [])], false);
    
    // In a real app, you would make an API call here
    // then revalidate the data
  };

  if (error) {
    return (
      <div className="p-4 bg-red-100 border border-red-400 text-red-700 rounded">
        Error loading posts: {error.message}
      </div>
    );
  }

  return (
    <div className="p-4">
      <div className="mb-4 space-y-4">
        <div className="flex gap-4">
          <input
            type="number"
            placeholder="Filter by user ID"
            value={userId || ''}
            onChange={(e) => setUserId(e.target.value ? Number(e.target.value) : undefined)}
            className="border p-2 rounded"
          />
          <button
            onClick={handleRefresh}
            disabled={isLoading}
            className="px-4 py-2 bg-blue-500 text-white rounded hover:bg-blue-600 disabled:opacity-50"
          >
            {isLoading ? 'Loading...' : 'Refresh'}
          </button>
          <button
            onClick={handleAddPost}
            className="px-4 py-2 bg-green-500 text-white rounded hover:bg-green-600"
          >
            Add Post
          </button>
        </div>

        <div className="flex gap-2">
          <button
            onClick={() => setPage(prev => Math.max(1, prev - 1))}
            disabled={page === 1}
            className="px-3 py-1 bg-gray-200 rounded disabled:opacity-50"
          >
            Previous
          </button>
          <span className="px-3 py-1">Page {page}</span>
          <button
            onClick={() => setPage(prev => prev + 1)}
            className="px-3 py-1 bg-gray-200 rounded"
          >
            Next
          </button>
        </div>
      </div>

      {isLoading && !posts ? (
        <div className="space-y-2">
          {[...Array(5)].map((_, i) => (
            <div key={i} className="h-20 bg-gray-200 animate-pulse rounded"></div>
          ))}
        </div>
      ) : (
        <div className="space-y-3">
          {posts?.map((post) => (
            <div key={post.id} className="p-4 border rounded-lg">
              <h3 className="font-bold text-lg">{post.title}</h3>
              <p className="text-gray-600">{post.body}</p>
              <div className="text-sm text-gray-500 mt-2">
                User ID: {post.userId}
              </div>
            </div>
          ))}
        </div>
      )}
    </div>
  );
};

export default PostsComponent;
```

## Custom Hook with SWR

```tsx
import useSWR from 'swr';
import { useCallback } from 'react';

interface Todo {
  id: number;
  title: string;
  completed: boolean;
}

// Custom hook for todos
export const useTodos = () => {
  const { data, error, mutate, ...rest } = useSWR<Todo[]>(
    'https://jsonplaceholder.typicode.com/todos',
    async (url: string) => {
      const response = await fetch(url);
      if (!response.ok) throw new Error('Failed to fetch todos');
      return response.json();
    }
  );

  const addTodo = useCallback(async (title: string) => {
    const newTodo: Todo = {
      id: Date.now(),
      title,
      completed: false,
    };

    // Optimistic update
    await mutate([newTodo, ...(data || [])], false);
    
    // In real app, make API call then revalidate
    // await fetch('/api/todos', { method: 'POST', body: JSON.stringify(newTodo) });
    // mutate();
  }, [data, mutate]);

  const toggleTodo = useCallback(async (id: number) => {
    const updatedTodos = data?.map(todo =>
      todo.id === id ? { ...todo, completed: !todo.completed } : todo
    );

    await mutate(updatedTodos, false);
    
    // In real app, make API call then revalidate
    // await fetch(`/api/todos/${id}`, { method: 'PATCH' });
    // mutate();
  }, [data, mutate]);

  return {
    todos: data,
    error,
    addTodo,
    toggleTodo,
    ...rest,
  };
};

// Component using the custom hook
const TodosComponent: React.FC = () => {
  const { todos, error, isLoading, addTodo, toggleTodo } = useTodos();

  if (error) {
    return <div>Error: {error.message}</div>;
  }

  return (
    <div className="p-4">
      <h1 className="text-2xl font-bold mb-4">Todos</h1>
      
      <button
        onClick={() => addTodo(`New Todo ${Date.now()}`)}
        className="mb-4 px-4 py-2 bg-blue-500 text-white rounded"
      >
        Add Todo
      </button>

      {isLoading ? (
        <div>Loading...</div>
      ) : (
        <div className="space-y-2">
          {todos?.slice(0, 10).map((todo) => (
            <div
              key={todo.id}
              className={`p-2 border rounded cursor-pointer ${
                todo.completed ? 'bg-green-100' : 'bg-white'
              }`}
              onClick={() => toggleTodo(todo.id)}
            >
              <span className={todo.completed ? 'line-through' : ''}>
                {todo.title}
              </span>
            </div>
          ))}
        </div>
      )}
    </div>
  );
};

export default TodosComponent;
```

## Key Features Demonstrated:

1. **TypeScript Integration**: Full type safety with interfaces
2. **Loading States**: Proper handling of loading and error states
3. **Optimistic Updates**: Immediate UI updates with manual mutation
4. **Pagination**: URL-based pagination with parameters
5. **Custom Hooks**: Reusable data fetching logic
6. **Error Handling**: Comprehensive error handling with retry
7. **Configuration**: SWR options for caching and revalidation

## Installation Requirements:

```bash
npm install swr
# or
yarn add swr
```

These examples show different patterns for using SWR with React and TypeScript, from basic data fetching to more advanced scenarios with optimistic updates and custom hooks.