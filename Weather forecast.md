# Micro-IT-Internship
<!doctype html>
<html lang="en">
  <head>
    <meta charset="UTF-8" />
    <link rel="icon" type="image/svg+xml" href="/vite.svg" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0" />
    <title>Weather Forecast</title>
  </head>
  <body>
    <div id="root"></div>
    <script type="module" src="/src/main.tsx"></script>
  </body>
</html>

{
  "name": "vite-react-typescript-starter",
  "private": true,
  "version": "0.0.0",
  "type": "module",
  "scripts": {
    "dev": "vite",
    "build": "vite build",
    "lint": "eslint .",
    "preview": "vite preview"
  },
  "dependencies": {
    "lucide-react": "^0.344.0",
    "react": "^18.3.1",
    "react-dom": "^18.3.1"
  },
  "devDependencies": {
    "@eslint/js": "^9.9.1",
    "@types/react": "^18.3.5",
    "@types/react-dom": "^18.3.0",
    "@vitejs/plugin-react": "^4.3.1",
    "autoprefixer": "^10.4.18",
    "eslint": "^9.9.1",
    "eslint-plugin-react-hooks": "^5.1.0-rc.0",
    "eslint-plugin-react-refresh": "^0.4.11",
    "globals": "^15.9.0",
    "postcss": "^8.4.35",
    "tailwindcss": "^3.4.1",
    "typescript": "^5.5.3",
    "typescript-eslint": "^8.3.0",
    "vite": "^5.4.2"
  }
}
export default {
  plugins: {
    tailwindcss: {},
    autoprefixer: {},
  },
};
/** @type {import('tailwindcss').Config} */
export default {
  content: ['./index.html', './src/**/*.{js,ts,jsx,tsx}'],
  theme: {
    extend: {},
  },
  plugins: [],
};
import js from '@eslint/js';
import globals from 'globals';
import reactHooks from 'eslint-plugin-react-hooks';
import reactRefresh from 'eslint-plugin-react-refresh';
import tseslint from 'typescript-eslint';

export default tseslint.config(
  { ignores: ['dist'] },
  {
    extends: [js.configs.recommended, ...tseslint.configs.recommended],
    files: ['**/*.{ts,tsx}'],
    languageOptions: {
      ecmaVersion: 2020,
      globals: globals.browser,
    },
    plugins: {
      'react-hooks': reactHooks,
      'react-refresh': reactRefresh,
    },
    rules: {
      ...reactHooks.configs.recommended.rules,
      'react-refresh/only-export-components': [
        'warn',
        { allowConstantExport: true },
      ],
    },
  }
);
import { StrictMode } from 'react';
import { createRoot } from 'react-dom/client';
import App from './App.tsx';
import './index.css';

createRoot(document.getElementById('root')!).render(
  <StrictMode>
    <App />
  </StrictMode>
);
@tailwind base;
@tailwind components;
@tailwind utilities;

@layer base {
  body {
    @apply bg-gray-50 text-gray-900;
  }
}

@layer components {
  .animate-fadeIn {
    animation: fadeIn 0.3s ease-in-out;
  }

  .animate-scaleX {
    animation: scaleX 0.3s ease-in-out forwards;
  }
}

@keyframes fadeIn {
  from {
    opacity: 0;
    transform: translateY(10px);
  }
  to {
    opacity: 1;
    transform: translateY(0);
  }
}

@keyframes scaleX {
  from {
    transform: scaleX(0);
  }
  to {
    transform: scaleX(1);
  }
}
import React, { useState } from 'react';
import WeatherDisplay from './components/WeatherDisplay';
import SearchBar from './components/SearchBar';
import { Cloud } from 'lucide-react';

