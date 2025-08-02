# Function Library

This document provides comprehensive documentation for all utility functions and helpers in the hello-world project.

## Table of Contents

- [Getting Started](#getting-started)
- [Function Categories](#function-categories)
- [Core Utilities](#core-utilities)
- [String Utilities](#string-utilities)
- [Array Utilities](#array-utilities)
- [Object Utilities](#object-utilities)
- [Date/Time Utilities](#datetime-utilities)
- [Validation Functions](#validation-functions)
- [Math Utilities](#math-utilities)
- [Async Utilities](#async-utilities)
- [Performance Utilities](#performance-utilities)
- [Error Handling](#error-handling)
- [Testing](#testing)

## Getting Started

### Installation

```bash
npm install @hello-world/utils
```

### Basic Usage

```javascript
import { formatDate, debounce, validateEmail } from '@hello-world/utils';

// Format a date
const formatted = formatDate(new Date(), 'YYYY-MM-DD');

// Debounce a function
const debouncedSearch = debounce(searchFunction, 300);

// Validate an email
const isValid = validateEmail('user@example.com');
```

### TypeScript Support

All functions include complete TypeScript definitions:

```typescript
import { formatDate, DateFormat } from '@hello-world/utils';

const date: string = formatDate(new Date(), 'YYYY-MM-DD');
```

## Function Categories

### Core Utilities
- [isEqual](#isequal) - Deep equality comparison
- [clone](#clone) - Deep cloning
- [merge](#merge) - Deep merging
- [pick](#pick) - Object property selection
- [omit](#omit) - Object property exclusion

### String Utilities
- [camelCase](#camelcase) - Convert to camelCase
- [kebabCase](#kebabcase) - Convert to kebab-case
- [snakeCase](#snakecase) - Convert to snake_case
- [capitalize](#capitalize) - Capitalize first letter
- [truncate](#truncate) - Truncate string with ellipsis
- [slugify](#slugify) - Create URL-friendly slugs

### Array Utilities
- [unique](#unique) - Remove duplicates
- [flatten](#flatten) - Flatten nested arrays
- [chunk](#chunk) - Split into chunks
- [groupBy](#groupby) - Group by key
- [sortBy](#sortby) - Sort by key/function

### Object Utilities
- [get](#get) - Safe property access
- [set](#set) - Safe property setting
- [has](#has) - Property existence check
- [keys](#keys) - Get object keys
- [values](#values) - Get object values

### Date/Time Utilities
- [formatDate](#formatdate) - Format dates
- [parseDate](#parsedate) - Parse date strings
- [addDays](#adddays) - Add days to date
- [diffDays](#diffdays) - Calculate date difference
- [isValidDate](#isvaliddate) - Validate dates

### Validation Functions
- [validateEmail](#validateemail) - Email validation
- [validatePhone](#validatephone) - Phone number validation
- [validateURL](#validateurl) - URL validation
- [validatePassword](#validatepassword) - Password validation
- [validateCreditCard](#validatecreditcard) - Credit card validation

### Math Utilities
- [round](#round) - Round to decimal places
- [random](#random) - Random number generation
- [clamp](#clamp) - Clamp value to range
- [percentage](#percentage) - Calculate percentage
- [average](#average) - Calculate average

### Async Utilities
- [delay](#delay) - Promise-based delay
- [timeout](#timeout) - Promise timeout
- [retry](#retry) - Retry with backoff
- [debounce](#debounce) - Debounce function calls
- [throttle](#throttle) - Throttle function calls

## Core Utilities

### isEqual

Performs deep equality comparison between two values.

```typescript
function isEqual<T>(a: T, b: T): boolean
```

#### Examples

```javascript
import { isEqual } from '@hello-world/utils';

// Primitive values
isEqual(1, 1); // true
isEqual('hello', 'hello'); // true
isEqual(true, false); // false

// Objects
isEqual({ a: 1, b: 2 }, { a: 1, b: 2 }); // true
isEqual({ a: 1, b: 2 }, { b: 2, a: 1 }); // true
isEqual({ a: 1 }, { a: 1, b: 2 }); // false

// Arrays
isEqual([1, 2, 3], [1, 2, 3]); // true
isEqual([1, [2, 3]], [1, [2, 3]]); // true
isEqual([1, 2], [2, 1]); // false

// Nested structures
const obj1 = {
  name: 'John',
  address: {
    street: '123 Main St',
    city: 'New York'
  },
  hobbies: ['reading', 'gaming']
};

const obj2 = {
  name: 'John',
  address: {
    street: '123 Main St',
    city: 'New York'
  },
  hobbies: ['reading', 'gaming']
};

isEqual(obj1, obj2); // true

// Dates
isEqual(new Date('2024-01-01'), new Date('2024-01-01')); // true
isEqual(new Date('2024-01-01'), new Date('2024-01-02')); // false

// Use case: React component optimization
function MyComponent({ data }) {
  const [prevData, setPrevData] = useState(data);
  
  useEffect(() => {
    if (!isEqual(data, prevData)) {
      console.log('Data changed, updating...');
      setPrevData(data);
    }
  }, [data, prevData]);
}
```

### clone

Creates a deep copy of the provided value.

```typescript
function clone<T>(value: T): T
```

#### Examples

```javascript
import { clone } from '@hello-world/utils';

// Objects
const original = { a: 1, b: { c: 2 } };
const copied = clone(original);
copied.b.c = 3;
console.log(original.b.c); // 2 (unchanged)

// Arrays
const originalArray = [1, [2, 3], { a: 4 }];
const copiedArray = clone(originalArray);
copiedArray[1][0] = 5;
copiedArray[2].a = 6;
console.log(originalArray[1][0]); // 2 (unchanged)
console.log(originalArray[2].a); // 4 (unchanged)

// Complex structures
const complexData = {
  users: [
    { id: 1, name: 'John', preferences: { theme: 'dark' } },
    { id: 2, name: 'Jane', preferences: { theme: 'light' } }
  ],
  settings: {
    notifications: true,
    privacy: { public: false }
  }
};

const clonedData = clone(complexData);
clonedData.users[0].preferences.theme = 'light';
console.log(complexData.users[0].preferences.theme); // 'dark' (unchanged)

// Use case: State management
function useImmutableState(initialState) {
  const [state, setState] = useState(initialState);
  
  const updateState = (updates) => {
    setState(prevState => {
      const newState = clone(prevState);
      return { ...newState, ...updates };
    });
  };
  
  return [state, updateState];
}
```

### merge

Deeply merges multiple objects together.

```typescript
function merge<T extends Record<string, any>>(...objects: Partial<T>[]): T
```

#### Examples

```javascript
import { merge } from '@hello-world/utils';

// Basic merging
const obj1 = { a: 1, b: 2 };
const obj2 = { b: 3, c: 4 };
const result = merge(obj1, obj2);
// { a: 1, b: 3, c: 4 }

// Deep merging
const config1 = {
  api: {
    baseURL: 'https://api.example.com',
    timeout: 5000
  },
  features: {
    authentication: true
  }
};

const config2 = {
  api: {
    timeout: 10000,
    retries: 3
  },
  features: {
    caching: true
  }
};

const mergedConfig = merge(config1, config2);
/*
{
  api: {
    baseURL: 'https://api.example.com',
    timeout: 10000,
    retries: 3
  },
  features: {
    authentication: true,
    caching: true
  }
}
*/

// Multiple objects
const defaults = { theme: 'light', lang: 'en' };
const userPrefs = { theme: 'dark' };
const sessionData = { lastLogin: '2024-01-01' };

const finalConfig = merge(defaults, userPrefs, sessionData);
// { theme: 'dark', lang: 'en', lastLogin: '2024-01-01' }

// Use case: Configuration management
class ConfigManager {
  constructor() {
    this.defaultConfig = {
      database: {
        host: 'localhost',
        port: 5432,
        ssl: false
      },
      cache: {
        ttl: 300,
        maxSize: 1000
      }
    };
  }
  
  loadConfig(userConfig = {}, envConfig = {}) {
    return merge(this.defaultConfig, userConfig, envConfig);
  }
}
```

## String Utilities

### camelCase

Converts a string to camelCase.

```typescript
function camelCase(str: string): string
```

#### Examples

```javascript
import { camelCase } from '@hello-world/utils';

camelCase('hello world'); // 'helloWorld'
camelCase('Hello World'); // 'helloWorld'
camelCase('hello-world'); // 'helloWorld'
camelCase('hello_world'); // 'helloWorld'
camelCase('HELLO_WORLD'); // 'helloWorld'
camelCase('hello.world'); // 'helloWorld'
camelCase('hello   world'); // 'helloWorld'

// Use case: API data transformation
function transformApiResponse(data) {
  const transformed = {};
  
  for (const [key, value] of Object.entries(data)) {
    const camelKey = camelCase(key);
    transformed[camelKey] = typeof value === 'object' && value !== null
      ? transformApiResponse(value)
      : value;
  }
  
  return transformed;
}

// Transform snake_case API response to camelCase
const apiData = {
  user_id: 123,
  first_name: 'John',
  last_name: 'Doe',
  created_at: '2024-01-01',
  user_preferences: {
    email_notifications: true,
    theme_preference: 'dark'
  }
};

const camelCaseData = transformApiResponse(apiData);
/*
{
  userId: 123,
  firstName: 'John',
  lastName: 'Doe',
  createdAt: '2024-01-01',
  userPreferences: {
    emailNotifications: true,
    themePreference: 'dark'
  }
}
*/
```

### slugify

Converts a string to a URL-friendly slug.

```typescript
function slugify(str: string, options?: {
  lowercase?: boolean;
  separator?: string;
  maxLength?: number;
}): string
```

#### Examples

```javascript
import { slugify } from '@hello-world/utils';

// Basic usage
slugify('Hello World!'); // 'hello-world'
slugify('The Quick Brown Fox'); // 'the-quick-brown-fox'
slugify('Special Characters & Symbols!'); // 'special-characters-symbols'

// With options
slugify('Hello World', { separator: '_' }); // 'hello_world'
slugify('Hello World', { lowercase: false }); // 'Hello-World'
slugify('Very Long Title That Should Be Truncated', { maxLength: 20 }); // 'very-long-title-that'

// Real-world examples
const blogPosts = [
  'How to Build a React Application',
  'Advanced JavaScript Techniques',
  'CSS Grid vs. Flexbox: A Complete Guide'
];

const slugs = blogPosts.map(title => slugify(title));
/*
[
  'how-to-build-a-react-application',
  'advanced-javascript-techniques',
  'css-grid-vs-flexbox-a-complete-guide'
]
*/

// Use case: URL generation
function generatePostURL(post) {
  const slug = slugify(post.title, { maxLength: 50 });
  return `/blog/${post.id}-${slug}`;
}

const post = {
  id: 123,
  title: 'Understanding React Hooks: A Comprehensive Guide'
};

const url = generatePostURL(post);
// '/blog/123-understanding-react-hooks-a-comprehensive'
```

### truncate

Truncates a string to a specified length with ellipsis.

```typescript
function truncate(str: string, length: number, options?: {
  omission?: string;
  separator?: string;
}): string
```

#### Examples

```javascript
import { truncate } from '@hello-world/utils';

// Basic usage
truncate('Hello World', 8); // 'Hello...'
truncate('Short', 10); // 'Short' (no truncation needed)

// Custom omission
truncate('Hello World', 8, { omission: '…' }); // 'Hello W…'
truncate('Hello World', 8, { omission: ' [more]' }); // 'Hel [more]'

// Word boundary truncation
truncate('Hello World Example', 12, { separator: ' ' }); // 'Hello World'
truncate('Hello-World-Example', 12, { separator: '-' }); // 'Hello-World'

// Use cases
// Product descriptions
const products = [
  { name: 'Smartphone', description: 'Latest flagship smartphone with advanced camera and long battery life' },
  { name: 'Laptop', description: 'High-performance laptop for professional use' }
];

const truncatedProducts = products.map(product => ({
  ...product,
  shortDescription: truncate(product.description, 50, { separator: ' ' })
}));

// Comments preview
function CommentPreview({ comment, maxLength = 100 }) {
  const previewText = truncate(comment.text, maxLength, {
    separator: ' ',
    omission: '...'
  });
  
  const isLong = comment.text.length > maxLength;
  
  return (
    <div>
      <p>{previewText}</p>
      {isLong && <button>Read more</button>}
    </div>
  );
}

// Search result snippets
function highlightSearchTerm(text, searchTerm, contextLength = 150) {
  const index = text.toLowerCase().indexOf(searchTerm.toLowerCase());
  
  if (index === -1) {
    return truncate(text, contextLength);
  }
  
  const start = Math.max(0, index - contextLength / 2);
  const snippet = text.substring(start, start + contextLength);
  
  return truncate(snippet, contextLength, {
    omission: index > 0 ? '...' : ''
  });
}
```

## Array Utilities

### unique

Removes duplicate values from an array.

```typescript
function unique<T>(array: T[]): T[]
function unique<T, K>(array: T[], keyFn: (item: T) => K): T[]
```

#### Examples

```javascript
import { unique } from '@hello-world/utils';

// Primitive values
unique([1, 2, 2, 3, 3, 3]); // [1, 2, 3]
unique(['a', 'b', 'a', 'c']); // ['a', 'b', 'c']

// Objects with key function
const users = [
  { id: 1, name: 'John' },
  { id: 2, name: 'Jane' },
  { id: 1, name: 'John Doe' }, // duplicate id
  { id: 3, name: 'Bob' }
];

unique(users, user => user.id);
// [{ id: 1, name: 'John' }, { id: 2, name: 'Jane' }, { id: 3, name: 'Bob' }]

// Complex key functions
const products = [
  { id: 1, category: 'electronics', brand: 'Apple' },
  { id: 2, category: 'electronics', brand: 'Samsung' },
  { id: 3, category: 'clothing', brand: 'Nike' },
  { id: 4, category: 'electronics', brand: 'Apple' }
];

// Unique by category-brand combination
unique(products, p => `${p.category}-${p.brand}`);

// Use cases
// Remove duplicate tags
function TagsList({ tags }) {
  const uniqueTags = unique(tags.flat());
  
  return (
    <div>
      {uniqueTags.map(tag => (
        <span key={tag} className="tag">{tag}</span>
      ))}
    </div>
  );
}

// Deduplicate search results
function mergeSearchResults(results1, results2) {
  const allResults = [...results1, ...results2];
  return unique(allResults, result => result.id);
}

// Shopping cart items
function addToCart(cart, newItem) {
  const existingItem = cart.find(item => 
    item.productId === newItem.productId && 
    item.variant === newItem.variant
  );
  
  if (existingItem) {
    return cart.map(item =>
      item.productId === newItem.productId && item.variant === newItem.variant
        ? { ...item, quantity: item.quantity + newItem.quantity }
        : item
    );
  }
  
  return [...cart, newItem];
}
```

### groupBy

Groups array items by a key or function result.

```typescript
function groupBy<T, K extends string | number>(
  array: T[], 
  keyFn: (item: T) => K
): Record<K, T[]>
```

#### Examples

```javascript
import { groupBy } from '@hello-world/utils';

// Group by property
const students = [
  { name: 'John', grade: 'A', subject: 'Math' },
  { name: 'Jane', grade: 'B', subject: 'Math' },
  { name: 'Bob', grade: 'A', subject: 'Science' },
  { name: 'Alice', grade: 'A', subject: 'Math' }
];

// Group by grade
const byGrade = groupBy(students, student => student.grade);
/*
{
  A: [
    { name: 'John', grade: 'A', subject: 'Math' },
    { name: 'Bob', grade: 'A', subject: 'Science' },
    { name: 'Alice', grade: 'A', subject: 'Math' }
  ],
  B: [
    { name: 'Jane', grade: 'B', subject: 'Math' }
  ]
}
*/

// Group by subject
const bySubject = groupBy(students, student => student.subject);

// Complex grouping functions
const orders = [
  { id: 1, total: 50, date: '2024-01-15' },
  { id: 2, total: 150, date: '2024-01-15' },
  { id: 3, total: 75, date: '2024-01-16' },
  { id: 4, total: 200, date: '2024-01-16' }
];

// Group by date
const ordersByDate = groupBy(orders, order => order.date);

// Group by price range
const ordersByPriceRange = groupBy(orders, order => {
  if (order.total < 100) return 'low';
  if (order.total < 200) return 'medium';
  return 'high';
});

// Use cases
// Sales dashboard
function SalesDashboard({ sales }) {
  const salesByMonth = groupBy(sales, sale => 
    new Date(sale.date).toISOString().slice(0, 7) // YYYY-MM
  );
  
  return (
    <div>
      {Object.entries(salesByMonth).map(([month, monthlySales]) => (
        <div key={month}>
          <h3>{month}</h3>
          <p>Total Sales: {monthlySales.length}</p>
          <p>Revenue: ${monthlySales.reduce((sum, sale) => sum + sale.amount, 0)}</p>
        </div>
      ))}
    </div>
  );
}

// User permissions management
function organizePermissions(users) {
  const usersByRole = groupBy(users, user => user.role);
  
  return Object.entries(usersByRole).map(([role, roleUsers]) => ({
    role,
    count: roleUsers.length,
    permissions: [...new Set(roleUsers.flatMap(user => user.permissions))]
  }));
}

// Inventory management
function categorizeInventory(items) {
  const byCategory = groupBy(items, item => item.category);
  
  return Object.entries(byCategory).map(([category, categoryItems]) => ({
    category,
    totalItems: categoryItems.length,
    totalValue: categoryItems.reduce((sum, item) => sum + item.value, 0),
    lowStockItems: categoryItems.filter(item => item.stock < item.minStock)
  }));
}
```

## Validation Functions

### validateEmail

Validates email addresses with comprehensive checks.

```typescript
function validateEmail(email: string, options?: {
  allowTLD?: boolean;
  allowInternational?: boolean;
  maxLength?: number;
}): {
  isValid: boolean;
  errors: string[];
}
```

#### Examples

```javascript
import { validateEmail } from '@hello-world/utils';

// Basic validation
validateEmail('user@example.com'); // { isValid: true, errors: [] }
validateEmail('invalid-email'); // { isValid: false, errors: ['Invalid email format'] }

// With options
validateEmail('user@co', { allowTLD: false }); 
// { isValid: false, errors: ['Invalid TLD'] }

validateEmail('very-long-email-address@example.com', { maxLength: 20 });
// { isValid: false, errors: ['Email too long'] }

// Real-world usage
function EmailInput({ value, onChange, onValidation }) {
  const [errors, setErrors] = useState([]);
  
  const handleChange = (e) => {
    const email = e.target.value;
    onChange(email);
    
    const validation = validateEmail(email);
    setErrors(validation.errors);
    onValidation(validation.isValid);
  };
  
  return (
    <div>
      <input
        type="email"
        value={value}
        onChange={handleChange}
        className={errors.length > 0 ? 'error' : ''}
      />
      {errors.map(error => (
        <div key={error} className="error-message">{error}</div>
      ))}
    </div>
  );
}

// Bulk email validation
function validateEmailList(emails) {
  return emails.map(email => ({
    email,
    ...validateEmail(email)
  }));
}

// Form validation
function SignupForm() {
  const [formData, setFormData] = useState({ email: '', password: '' });
  const [errors, setErrors] = useState({});
  
  const validateForm = () => {
    const newErrors = {};
    
    const emailValidation = validateEmail(formData.email);
    if (!emailValidation.isValid) {
      newErrors.email = emailValidation.errors[0];
    }
    
    setErrors(newErrors);
    return Object.keys(newErrors).length === 0;
  };
  
  const handleSubmit = (e) => {
    e.preventDefault();
    if (validateForm()) {
      // Submit form
    }
  };
  
  return (
    <form onSubmit={handleSubmit}>
      <input
        type="email"
        value={formData.email}
        onChange={(e) => setFormData({ ...formData, email: e.target.value })}
      />
      {errors.email && <span className="error">{errors.email}</span>}
      <button type="submit">Sign Up</button>
    </form>
  );
}
```

### validatePassword

Validates passwords against security criteria.

```typescript
function validatePassword(password: string, options?: {
  minLength?: number;
  requireUppercase?: boolean;
  requireLowercase?: boolean;
  requireNumbers?: boolean;
  requireSpecialChars?: boolean;
  maxLength?: number;
}): {
  isValid: boolean;
  errors: string[];
  strength: 'weak' | 'medium' | 'strong';
}
```

#### Examples

```javascript
import { validatePassword } from '@hello-world/utils';

// Basic validation
validatePassword('password123');
// { isValid: false, errors: ['Must contain uppercase letter'], strength: 'weak' }

validatePassword('Password123!');
// { isValid: true, errors: [], strength: 'strong' }

// Custom requirements
const strictOptions = {
  minLength: 12,
  requireUppercase: true,
  requireLowercase: true,
  requireNumbers: true,
  requireSpecialChars: true
};

validatePassword('Pass123!', strictOptions);
// { isValid: false, errors: ['Password too short'], strength: 'weak' }

// Password strength indicator
function PasswordStrengthIndicator({ password }) {
  const validation = validatePassword(password);
  
  const getStrengthColor = (strength) => {
    switch (strength) {
      case 'weak': return 'red';
      case 'medium': return 'orange';
      case 'strong': return 'green';
      default: return 'gray';
    }
  };
  
  return (
    <div>
      <div 
        className="strength-bar"
        style={{ backgroundColor: getStrengthColor(validation.strength) }}
      >
        {validation.strength}
      </div>
      {validation.errors.map(error => (
        <div key={error} className="error">{error}</div>
      ))}
    </div>
  );
}

// Registration form with real-time validation
function PasswordInput({ onChange }) {
  const [password, setPassword] = useState('');
  const [showPassword, setShowPassword] = useState(false);
  const [validation, setValidation] = useState({ isValid: false, errors: [] });
  
  const handlePasswordChange = (e) => {
    const newPassword = e.target.value;
    setPassword(newPassword);
    setValidation(validatePassword(newPassword));
    onChange(newPassword, validation.isValid);
  };
  
  return (
    <div>
      <div className="password-input-container">
        <input
          type={showPassword ? 'text' : 'password'}
          value={password}
          onChange={handlePasswordChange}
          placeholder="Enter password"
        />
        <button
          type="button"
          onClick={() => setShowPassword(!showPassword)}
        >
          {showPassword ? 'Hide' : 'Show'}
        </button>
      </div>
      
      <PasswordStrengthIndicator password={password} />
      
      <div className="password-requirements">
        <h4>Password must contain:</h4>
        <ul>
          <li className={password.length >= 8 ? 'valid' : 'invalid'}>
            At least 8 characters
          </li>
          <li className={/[A-Z]/.test(password) ? 'valid' : 'invalid'}>
            One uppercase letter
          </li>
          <li className={/[a-z]/.test(password) ? 'valid' : 'invalid'}>
            One lowercase letter
          </li>
          <li className={/\d/.test(password) ? 'valid' : 'invalid'}>
            One number
          </li>
          <li className={/[!@#$%^&*]/.test(password) ? 'valid' : 'invalid'}>
            One special character
          </li>
        </ul>
      </div>
    </div>
  );
}
```

## Async Utilities

### debounce

Delays function execution until after a specified delay has passed since the last call.

```typescript
function debounce<T extends (...args: any[]) => any>(
  func: T,
  delay: number,
  options?: {
    leading?: boolean;
    trailing?: boolean;
    maxWait?: number;
  }
): T & { cancel: () => void; flush: () => void }
```

#### Examples

```javascript
import { debounce } from '@hello-world/utils';

// Basic search debouncing
const searchAPI = async (query) => {
  const response = await fetch(`/api/search?q=${query}`);
  return response.json();
};

const debouncedSearch = debounce(searchAPI, 300);

// Search component
function SearchBox() {
  const [query, setQuery] = useState('');
  const [results, setResults] = useState([]);
  const [loading, setLoading] = useState(false);
  
  const performSearch = debounce(async (searchQuery) => {
    if (!searchQuery.trim()) {
      setResults([]);
      return;
    }
    
    setLoading(true);
    try {
      const data = await searchAPI(searchQuery);
      setResults(data.results);
    } catch (error) {
      console.error('Search failed:', error);
    } finally {
      setLoading(false);
    }
  }, 300);
  
  const handleInputChange = (e) => {
    const value = e.target.value;
    setQuery(value);
    performSearch(value);
  };
  
  return (
    <div>
      <input
        type="text"
        value={query}
        onChange={handleInputChange}
        placeholder="Search..."
      />
      {loading && <div>Searching...</div>}
      <ul>
        {results.map(result => (
          <li key={result.id}>{result.title}</li>
        ))}
      </ul>
    </div>
  );
}

// Form auto-save
function AutoSaveForm() {
  const [formData, setFormData] = useState({ title: '', content: '' });
  const [saveStatus, setSaveStatus] = useState('saved');
  
  const saveForm = debounce(async (data) => {
    setSaveStatus('saving');
    try {
      await fetch('/api/forms/autosave', {
        method: 'POST',
        headers: { 'Content-Type': 'application/json' },
        body: JSON.stringify(data)
      });
      setSaveStatus('saved');
    } catch (error) {
      setSaveStatus('error');
    }
  }, 1000);
  
  const handleChange = (field) => (e) => {
    const newData = { ...formData, [field]: e.target.value };
    setFormData(newData);
    setSaveStatus('unsaved');
    saveForm(newData);
  };
  
  return (
    <form>
      <div>Status: {saveStatus}</div>
      <input
        value={formData.title}
        onChange={handleChange('title')}
        placeholder="Title"
      />
      <textarea
        value={formData.content}
        onChange={handleChange('content')}
        placeholder="Content"
      />
    </form>
  );
}

// Window resize handler
function useWindowSize() {
  const [windowSize, setWindowSize] = useState({
    width: window.innerWidth,
    height: window.innerHeight
  });
  
  useEffect(() => {
    const handleResize = debounce(() => {
      setWindowSize({
        width: window.innerWidth,
        height: window.innerHeight
      });
    }, 100);
    
    window.addEventListener('resize', handleResize);
    return () => {
      window.removeEventListener('resize', handleResize);
      handleResize.cancel(); // Cancel pending calls
    };
  }, []);
  
  return windowSize;
}

// API rate limiting
class APIClient {
  constructor() {
    this.debouncedRequests = new Map();
  }
  
  async request(endpoint, options = {}) {
    // Create a debounced version for each unique endpoint
    if (!this.debouncedRequests.has(endpoint)) {
      this.debouncedRequests.set(
        endpoint,
        debounce(this.makeRequest.bind(this), 100)
      );
    }
    
    return this.debouncedRequests.get(endpoint)(endpoint, options);
  }
  
  async makeRequest(endpoint, options) {
    const response = await fetch(endpoint, options);
    return response.json();
  }
}
```

### retry

Retries a function with exponential backoff on failure.

```typescript
function retry<T>(
  fn: () => Promise<T>,
  options?: {
    retries?: number;
    delay?: number;
    backoff?: number;
    maxDelay?: number;
    onRetry?: (error: Error, attempt: number) => void;
  }
): Promise<T>
```

#### Examples

```javascript
import { retry } from '@hello-world/utils';

// Basic API retry
const fetchUserData = async (userId) => {
  const response = await fetch(`/api/users/${userId}`);
  if (!response.ok) {
    throw new Error(`HTTP ${response.status}`);
  }
  return response.json();
};

// Retry with default settings (3 retries, exponential backoff)
const userData = await retry(() => fetchUserData(123));

// Custom retry configuration
const importantData = await retry(
  () => fetchUserData(123),
  {
    retries: 5,
    delay: 1000,
    backoff: 2,
    maxDelay: 10000,
    onRetry: (error, attempt) => {
      console.log(`Retry attempt ${attempt}:`, error.message);
    }
  }
);

// Database connection with retry
class DatabaseConnection {
  async connect() {
    return retry(
      async () => {
        const connection = await this.establishConnection();
        await this.testConnection(connection);
        return connection;
      },
      {
        retries: 5,
        delay: 500,
        onRetry: (error, attempt) => {
          console.warn(`Database connection attempt ${attempt} failed:`, error.message);
        }
      }
    );
  }
  
  async establishConnection() {
    // Simulated database connection
    if (Math.random() < 0.7) {
      throw new Error('Connection failed');
    }
    return { connected: true };
  }
  
  async testConnection(connection) {
    // Test the connection
    if (Math.random() < 0.3) {
      throw new Error('Connection test failed');
    }
  }
}

// File upload with retry
async function uploadFile(file, onProgress) {
  return retry(
    async () => {
      const formData = new FormData();
      formData.append('file', file);
      
      const response = await fetch('/api/upload', {
        method: 'POST',
        body: formData
      });
      
      if (!response.ok) {
        throw new Error(`Upload failed: ${response.statusText}`);
      }
      
      return response.json();
    },
    {
      retries: 3,
      delay: 1000,
      onRetry: (error, attempt) => {
        onProgress(`Upload failed, retrying... (${attempt}/3)`);
      }
    }
  );
}

// WebSocket connection with retry
class ReliableWebSocket {
  constructor(url, options = {}) {
    this.url = url;
    this.options = options;
    this.ws = null;
    this.reconnectAttempts = 0;
  }
  
  async connect() {
    return retry(
      () => this.establishConnection(),
      {
        retries: this.options.maxReconnectAttempts || 10,
        delay: 1000,
        backoff: 1.5,
        maxDelay: 30000,
        onRetry: (error, attempt) => {
          console.log(`WebSocket reconnection attempt ${attempt}`);
          this.reconnectAttempts = attempt;
          this.options.onReconnecting?.(attempt);
        }
      }
    );
  }
  
  establishConnection() {
    return new Promise((resolve, reject) => {
      const ws = new WebSocket(this.url);
      
      const timeout = setTimeout(() => {
        ws.close();
        reject(new Error('Connection timeout'));
      }, 5000);
      
      ws.onopen = () => {
        clearTimeout(timeout);
        this.ws = ws;
        this.reconnectAttempts = 0;
        this.setupEventHandlers();
        resolve(ws);
      };
      
      ws.onerror = (error) => {
        clearTimeout(timeout);
        reject(new Error('WebSocket connection failed'));
      };
    });
  }
  
  setupEventHandlers() {
    this.ws.onclose = () => {
      setTimeout(() => this.connect(), 1000);
    };
    
    this.ws.onmessage = (event) => {
      this.options.onMessage?.(JSON.parse(event.data));
    };
  }
}

// Email sending with retry
async function sendEmail(emailData) {
  return retry(
    async () => {
      const response = await fetch('/api/send-email', {
        method: 'POST',
        headers: { 'Content-Type': 'application/json' },
        body: JSON.stringify(emailData)
      });
      
      if (response.status === 429) {
        // Rate limited, should retry
        throw new Error('Rate limited');
      }
      
      if (response.status >= 500) {
        // Server error, should retry
        throw new Error(`Server error: ${response.status}`);
      }
      
      if (!response.ok) {
        // Client error, should not retry
        const error = new Error(`Email send failed: ${response.status}`);
        error.shouldRetry = false;
        throw error;
      }
      
      return response.json();
    },
    {
      retries: 3,
      delay: 2000,
      onRetry: (error, attempt) => {
        if (error.shouldRetry === false) {
          throw error; // Don't retry client errors
        }
        console.log(`Retrying email send (attempt ${attempt})`);
      }
    }
  );
}
```

## Testing

### Unit Tests

```javascript
import { 
  isEqual, 
  camelCase, 
  unique, 
  validateEmail, 
  debounce 
} from '@hello-world/utils';

describe('isEqual', () => {
  test('compares primitive values correctly', () => {
    expect(isEqual(1, 1)).toBe(true);
    expect(isEqual('hello', 'hello')).toBe(true);
    expect(isEqual(true, false)).toBe(false);
  });
  
  test('compares objects deeply', () => {
    const obj1 = { a: 1, b: { c: 2 } };
    const obj2 = { a: 1, b: { c: 2 } };
    const obj3 = { a: 1, b: { c: 3 } };
    
    expect(isEqual(obj1, obj2)).toBe(true);
    expect(isEqual(obj1, obj3)).toBe(false);
  });
  
  test('compares arrays correctly', () => {
    expect(isEqual([1, 2, 3], [1, 2, 3])).toBe(true);
    expect(isEqual([1, [2, 3]], [1, [2, 3]])).toBe(true);
    expect(isEqual([1, 2], [2, 1])).toBe(false);
  });
});

describe('camelCase', () => {
  test('converts various formats to camelCase', () => {
    expect(camelCase('hello world')).toBe('helloWorld');
    expect(camelCase('Hello World')).toBe('helloWorld');
    expect(camelCase('hello-world')).toBe('helloWorld');
    expect(camelCase('hello_world')).toBe('helloWorld');
    expect(camelCase('HELLO_WORLD')).toBe('helloWorld');
    expect(camelCase('hello.world')).toBe('helloWorld');
    expect(camelCase('hello   world')).toBe('helloWorld');
  });
  
  test('handles edge cases', () => {
    expect(camelCase('')).toBe('');
    expect(camelCase('a')).toBe('a');
    expect(camelCase('A')).toBe('a');
  });
});

describe('unique', () => {
  test('removes duplicates from primitive arrays', () => {
    expect(unique([1, 2, 2, 3])).toEqual([1, 2, 3]);
    expect(unique(['a', 'b', 'a'])).toEqual(['a', 'b']);
  });
  
  test('removes duplicates with key function', () => {
    const items = [
      { id: 1, name: 'John' },
      { id: 2, name: 'Jane' },
      { id: 1, name: 'John Doe' }
    ];
    
    const result = unique(items, item => item.id);
    expect(result).toHaveLength(2);
    expect(result[0].id).toBe(1);
    expect(result[1].id).toBe(2);
  });
});

describe('validateEmail', () => {
  test('validates correct email addresses', () => {
    expect(validateEmail('user@example.com').isValid).toBe(true);
    expect(validateEmail('test.email+tag@example.co.uk').isValid).toBe(true);
  });
  
  test('rejects invalid email addresses', () => {
    expect(validateEmail('invalid-email').isValid).toBe(false);
    expect(validateEmail('user@').isValid).toBe(false);
    expect(validateEmail('@example.com').isValid).toBe(false);
  });
  
  test('returns appropriate error messages', () => {
    const result = validateEmail('invalid-email');
    expect(result.errors).toContain('Invalid email format');
  });
});

describe('debounce', () => {
  beforeEach(() => {
    jest.useFakeTimers();
  });
  
  afterEach(() => {
    jest.useRealTimers();
  });
  
  test('delays function execution', () => {
    const mockFn = jest.fn();
    const debouncedFn = debounce(mockFn, 100);
    
    debouncedFn();
    expect(mockFn).not.toHaveBeenCalled();
    
    jest.advanceTimersByTime(100);
    expect(mockFn).toHaveBeenCalledTimes(1);
  });
  
  test('cancels previous calls', () => {
    const mockFn = jest.fn();
    const debouncedFn = debounce(mockFn, 100);
    
    debouncedFn();
    debouncedFn();
    debouncedFn();
    
    jest.advanceTimersByTime(100);
    expect(mockFn).toHaveBeenCalledTimes(1);
  });
  
  test('can be cancelled manually', () => {
    const mockFn = jest.fn();
    const debouncedFn = debounce(mockFn, 100);
    
    debouncedFn();
    debouncedFn.cancel();
    
    jest.advanceTimersByTime(100);
    expect(mockFn).not.toHaveBeenCalled();
  });
});
```

### Integration Tests

```javascript
import { validatePassword, retry, slugify } from '@hello-world/utils';

describe('Password Validation Integration', () => {
  test('validates complex password requirements', () => {
    const options = {
      minLength: 12,
      requireUppercase: true,
      requireLowercase: true,
      requireNumbers: true,
      requireSpecialChars: true
    };
    
    const result = validatePassword('SuperSecure123!', options);
    expect(result.isValid).toBe(true);
    expect(result.strength).toBe('strong');
    expect(result.errors).toHaveLength(0);
  });
  
  test('provides detailed feedback for weak passwords', () => {
    const result = validatePassword('password');
    expect(result.isValid).toBe(false);
    expect(result.strength).toBe('weak');
    expect(result.errors.length).toBeGreaterThan(0);
  });
});

describe('Retry with Real Scenarios', () => {
  test('retries failed async operations', async () => {
    let attemptCount = 0;
    const flakeyFunction = async () => {
      attemptCount++;
      if (attemptCount < 3) {
        throw new Error('Temporary failure');
      }
      return 'success';
    };
    
    const result = await retry(flakeyFunction, { retries: 3, delay: 10 });
    expect(result).toBe('success');
    expect(attemptCount).toBe(3);
  });
  
  test('gives up after max retries', async () => {
    const alwaysFailFunction = async () => {
      throw new Error('Permanent failure');
    };
    
    await expect(
      retry(alwaysFailFunction, { retries: 2, delay: 10 })
    ).rejects.toThrow('Permanent failure');
  });
});

describe('URL Slug Generation', () => {
  test('creates SEO-friendly URLs', () => {
    const testCases = [
      {
        input: 'How to Build a React App',
        expected: 'how-to-build-a-react-app'
      },
      {
        input: 'Advanced JavaScript: ES6+ Features',
        expected: 'advanced-javascript-es6-features'
      },
      {
        input: 'CSS Grid vs. Flexbox (Complete Guide)',
        expected: 'css-grid-vs-flexbox-complete-guide'
      }
    ];
    
    testCases.forEach(({ input, expected }) => {
      expect(slugify(input)).toBe(expected);
    });
  });
});
```

## Performance Considerations

### Memory Usage

```javascript
// Efficient array operations
import { unique, groupBy } from '@hello-world/utils';

// Instead of multiple loops, use efficient single-pass operations
function processLargeDataset(data) {
  // Efficient: single pass with Map
  const uniqueItems = unique(data, item => item.id);
  
  // Efficient: single pass grouping
  const grouped = groupBy(uniqueItems, item => item.category);
  
  return grouped;
}

// Memory-efficient debouncing for large datasets
function createOptimizedSearch() {
  const cache = new Map();
  const maxCacheSize = 100;
  
  const performSearch = debounce(async (query) => {
    if (cache.has(query)) {
      return cache.get(query);
    }
    
    const results = await searchAPI(query);
    
    // Prevent memory leaks with cache size limit
    if (cache.size >= maxCacheSize) {
      const firstKey = cache.keys().next().value;
      cache.delete(firstKey);
    }
    
    cache.set(query, results);
    return results;
  }, 300);
  
  return performSearch;
}
```

### Bundle Size Optimization

```javascript
// Tree-shakable imports
import { validateEmail } from '@hello-world/utils/validation';
import { debounce } from '@hello-world/utils/async';
import { camelCase } from '@hello-world/utils/string';

// Instead of importing everything
// import * as utils from '@hello-world/utils'; // ❌ Large bundle

// Use specific imports
import { 
  validateEmail, 
  debounce, 
  camelCase 
} from '@hello-world/utils'; // ✅ Smaller bundle
```

## Changelog

### Version 3.1.0 (2024-01-15)
- Added `retry` function with exponential backoff
- Enhanced `validatePassword` with strength scoring
- Performance improvements for large arrays
- Better TypeScript definitions

### Version 3.0.0 (2024-01-01)
- Breaking: Redesigned validation function APIs
- Added comprehensive async utilities
- New string manipulation functions
- Improved error handling

### Version 2.5.0 (2023-12-01)
- Added `groupBy` and advanced array utilities
- Enhanced debounce with options
- New date formatting functions
- Performance optimizations