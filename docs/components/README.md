# Component Library

This document provides comprehensive documentation for all reusable components in the hello-world project.

## Table of Contents

- [Getting Started](#getting-started)
- [Component Categories](#component-categories)
- [Design System](#design-system)
- [Component Reference](#component-reference)
- [Usage Guidelines](#usage-guidelines)
- [Accessibility](#accessibility)
- [Testing](#testing)

## Getting Started

### Installation

```bash
npm install @hello-world/components
```

### Basic Usage

```javascript
import { Button, Card, Modal } from '@hello-world/components';
import '@hello-world/components/dist/styles.css';

function App() {
  return (
    <div>
      <Button variant="primary" size="lg">
        Hello World
      </Button>
    </div>
  );
}
```

### CSS Variables

Our components use CSS custom properties for theming:

```css
:root {
  --hw-color-primary: #007bff;
  --hw-color-secondary: #6c757d;
  --hw-color-success: #28a745;
  --hw-color-danger: #dc3545;
  --hw-color-warning: #ffc107;
  --hw-color-info: #17a2b8;
  
  --hw-font-family: -apple-system, BlinkMacSystemFont, 'Segoe UI', Roboto;
  --hw-font-size-base: 1rem;
  --hw-line-height-base: 1.5;
  
  --hw-border-radius: 0.25rem;
  --hw-box-shadow: 0 0.125rem 0.25rem rgba(0, 0, 0, 0.075);
}
```

## Component Categories

### Layout Components
- [Container](#container) - Main content container
- [Grid](#grid) - Flexbox grid system
- [Spacer](#spacer) - Spacing utilities
- [Divider](#divider) - Visual separators

### Navigation Components
- [Navbar](#navbar) - Main navigation bar
- [Sidebar](#sidebar) - Side navigation
- [Breadcrumb](#breadcrumb) - Navigation breadcrumbs
- [Pagination](#pagination) - Page navigation

### Form Components
- [Input](#input) - Text input fields
- [Textarea](#textarea) - Multi-line text input
- [Select](#select) - Dropdown selection
- [Checkbox](#checkbox) - Boolean input
- [Radio](#radio) - Single choice input
- [Switch](#switch) - Toggle input
- [DatePicker](#datepicker) - Date selection
- [FileUpload](#fileupload) - File input

### Display Components
- [Button](#button) - Clickable actions
- [Card](#card) - Content containers
- [Badge](#badge) - Status indicators
- [Alert](#alert) - Notification messages
- [Avatar](#avatar) - User representation
- [Table](#table) - Data tables
- [List](#list) - Ordered/unordered lists

### Feedback Components
- [Modal](#modal) - Dialog boxes
- [Toast](#toast) - Temporary notifications
- [Tooltip](#tooltip) - Contextual help
- [Popover](#popover) - Contextual information
- [Loader](#loader) - Loading indicators
- [Progress](#progress) - Progress indicators

### Data Components
- [Chart](#chart) - Data visualization
- [Calendar](#calendar) - Date/time display
- [Timeline](#timeline) - Chronological events

## Design System

### Color Palette

```javascript
// Primary Colors
const colors = {
  primary: {
    50: '#e3f2fd',
    100: '#bbdefb',
    500: '#2196f3',  // Main
    900: '#0d47a1'
  },
  secondary: {
    50: '#f3e5f5',
    100: '#e1bee7',
    500: '#9c27b0',  // Main
    900: '#4a148c'
  },
  success: {
    50: '#e8f5e8',
    500: '#4caf50',  // Main
    900: '#1b5e20'
  },
  error: {
    50: '#ffebee',
    500: '#f44336',  // Main
    900: '#b71c1c'
  }
};
```

### Typography Scale

```javascript
const typography = {
  fontSize: {
    xs: '0.75rem',
    sm: '0.875rem',
    base: '1rem',
    lg: '1.125rem',
    xl: '1.25rem',
    '2xl': '1.5rem',
    '3xl': '1.875rem',
    '4xl': '2.25rem'
  },
  fontWeight: {
    normal: 400,
    medium: 500,
    semibold: 600,
    bold: 700
  }
};
```

### Spacing Scale

```javascript
const spacing = {
  0: '0',
  1: '0.25rem',
  2: '0.5rem',
  3: '0.75rem',
  4: '1rem',
  5: '1.25rem',
  6: '1.5rem',
  8: '2rem',
  10: '2.5rem',
  12: '3rem'
};
```

## Component Reference

### Button

A versatile button component with multiple variants and sizes.

#### Props

| Prop | Type | Default | Description |
|------|------|---------|-------------|
| `variant` | `'primary' \| 'secondary' \| 'outline' \| 'ghost' \| 'link'` | `'primary'` | Button style variant |
| `size` | `'sm' \| 'md' \| 'lg'` | `'md'` | Button size |
| `disabled` | `boolean` | `false` | Disable the button |
| `loading` | `boolean` | `false` | Show loading state |
| `fullWidth` | `boolean` | `false` | Make button full width |
| `leftIcon` | `ReactNode` | - | Icon before text |
| `rightIcon` | `ReactNode` | - | Icon after text |
| `onClick` | `(event: MouseEvent) => void` | - | Click handler |
| `type` | `'button' \| 'submit' \| 'reset'` | `'button'` | Button type |

#### Examples

```javascript
import { Button } from '@hello-world/components';
import { ChevronRightIcon, LoaderIcon } from '@hello-world/icons';

// Basic usage
<Button>Click me</Button>

// Different variants
<Button variant="primary">Primary</Button>
<Button variant="secondary">Secondary</Button>
<Button variant="outline">Outline</Button>
<Button variant="ghost">Ghost</Button>
<Button variant="link">Link</Button>

// Different sizes
<Button size="sm">Small</Button>
<Button size="md">Medium</Button>
<Button size="lg">Large</Button>

// With icons
<Button leftIcon={<ChevronRightIcon />}>
  Next
</Button>

<Button rightIcon={<ChevronRightIcon />}>
  Continue
</Button>

// Loading state
<Button loading leftIcon={<LoaderIcon />}>
  Saving...
</Button>

// Disabled state
<Button disabled>
  Disabled
</Button>

// Full width
<Button fullWidth>
  Full Width Button
</Button>

// Form submission
<Button type="submit" variant="primary">
  Submit Form
</Button>
```

#### Event Handling

```javascript
function MyComponent() {
  const handleClick = (event) => {
    console.log('Button clicked!', event);
  };

  const handleAsyncAction = async () => {
    setLoading(true);
    try {
      await someAsyncOperation();
    } finally {
      setLoading(false);
    }
  };

  return (
    <div>
      <Button onClick={handleClick}>
        Simple Click
      </Button>
      
      <Button onClick={handleAsyncAction} loading={loading}>
        Async Action
      </Button>
    </div>
  );
}
```

### Card

A flexible container component for grouping related content.

#### Props

| Prop | Type | Default | Description |
|------|------|---------|-------------|
| `variant` | `'default' \| 'outlined' \| 'elevated'` | `'default'` | Card style variant |
| `padding` | `'none' \| 'sm' \| 'md' \| 'lg'` | `'md'` | Internal padding |
| `hoverable` | `boolean` | `false` | Add hover effects |
| `clickable` | `boolean` | `false` | Make card clickable |
| `onClick` | `(event: MouseEvent) => void` | - | Click handler |

#### Examples

```javascript
import { Card, Button, Avatar } from '@hello-world/components';

// Basic card
<Card>
  <h3>Card Title</h3>
  <p>Card content goes here.</p>
</Card>

// Card variants
<Card variant="outlined">
  Outlined card
</Card>

<Card variant="elevated">
  Elevated card
</Card>

// Clickable card
<Card clickable onClick={() => navigate('/details')}>
  <h3>Clickable Card</h3>
  <p>Click anywhere on this card</p>
</Card>

// User profile card
<Card variant="elevated" padding="lg">
  <div style={{ display: 'flex', alignItems: 'center', gap: '1rem' }}>
    <Avatar src="/user-avatar.jpg" size="lg" />
    <div>
      <h3>John Doe</h3>
      <p>Software Engineer</p>
      <Button size="sm" variant="outline">
        View Profile
      </Button>
    </div>
  </div>
</Card>

// Product card
<Card variant="outlined" hoverable>
  <img src="/product.jpg" alt="Product" style={{ width: '100%' }} />
  <div style={{ padding: '1rem' }}>
    <h3>Product Name</h3>
    <p>$99.99</p>
    <Button fullWidth>Add to Cart</Button>
  </div>
</Card>
```

### Modal

A dialog component for displaying content in an overlay.

#### Props

| Prop | Type | Default | Description |
|------|------|---------|-------------|
| `isOpen` | `boolean` | `false` | Whether modal is open |
| `onClose` | `() => void` | - | Close handler |
| `size` | `'sm' \| 'md' \| 'lg' \| 'xl'` | `'md'` | Modal size |
| `title` | `string` | - | Modal title |
| `closeOnOverlayClick` | `boolean` | `true` | Close when clicking overlay |
| `closeOnEscape` | `boolean` | `true` | Close when pressing Escape |
| `showCloseButton` | `boolean` | `true` | Show close button |

#### Examples

```javascript
import { Modal, Button, Input } from '@hello-world/components';
import { useState } from 'react';

function MyComponent() {
  const [isModalOpen, setIsModalOpen] = useState(false);
  const [formData, setFormData] = useState({ name: '', email: '' });

  const handleSubmit = (e) => {
    e.preventDefault();
    console.log('Form submitted:', formData);
    setIsModalOpen(false);
  };

  return (
    <div>
      <Button onClick={() => setIsModalOpen(true)}>
        Open Modal
      </Button>

      <Modal
        isOpen={isModalOpen}
        onClose={() => setIsModalOpen(false)}
        title="User Information"
        size="md"
      >
        <form onSubmit={handleSubmit}>
          <div style={{ marginBottom: '1rem' }}>
            <Input
              label="Name"
              value={formData.name}
              onChange={(e) => setFormData({ ...formData, name: e.target.value })}
              required
            />
          </div>
          
          <div style={{ marginBottom: '1rem' }}>
            <Input
              label="Email"
              type="email"
              value={formData.email}
              onChange={(e) => setFormData({ ...formData, email: e.target.value })}
              required
            />
          </div>

          <div style={{ display: 'flex', gap: '0.5rem', justifyContent: 'flex-end' }}>
            <Button
              type="button"
              variant="secondary"
              onClick={() => setIsModalOpen(false)}
            >
              Cancel
            </Button>
            <Button type="submit" variant="primary">
              Save
            </Button>
          </div>
        </form>
      </Modal>
    </div>
  );
}

// Confirmation modal
function ConfirmationModal({ isOpen, onClose, onConfirm, title, message }) {
  return (
    <Modal
      isOpen={isOpen}
      onClose={onClose}
      title={title}
      size="sm"
      closeOnOverlayClick={false}
    >
      <p>{message}</p>
      
      <div style={{ display: 'flex', gap: '0.5rem', justifyContent: 'flex-end', marginTop: '1.5rem' }}>
        <Button variant="secondary" onClick={onClose}>
          Cancel
        </Button>
        <Button variant="primary" onClick={onConfirm}>
          Confirm
        </Button>
      </div>
    </Modal>
  );
}
```

### Input

A versatile input component with validation and different states.

#### Props

| Prop | Type | Default | Description |
|------|------|---------|-------------|
| `type` | `string` | `'text'` | Input type |
| `label` | `string` | - | Input label |
| `placeholder` | `string` | - | Placeholder text |
| `value` | `string` | - | Input value |
| `defaultValue` | `string` | - | Default value |
| `onChange` | `(event: ChangeEvent) => void` | - | Change handler |
| `onBlur` | `(event: FocusEvent) => void` | - | Blur handler |
| `onFocus` | `(event: FocusEvent) => void` | - | Focus handler |
| `error` | `string \| boolean` | - | Error state/message |
| `success` | `boolean` | `false` | Success state |
| `disabled` | `boolean` | `false` | Disabled state |
| `required` | `boolean` | `false` | Required field |
| `size` | `'sm' \| 'md' \| 'lg'` | `'md'` | Input size |
| `leftIcon` | `ReactNode` | - | Icon on the left |
| `rightIcon` | `ReactNode` | - | Icon on the right |
| `helperText` | `string` | - | Helper text |

#### Examples

```javascript
import { Input, Button } from '@hello-world/components';
import { SearchIcon, EyeIcon, EyeOffIcon } from '@hello-world/icons';
import { useState } from 'react';

// Basic input
<Input
  label="Username"
  placeholder="Enter your username"
  value={username}
  onChange={(e) => setUsername(e.target.value)}
/>

// Input with validation
<Input
  label="Email"
  type="email"
  value={email}
  onChange={(e) => setEmail(e.target.value)}
  error={emailError}
  helperText="We'll never share your email with anyone else."
  required
/>

// Search input with icon
<Input
  placeholder="Search..."
  leftIcon={<SearchIcon />}
  value={searchQuery}
  onChange={(e) => setSearchQuery(e.target.value)}
/>

// Password input with toggle
function PasswordInput({ value, onChange, ...props }) {
  const [showPassword, setShowPassword] = useState(false);

  return (
    <Input
      type={showPassword ? 'text' : 'password'}
      value={value}
      onChange={onChange}
      rightIcon={
        <Button
          variant="ghost"
          size="sm"
          onClick={() => setShowPassword(!showPassword)}
        >
          {showPassword ? <EyeOffIcon /> : <EyeIcon />}
        </Button>
      }
      {...props}
    />
  );
}

// Form validation example
function SignupForm() {
  const [formData, setFormData] = useState({
    username: '',
    email: '',
    password: '',
    confirmPassword: ''
  });
  const [errors, setErrors] = useState({});

  const validateForm = () => {
    const newErrors = {};

    if (!formData.username.trim()) {
      newErrors.username = 'Username is required';
    } else if (formData.username.length < 3) {
      newErrors.username = 'Username must be at least 3 characters';
    }

    if (!formData.email.trim()) {
      newErrors.email = 'Email is required';
    } else if (!/\S+@\S+\.\S+/.test(formData.email)) {
      newErrors.email = 'Email is invalid';
    }

    if (!formData.password) {
      newErrors.password = 'Password is required';
    } else if (formData.password.length < 8) {
      newErrors.password = 'Password must be at least 8 characters';
    }

    if (formData.password !== formData.confirmPassword) {
      newErrors.confirmPassword = 'Passwords do not match';
    }

    setErrors(newErrors);
    return Object.keys(newErrors).length === 0;
  };

  const handleSubmit = (e) => {
    e.preventDefault();
    if (validateForm()) {
      console.log('Form is valid:', formData);
    }
  };

  const handleChange = (field) => (e) => {
    setFormData({ ...formData, [field]: e.target.value });
    // Clear error when user starts typing
    if (errors[field]) {
      setErrors({ ...errors, [field]: null });
    }
  };

  return (
    <form onSubmit={handleSubmit}>
      <Input
        label="Username"
        value={formData.username}
        onChange={handleChange('username')}
        error={errors.username}
        required
      />

      <Input
        label="Email"
        type="email"
        value={formData.email}
        onChange={handleChange('email')}
        error={errors.email}
        required
      />

      <PasswordInput
        label="Password"
        value={formData.password}
        onChange={handleChange('password')}
        error={errors.password}
        required
      />

      <PasswordInput
        label="Confirm Password"
        value={formData.confirmPassword}
        onChange={handleChange('confirmPassword')}
        error={errors.confirmPassword}
        required
      />

      <Button type="submit" fullWidth>
        Sign Up
      </Button>
    </form>
  );
}
```

## Usage Guidelines

### Component Composition

Components are designed to work together seamlessly:

```javascript
import { Card, Input, Button, Alert } from '@hello-world/components';

function ContactForm() {
  const [showSuccess, setShowSuccess] = useState(false);

  return (
    <Card variant="outlined" padding="lg">
      <h2>Contact Us</h2>
      
      {showSuccess && (
        <Alert variant="success" onClose={() => setShowSuccess(false)}>
          Message sent successfully!
        </Alert>
      )}

      <form onSubmit={handleSubmit}>
        <Input label="Name" required />
        <Input label="Email" type="email" required />
        <Input label="Message" as="textarea" rows={4} required />
        
        <Button type="submit" fullWidth>
          Send Message
        </Button>
      </form>
    </Card>
  );
}
```

### Responsive Design

Components automatically adapt to different screen sizes:

```javascript
// Responsive grid
<Grid container spacing={4}>
  <Grid item xs={12} sm={6} md={4}>
    <Card>Mobile: full, Tablet: half, Desktop: third</Card>
  </Grid>
  <Grid item xs={12} sm={6} md={4}>
    <Card>Mobile: full, Tablet: half, Desktop: third</Card>
  </Grid>
  <Grid item xs={12} sm={6} md={4}>
    <Card>Mobile: full, Tablet: half, Desktop: third</Card>
  </Grid>
</Grid>

// Responsive button sizing
<Button size={{ xs: 'sm', md: 'md', lg: 'lg' }}>
  Responsive Button
</Button>
```

### Dark Mode Support

All components support dark mode through CSS variables:

```css
[data-theme="dark"] {
  --hw-color-bg-primary: #1a1a1a;
  --hw-color-bg-secondary: #2d2d2d;
  --hw-color-text-primary: #ffffff;
  --hw-color-text-secondary: #b3b3b3;
}
```

```javascript
// Dark mode toggle
function ThemeToggle() {
  const [isDark, setIsDark] = useState(false);

  useEffect(() => {
    document.documentElement.setAttribute(
      'data-theme',
      isDark ? 'dark' : 'light'
    );
  }, [isDark]);

  return (
    <Button onClick={() => setIsDark(!isDark)}>
      {isDark ? 'Light Mode' : 'Dark Mode'}
    </Button>
  );
}
```

## Accessibility

All components follow WCAG 2.1 guidelines:

### Keyboard Navigation
- All interactive elements are keyboard accessible
- Focus indicators are clearly visible
- Tab order is logical

### Screen Reader Support
- Proper ARIA labels and descriptions
- Semantic HTML elements
- Live regions for dynamic content

### Color and Contrast
- Minimum 4.5:1 contrast ratio for normal text
- Color is not the only way to convey information
- Focus indicators meet contrast requirements

### Example: Accessible Form

```javascript
import { Input, Button, Alert } from '@hello-world/components';

function AccessibleForm() {
  const [errors, setErrors] = useState({});

  return (
    <form role="form" aria-label="User registration">
      <Alert
        variant="info"
        role="alert"
        aria-live="polite"
      >
        All fields marked with * are required
      </Alert>

      <Input
        id="username"
        label="Username *"
        value={username}
        onChange={handleUsernameChange}
        error={errors.username}
        aria-required="true"
        aria-invalid={!!errors.username}
        aria-describedby={errors.username ? "username-error" : "username-help"}
      />
      
      {errors.username && (
        <div id="username-error" role="alert" aria-live="polite">
          {errors.username}
        </div>
      )}
      
      <div id="username-help">
        Username must be 3-20 characters long
      </div>

      <Button
        type="submit"
        aria-describedby="submit-help"
      >
        Create Account
      </Button>
      
      <div id="submit-help">
        By creating an account, you agree to our terms of service
      </div>
    </form>
  );
}
```

## Testing

### Unit Testing

```javascript
import { render, screen, fireEvent } from '@testing-library/react';
import { Button } from '@hello-world/components';

describe('Button', () => {
  test('renders with correct text', () => {
    render(<Button>Click me</Button>);
    expect(screen.getByRole('button', { name: /click me/i })).toBeInTheDocument();
  });

  test('calls onClick when clicked', () => {
    const handleClick = jest.fn();
    render(<Button onClick={handleClick}>Click me</Button>);
    
    fireEvent.click(screen.getByRole('button'));
    expect(handleClick).toHaveBeenCalledTimes(1);
  });

  test('is disabled when disabled prop is true', () => {
    render(<Button disabled>Disabled</Button>);
    expect(screen.getByRole('button')).toBeDisabled();
  });

  test('shows loading state', () => {
    render(<Button loading>Loading</Button>);
    expect(screen.getByRole('button')).toHaveAttribute('aria-busy', 'true');
  });
});
```

### Visual Testing

```javascript
import { render } from '@testing-library/react';
import { Button } from '@hello-world/components';

describe('Button Visual Tests', () => {
  test('primary variant snapshot', () => {
    const { container } = render(<Button variant="primary">Primary</Button>);
    expect(container.firstChild).toMatchSnapshot();
  });

  test('all size variants', () => {
    const sizes = ['sm', 'md', 'lg'];
    sizes.forEach(size => {
      const { container } = render(<Button size={size}>Button</Button>);
      expect(container.firstChild).toMatchSnapshot(`button-${size}`);
    });
  });
});
```

### Integration Testing

```javascript
import { render, screen, fireEvent, waitFor } from '@testing-library/react';
import userEvent from '@testing-library/user-event';
import { ContactForm } from './ContactForm';

describe('ContactForm Integration', () => {
  test('submits form with valid data', async () => {
    const user = userEvent.setup();
    const onSubmit = jest.fn();
    
    render(<ContactForm onSubmit={onSubmit} />);

    await user.type(screen.getByLabelText(/name/i), 'John Doe');
    await user.type(screen.getByLabelText(/email/i), 'john@example.com');
    await user.type(screen.getByLabelText(/message/i), 'Hello world!');
    
    await user.click(screen.getByRole('button', { name: /send/i }));

    await waitFor(() => {
      expect(onSubmit).toHaveBeenCalledWith({
        name: 'John Doe',
        email: 'john@example.com',
        message: 'Hello world!'
      });
    });
  });

  test('shows validation errors for empty fields', async () => {
    const user = userEvent.setup();
    
    render(<ContactForm />);
    
    await user.click(screen.getByRole('button', { name: /send/i }));

    expect(await screen.findByText(/name is required/i)).toBeInTheDocument();
    expect(await screen.findByText(/email is required/i)).toBeInTheDocument();
    expect(await screen.findByText(/message is required/i)).toBeInTheDocument();
  });
});
```

## Changelog

### Version 2.1.0 (2024-01-15)
- Added dark mode support
- New Chart and Timeline components
- Enhanced accessibility features
- Performance optimizations

### Version 2.0.0 (2024-01-01)
- Breaking: Redesigned component API
- New design system with CSS variables
- Added responsive prop support
- Improved TypeScript definitions

### Version 1.5.0 (2023-12-01)
- Added Modal and Toast components
- Enhanced form validation
- New animation system
- Accessibility improvements