function App() {
  const [weatherData, setWeatherData] = useState<any>(null);
  const [loading, setLoading] = useState(false);
  const [error, setError] = useState<string | null>(null);

  const handleSearch = async (city: string) => {
    setLoading(true);
    setError(null);
    try {
      const response = await fetch(
        `https://api.openweathermap.org/data/2.5/weather?q=${city}&units=metric&appid=${import.meta.env.VITE_WEATHER_API_KEY}`
      );
      if (!response.ok) throw new Error('City not found');
      const data = await response.json();
      setWeatherData(data);
    } catch (err) {
      setError(err instanceof Error ? err.message : 'Failed to fetch weather data');
    } finally {
      setLoading(false);
    }
  };

  return (
    <div 
      className="min-h-screen bg-cover bg-center bg-fixed flex items-center justify-center"
      style={{
        backgroundImage: 'linear-gradient(rgba(0, 0, 0, 0.5), rgba(0, 0, 0, 0.5)), url("https://images.pexels.com/photos/2559484/pexels-photo-2559484.jpeg")'
      }}
    >
      <div className="container mx-auto px-4 py-8">
        <header className="text-center mb-12">
          <Cloud size={48} className="text-sky-400 mx-auto mb-4" />
          <h1 className="text-4xl font-bold text-white mb-2">Weather Forecast</h1>
          <p className="text-sky-200">Check the weather in your city</p>
        </header>

        <div className="max-w-md mx-auto">
          <SearchBar onSearch={handleSearch} />
          
          {loading && (
            <div className="text-center py-8">
              <div className="animate-spin rounded-full h-12 w-12 border-b-2 border-sky-400 mx-auto"></div>
            </div>
          )}
          
          {error && (
            <div className="bg-red-500/80 backdrop-blur-sm text-white p-4 rounded-lg mt-4 text-center">
              {error}
            </div>
          )}
          
          {weatherData && !loading && <WeatherDisplay data={weatherData} />}
        </div>
      </div>
    </div>
  );
}

export default App;
import { Todo } from '../types/Todo';

const STORAGE_KEY = 'todos';

export const getTodos = (): Todo[] => {
  const todos = localStorage.getItem(STORAGE_KEY);
  if (!todos) return [];
  
  try {
    // Parse the JSON and convert date strings back to Date objects
    return JSON.parse(todos, (key, value) => {
      if (key === 'createdAt') return new Date(value);
      return value;
    });
  } catch (error) {
    console.error('Error parsing todos from localStorage:', error);
    return [];
  }
};

export const saveTodos = (todos: Todo[]): void => {
  localStorage.setItem(STORAGE_KEY, JSON.stringify(todos));
};
import React from 'react';
import { 
  Thermometer,
  Droplets,
  Wind,
  Eye,
  Compass
} from 'lucide-react';

interface WeatherDisplayProps {
  data: any;
}

const WeatherDisplay: React.FC<WeatherDisplayProps> = ({ data }) => {
  return (
    <div className="mt-8 bg-white/10 backdrop-blur-md rounded-lg border border-white/20 overflow-hidden">
      <div className="p-6 text-center">
        <h2 className="text-2xl font-bold text-white mb-1">
          {data.name}, {data.sys.country}
        </h2>
        <p className="text-lg text-sky-200 capitalize mb-4">
          {data.weather[0].description}
        </p>
        
        <div className="flex justify-center items-center mb-6">
          <img
            src={`https://openweathermap.org/img/wn/${data.weather[0].icon}@2x.png`}
            alt={data.weather[0].description}
            className="w-20 h-20"
          />
          <div className="text-6xl font-bold text-white">
            {Math.round(data.main.temp)}°C
          </div>
        </div>

        <div className="grid grid-cols-2 gap-4">
          <div className="flex items-center justify-center gap-2 text-sky-200">
            <Thermometer size={20} />
            <span>Feels like: {Math.round(data.main.feels_like)}°C</span>
          </div>
          <div className="flex items-center justify-center gap-2 text-sky-200">
            <Droplets size={20} />
            <span>Humidity: {data.main.humidity}%</span>
          </div>
          <div className="flex items-center justify-center gap-2 text-sky-200">
            <Wind size={20} />
            <span>Wind: {Math.round(data.wind.speed)} m/s</span>
          </div>
          <div className="flex items-center justify-center gap-2 text-sky-200">
            <Eye size={20} />
            <span>Visibility: {data.visibility / 1000} km</span>
          </div>
        </div>
      </div>
      
      <div className="bg-black/20 p-4 flex items-center justify-between">
        <div className="text-sky-200">
          <div className="text-sm">Min</div>
          <div className="font-bold">{Math.round(data.main.temp_min)}°C</div>
        </div>
        <Compass className="text-sky-400" />
        <div className="text-sky-200">
          <div className="text-sm">Max</div>
          <div className="font-bold">{Math.round(data.main.temp_max)}°C</div>
        </div>
      </div>
    </div>
  );
};

