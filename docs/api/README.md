# API Reference

This document provides comprehensive documentation for all public APIs in the hello-world project.

## Table of Contents

- [Authentication](#authentication)
- [Base URL](#base-url)
- [Request/Response Format](#requestresponse-format)
- [Error Handling](#error-handling)
- [Rate Limiting](#rate-limiting)
- [API Endpoints](#api-endpoints)

## Authentication

All API requests require authentication using API keys or bearer tokens.

### API Key Authentication

```bash
curl -H "X-API-Key: your-api-key" \
     https://api.hello-world.com/v1/resource
```

### Bearer Token Authentication

```bash
curl -H "Authorization: Bearer your-token" \
     https://api.hello-world.com/v1/resource
```

### Example Authentication Flow

```javascript
// JavaScript example
const response = await fetch('https://api.hello-world.com/v1/auth/login', {
  method: 'POST',
  headers: {
    'Content-Type': 'application/json',
  },
  body: JSON.stringify({
    username: 'user@example.com',
    password: 'your-password'
  })
});

const { token } = await response.json();

// Use token in subsequent requests
const apiResponse = await fetch('https://api.hello-world.com/v1/users', {
  headers: {
    'Authorization': `Bearer ${token}`
  }
});
```

## Base URL

All API endpoints are relative to the base URL:

```
https://api.hello-world.com/v1
```

### Environment URLs
- **Production**: `https://api.hello-world.com/v1`
- **Staging**: `https://staging-api.hello-world.com/v1`
- **Development**: `https://dev-api.hello-world.com/v1`

## Request/Response Format

### Content Type
All requests should use `application/json` content type:

```bash
Content-Type: application/json
```

### Standard Response Format

```json
{
  "success": true,
  "data": {},
  "message": "Operation completed successfully",
  "meta": {
    "timestamp": "2024-01-01T00:00:00Z",
    "request_id": "req_123456789"
  }
}
```

### Error Response Format

```json
{
  "success": false,
  "error": {
    "code": "VALIDATION_ERROR",
    "message": "Invalid input provided",
    "details": {
      "field": "email",
      "reason": "Invalid email format"
    }
  },
  "meta": {
    "timestamp": "2024-01-01T00:00:00Z",
    "request_id": "req_123456789"
  }
}
```

## Error Handling

### HTTP Status Codes

| Status Code | Meaning |
|-------------|---------|
| 200 | OK - Request successful |
| 201 | Created - Resource created successfully |
| 400 | Bad Request - Invalid request format |
| 401 | Unauthorized - Authentication required |
| 403 | Forbidden - Access denied |
| 404 | Not Found - Resource not found |
| 429 | Too Many Requests - Rate limit exceeded |
| 500 | Internal Server Error - Server error |

### Error Codes

| Error Code | Description |
|------------|-------------|
| `VALIDATION_ERROR` | Input validation failed |
| `AUTHENTICATION_FAILED` | Invalid credentials |
| `AUTHORIZATION_DENIED` | Insufficient permissions |
| `RESOURCE_NOT_FOUND` | Requested resource not found |
| `RATE_LIMIT_EXCEEDED` | API rate limit exceeded |
| `INTERNAL_ERROR` | Internal server error |

## Rate Limiting

API requests are limited to prevent abuse:

- **Rate Limit**: 1000 requests per hour per API key
- **Burst Limit**: 100 requests per minute

Rate limit headers are included in responses:

```
X-RateLimit-Limit: 1000
X-RateLimit-Remaining: 999
X-RateLimit-Reset: 1640995200
```

## API Endpoints

### User Management

#### Get User Profile
```http
GET /v1/users/{user_id}
```

**Parameters:**
- `user_id` (path, required): Unique user identifier

**Response:**
```json
{
  "success": true,
  "data": {
    "id": "user_123",
    "username": "john_doe",
    "email": "john@example.com",
    "created_at": "2024-01-01T00:00:00Z",
    "updated_at": "2024-01-01T00:00:00Z"
  }
}
```

**Example:**
```bash
curl -H "Authorization: Bearer your-token" \
     https://api.hello-world.com/v1/users/user_123
```

```javascript
// JavaScript example
const user = await fetch('/api/v1/users/user_123', {
  headers: {
    'Authorization': 'Bearer ' + token
  }
}).then(res => res.json());

console.log(user.data.username); // "john_doe"
```

#### Create User
```http
POST /v1/users
```

**Request Body:**
```json
{
  "username": "john_doe",
  "email": "john@example.com",
  "password": "secure_password"
}
```

**Response:**
```json
{
  "success": true,
  "data": {
    "id": "user_123",
    "username": "john_doe",
    "email": "john@example.com",
    "created_at": "2024-01-01T00:00:00Z"
  }
}
```

**Example:**
```bash
curl -X POST \
     -H "Content-Type: application/json" \
     -H "Authorization: Bearer your-token" \
     -d '{"username":"john_doe","email":"john@example.com","password":"secure_password"}' \
     https://api.hello-world.com/v1/users
```

```python
# Python example
import requests

response = requests.post(
    'https://api.hello-world.com/v1/users',
    headers={
        'Authorization': 'Bearer your-token',
        'Content-Type': 'application/json'
    },
    json={
        'username': 'john_doe',
        'email': 'john@example.com',
        'password': 'secure_password'
    }
)

user = response.json()
print(user['data']['id'])  # user_123
```

#### Update User
```http
PUT /v1/users/{user_id}
```

**Parameters:**
- `user_id` (path, required): Unique user identifier

**Request Body:**
```json
{
  "username": "new_username",
  "email": "new_email@example.com"
}
```

**Example:**
```javascript
// JavaScript example with error handling
try {
  const response = await fetch('/api/v1/users/user_123', {
    method: 'PUT',
    headers: {
      'Authorization': 'Bearer ' + token,
      'Content-Type': 'application/json'
    },
    body: JSON.stringify({
      username: 'new_username',
      email: 'new_email@example.com'
    })
  });

  if (!response.ok) {
    throw new Error(`HTTP error! status: ${response.status}`);
  }

  const result = await response.json();
  console.log('User updated:', result.data);
} catch (error) {
  console.error('Failed to update user:', error);
}
```

#### Delete User
```http
DELETE /v1/users/{user_id}
```

**Parameters:**
- `user_id` (path, required): Unique user identifier

**Response:**
```json
{
  "success": true,
  "message": "User deleted successfully"
}
```

### Data Resources

#### List Resources
```http
GET /v1/resources
```

**Query Parameters:**
- `page` (optional): Page number (default: 1)
- `limit` (optional): Items per page (default: 10, max: 100)
- `sort` (optional): Sort field (default: created_at)
- `order` (optional): Sort order (asc, desc, default: desc)
- `filter` (optional): Filter criteria

**Response:**
```json
{
  "success": true,
  "data": {
    "items": [
      {
        "id": "resource_123",
        "name": "Sample Resource",
        "description": "A sample resource",
        "created_at": "2024-01-01T00:00:00Z"
      }
    ],
    "pagination": {
      "page": 1,
      "limit": 10,
      "total": 1,
      "pages": 1
    }
  }
}
```

**Example:**
```bash
# Get second page with 20 items per page
curl -H "Authorization: Bearer your-token" \
     "https://api.hello-world.com/v1/resources?page=2&limit=20&sort=name&order=asc"
```

```python
# Python example with pagination
import requests

def get_all_resources(token):
    resources = []
    page = 1
    
    while True:
        response = requests.get(
            f'https://api.hello-world.com/v1/resources?page={page}&limit=100',
            headers={'Authorization': f'Bearer {token}'}
        )
        
        data = response.json()
        resources.extend(data['data']['items'])
        
        if page >= data['data']['pagination']['pages']:
            break
            
        page += 1
    
    return resources

all_resources = get_all_resources('your-token')
print(f'Retrieved {len(all_resources)} resources')
```

## SDK Examples

### JavaScript/Node.js

```javascript
// Initialize the API client
const HelloWorldAPI = require('@hello-world/api-client');

const client = new HelloWorldAPI({
  apiKey: 'your-api-key',
  baseURL: 'https://api.hello-world.com/v1'
});

// Create a user
const newUser = await client.users.create({
  username: 'jane_doe',
  email: 'jane@example.com',
  password: 'secure_password'
});

// Get user profile
const user = await client.users.get('user_123');

// List resources with pagination
const resources = await client.resources.list({
  page: 1,
  limit: 50,
  sort: 'created_at',
  order: 'desc'
});
```

### Python

```python
from hello_world_api import Client

# Initialize client
client = Client(api_key='your-api-key')

# Create user
user = client.users.create(
    username='jane_doe',
    email='jane@example.com',
    password='secure_password'
)

# Get user
user = client.users.get('user_123')

# List resources
resources = client.resources.list(
    page=1,
    limit=50,
    sort='created_at',
    order='desc'
)
```

## WebSocket API

For real-time functionality, connect to our WebSocket endpoint:

```
wss://ws.hello-world.com/v1
```

### Connection Example

```javascript
const ws = new WebSocket('wss://ws.hello-world.com/v1');

ws.onopen = function(event) {
  // Authenticate
  ws.send(JSON.stringify({
    type: 'auth',
    token: 'your-bearer-token'
  }));
};

ws.onmessage = function(event) {
  const message = JSON.parse(event.data);
  console.log('Received:', message);
};

// Subscribe to user updates
ws.send(JSON.stringify({
  type: 'subscribe',
  channel: 'user.updates',
  user_id: 'user_123'
}));
```

## Testing

### Test Credentials

For development and testing, use these credentials:

```
API Key: test_key_123456789
Bearer Token: test_token_abcdefghijklmnop
Base URL: https://api-sandbox.hello-world.com/v1
```

### Postman Collection

Import our [Postman collection](./postman-collection.json) for easy API testing.

### OpenAPI Specification

Download our [OpenAPI 3.0 specification](./openapi.yaml) for automated testing and code generation.

## Changelog

### Version 1.2.0 (2024-01-15)
- Added WebSocket support
- Enhanced error responses
- New pagination parameters

### Version 1.1.0 (2024-01-01)
- Added resource filtering
- Improved rate limiting
- Enhanced authentication options

### Version 1.0.0 (2023-12-01)
- Initial API release
- Basic CRUD operations
- Authentication system