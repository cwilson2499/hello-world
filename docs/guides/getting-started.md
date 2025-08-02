# Getting Started

Welcome to the hello-world project! This guide will help you get up and running quickly.

## Quick Start

### Prerequisites

- Node.js 16+ or npm/yarn
- Git
- A text editor or IDE

### Installation

```bash
# Clone the repository
git clone https://github.com/your-org/hello-world.git
cd hello-world

# Install dependencies
npm install
# or
yarn install

# Start development server
npm run dev
# or
yarn dev
```

### Project Structure

```
hello-world/
├── docs/                 # Documentation
├── src/                  # Source code
│   ├── components/       # React components
│   ├── utils/           # Utility functions
│   ├── api/             # API layer
│   └── types/           # TypeScript types
├── tests/               # Test files
├── public/              # Static assets
└── package.json         # Dependencies
```

## First Steps

### 1. Explore the Components

```javascript
import { Button, Card, Input } from '@hello-world/components';

function MyFirstComponent() {
  return (
    <Card>
      <h1>Hello World!</h1>
      <Input placeholder="Enter your name" />
      <Button variant="primary">Submit</Button>
    </Card>
  );
}
```

### 2. Use Utility Functions

```javascript
import { validateEmail, formatDate, debounce } from '@hello-world/utils';

// Validate an email
const isValid = validateEmail('user@example.com');

// Format a date
const formatted = formatDate(new Date(), 'YYYY-MM-DD');

// Debounce a function
const debouncedSearch = debounce(searchFunction, 300);
```

### 3. Make API Calls

```javascript
import { apiClient } from '@hello-world/api';

// Fetch user data
const user = await apiClient.users.get('123');

// Create a new user
const newUser = await apiClient.users.create({
  name: 'John Doe',
  email: 'john@example.com'
});
```

## Development Workflow

### Running Tests

```bash
# Run all tests
npm test

# Run tests in watch mode
npm run test:watch

# Run tests with coverage
npm run test:coverage
```

### Code Quality

```bash
# Lint code
npm run lint

# Fix linting issues
npm run lint:fix

# Format code
npm run format

# Type check
npm run type-check
```

### Building for Production

```bash
# Build the project
npm run build

# Preview the build
npm run preview
```

## Next Steps

- Read the [API Documentation](../api/README.md)
- Explore [Components](../components/README.md)
- Learn about [Utility Functions](../functions/README.md)
- Check out [Examples](../examples/README.md)
- Review [Best Practices](./best-practices.md)