export default WeatherDisplay;
import React from 'react';
import { CheckCircle, Circle } from 'lucide-react';

interface TodoTabsProps {
  activeTab: 'todo' | 'completed';
  onTabChange: (tab: 'todo' | 'completed') => void;
  todoCount: number;
  completedCount: number;
}

const TodoTabs: React.FC<TodoTabsProps> = ({
  activeTab,
  onTabChange,
  todoCount,
  completedCount,
}) => {
  return (
    <div className="flex border-b border-gray-200 mb-4">
      <button
        className={`flex items-center py-3 px-4 relative transition-all duration-300 ${
          activeTab === 'todo'
            ? 'text-blue-600 font-medium'
            : 'text-gray-500 hover:text-gray-700'
        }`}
        onClick={() => onTabChange('todo')}
      >
        <Circle
          size={18}
          className={`mr-2 ${activeTab === 'todo' ? 'text-blue-600' : 'text-gray-400'}`}
        />
        <span>To Do</span>
        <span className="ml-2 text-sm rounded-full bg-gray-100 px-2 py-0.5">
          {todoCount}
        </span>
        {activeTab === 'todo' && (
          <span className="absolute bottom-0 left-0 right-0 h-0.5 bg-blue-600 animate-scaleX"></span>
        )}
      </button>
      
      <button
        className={`flex items-center py-3 px-4 relative transition-all duration-300 ${
          activeTab === 'completed'
            ? 'text-green-600 font-medium'
            : 'text-gray-500 hover:text-gray-700'
        }`}
        onClick={() => onTabChange('completed')}
      >
        <CheckCircle
          size={18}
          className={`mr-2 ${activeTab === 'completed' ? 'text-green-600' : 'text-gray-400'}`}
        />
        <span>Completed</span>
        <span className="ml-2 text-sm rounded-full bg-gray-100 px-2 py-0.5">
          {completedCount}
        </span>
        {activeTab === 'completed' && (
          <span className="absolute bottom-0 left-0 right-0 h-0.5 bg-green-600 animate-scaleX"></span>
        )}
      </button>
    </div>
  );
};

export default TodoTabs;
import React from 'react';
import { Todo } from '../types/Todo';
import TodoItem from './TodoItem';
import { ListChecks } from 'lucide-react';

interface TodoListProps {
  todos: Todo[];
  onToggleComplete: (id: string) => void;
  onDelete: (id: string) => void;
  onEdit: (id: string, updates: Partial<Todo>) => void;
  emptyMessage: string;
}

const TodoList: React.FC<TodoListProps> = ({
  todos,
  onToggleComplete,
  onDelete,
  onEdit,
  emptyMessage,
}) => {
  if (todos.length === 0) {
    return (
      <div className="flex flex-col items-center justify-center py-10 text-center">
        <ListChecks size={48} className="text-gray-300 mb-2" />
        <p className="text-gray-500">{emptyMessage}</p>
      </div>
    );
  }

  return (
    <div className="animate-fadeIn">
      {todos.map((todo) => (
        <TodoItem
          key={todo.id}
          todo={todo}
          onToggleComplete={onToggleComplete}
          onDelete={onDelete}
          onEdit={onEdit}
        />
      ))}
    </div>
  );
};

export default TodoList;
import React, { useState } from 'react';
import { Check, Trash, Edit, Save, X } from 'lucide-react';
import { Todo } from '../types/Todo';

interface TodoItemProps {
  todo: Todo;
  onToggleComplete: (id: string) => void;
  onDelete: (id: string) => void;
  onEdit: (id: string, updates: Partial<Todo>) => void;
}

const TodoItem: React.FC<TodoItemProps> = ({ todo, onToggleComplete, onDelete, onEdit }) => {
  const [isEditing, setIsEditing] = useState(false);
  const [editTitle, setEditTitle] = useState(todo.title);
  const [editDescription, setEditDescription] = useState(todo.description);
  const [editPriority, setEditPriority] = useState(todo.priority);

  const handleEditSave = () => {
    if (!editTitle.trim()) return;
    
    onEdit(todo.id, {
      title: editTitle.trim(),
      description: editDescription.trim(),
      priority: editPriority,
    });
    
    setIsEditing(false);
  };

  const handleEditCancel = () => {
    setEditTitle(todo.title);
    setEditDescription(todo.description);
    setEditPriority(todo.priority);
    setIsEditing(false);
  };

  const priorityColors = {
    low: 'bg-blue-100 text-blue-800 border-blue-200',
    medium: 'bg-amber-100 text-amber-800 border-amber-200',
    high: 'bg-red-100 text-red-800 border-red-200',
  };

  const priorityBorders = {
    low: 'border-l-blue-500',
    medium: 'border-l-amber-500',
    high: 'border-l-red-500',
  };

  return (
    <div
      className={`bg-white rounded-lg shadow-sm mb-3 border-l-4 ${
        priorityBorders[todo.priority]
      } transition-all duration-300 hover:shadow-md ${
        todo.completed ? 'opacity-70' : ''
      }`}
    >
      {isEditing ? (
        <div className="p-4 animate-fadeIn">
          <input
            type="text"
            value={editTitle}
            onChange={(e) => setEditTitle(e.target.value)}
            className="w-full p-2 mb-2 border border-gray-200 rounded-md focus:outline-none focus:ring-2 focus:ring-blue-500"
            autoFocus
          />
          
          <textarea
            value={editDescription}
            onChange={(e) => setEditDescription(e.target.value)}
            className="w-full p-2 mb-2 border border-gray-200 rounded-md focus:outline-none focus:ring-2 focus:ring-blue-500 min-h-[60px]"
            placeholder="Description (optional)"
          />
          
          <div className="flex mb-3 space-x-2">
            {(['low', 'medium', 'high'] as const).map((option) => (
              <button
                key={option}
                type="button"
                onClick={() => setEditPriority(option)}
                className={`px-3 py-1 rounded-full border text-sm font-medium transition-all duration-200 ${
                  editPriority === option
                    ? priorityColors[option]
                    : 'bg-gray-50 text-gray-700 border-gray-200'
                }`}
              >
                {option.charAt(0).toUpperCase() + option.slice(1)}
              </button>
            ))}
          </div>
          
          <div className="flex justify-end space-x-2">
            <button
              onClick={handleEditCancel}
              className="p-1 rounded-full hover:bg-gray-100"
              aria-label="Cancel editing"
            >
              <X size={18} className="text-gray-600" />
            </button>
            <button
              onClick={handleEditSave}
              className="p-1 rounded-full hover:bg-blue-100"
              aria-label="Save edits"
            >
              <Save size={18} className="text-blue-600" />
            </button>
          </div>
        </div>
      ) : (
        <div className="p-4">
          <div className="flex items-start">
            <button
              onClick={() => onToggleComplete(todo.id)}
              className={`shrink-0 w-5 h-5 mr-3 mt-1 rounded-full border ${
                todo.completed
                  ? 'bg-green-500 border-green-500 text-white'
                  : 'border-gray-300 hover:border-blue-500'
              } flex items-center justify-center transition-colors duration-300`}
              aria-label={todo.completed ? 'Mark as incomplete' : 'Mark as complete'}
            >
              {todo.completed && <Check size={12} />}
            </button>
            
            <div className="flex-1 min-w-0">
              <h3 className={`font-medium text-gray-900 break-words ${
                todo.completed ? 'line-through text-gray-500' : ''
              }`}>
                {todo.title}
              </h3>
              
              {todo.description && (
                <p className={`mt-1 text-sm text-gray-600 break-words ${
                  todo.completed ? 'line-through text-gray-400' : ''
                }`}>
                  {todo.description}
                </p>
              )}
              
              <div className="mt-2 flex items-center justify-between">
                <span className={`inline-flex items-center px-2 py-0.5 rounded-full text-xs font-medium ${priorityColors[todo.priority]}`}>
                  {todo.priority.charAt(0).toUpperCase() + todo.priority.slice(1)}
                </span>
                
                <span className="text-xs text-gray-500">
                  {todo.createdAt.toLocaleDateString()}
                </span>
              </div>
            </div>
            
            <div className="ml-2 flex space-x-1">
              <button
                onClick={() => setIsEditing(true)}
                className="p-1 rounded-full hover:bg-gray-100 text-gray-500 hover:text-blue-500 transition-colors duration-300"
                aria-label="Edit task"
              >
                <Edit size={16} />
              </button>
              <button
                onClick={() => onDelete(todo.id)}
                className="p-1 rounded-full hover:bg-gray-100 text-gray-500 hover:text-red-500 transition-colors duration-300"
                aria-label="Delete task"
              >
                <Trash size={16} />
              </button>
            </div>
          </div>
        </div>
      )}
    </div>
  );
};

export default TodoItem;
import React, { useState } from 'react';
import { Plus } from 'lucide-react';
import { Todo } from '../types/Todo';

interface TodoFormProps {
  onAddTodo: (todo: Omit<Todo, 'id' | 'completed' | 'createdAt'>) => void;
}

const TodoForm: React.FC<TodoFormProps> = ({ onAddTodo }) => {
  const [title, setTitle] = useState('');
  const [description, setDescription] = useState('');
  const [priority, setPriority] = useState<'low' | 'medium' | 'high'>('medium');
  const [isExpanded, setIsExpanded] = useState(false);

  const handleSubmit = (e: React.FormEvent) => {
    e.preventDefault();
    
    if (!title.trim()) return;
    
    onAddTodo({
      title: title.trim(),
      description: description.trim(),
      priority,
    });
    
    // Reset form
    setTitle('');
    setDescription('');
    setPriority('medium');
    setIsExpanded(false);
  };

  const priorityColors = {
    low: 'bg-blue-100 text-blue-800 border-blue-200',
    medium: 'bg-amber-100 text-amber-800 border-amber-200',
    high: 'bg-red-100 text-red-800 border-red-200',
  };

  return (
    <form onSubmit={handleSubmit} className="mb-6 bg-white rounded-lg shadow-md p-4 transition-all duration-300">
      <div className="flex items-center mb-3">
        <input
          type="text"
          placeholder="Add a new task..."
          value={title}
          onChange={(e) => setTitle(e.target.value)}
          onFocus={() => setIsExpanded(true)}
          className="flex-grow p-2 border-b-2 border-gray-200 focus:border-blue-500 focus:outline-none transition-colors duration-300"
        />
        <button
          type="submit"
          disabled={!title.trim()}
          className={`ml-2 p-2 rounded-full ${
            title.trim() ? 'bg-blue-500 text-white hover:bg-blue-600' : 'bg-gray-200 text-gray-400'
          } transition-colors duration-300`}
        >
          <Plus size={20} />
        </button>
      </div>
      
      {isExpanded && (
        <div className="space-y-3 animate-fadeIn">
          <div>
            <label htmlFor="description" className="block text-sm font-medium text-gray-700 mb-1">
              Description
            </label>
            <textarea
              id="description"
              placeholder="Add details (optional)"
              value={description}
              onChange={(e) => setDescription(e.target.value)}
              className="w-full p-2 border border-gray-200 rounded-md focus:outline-none focus:ring-2 focus:ring-blue-500 transition-all duration-300 min-h-[80px] text-sm"
            />
          </div>
          
          <div>
            <label className="block text-sm font-medium text-gray-700 mb-1">
              Priority
            </label>
            <div className="flex space-x-2">
              {(['low', 'medium', 'high'] as const).map((option) => (
                <button
                  key={option}
                  type="button"
                  onClick={() => setPriority(option)}
                  className={`px-3 py-1 rounded-full border text-sm font-medium transition-all duration-200 ${
                    priority === option
                      ? priorityColors[option]
                      : 'bg-gray-50 text-gray-700 border-gray-200'
                  }`}
                >
                  {option.charAt(0).toUpperCase() + option.slice(1)}
                </button>
              ))}
            </div>
          </div>
        </div>
      )}
    </form>
  );
};
