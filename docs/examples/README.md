# Code Examples

This section provides practical examples and use cases for the hello-world project.

## Table of Contents

- [Basic Examples](#basic-examples)
- [Advanced Patterns](#advanced-patterns)
- [Real-World Applications](#real-world-applications)
- [Integration Examples](#integration-examples)
- [Performance Optimizations](#performance-optimizations)

## Basic Examples

### Simple Form

```javascript
import { Input, Button, Card, validateEmail } from '@hello-world/components';
import { useState } from 'react';

function ContactForm() {
  const [formData, setFormData] = useState({
    name: '',
    email: '',
    message: ''
  });
  const [errors, setErrors] = useState({});

  const handleSubmit = (e) => {
    e.preventDefault();
    
    const newErrors = {};
    if (!formData.name.trim()) {
      newErrors.name = 'Name is required';
    }
    if (!validateEmail(formData.email).isValid) {
      newErrors.email = 'Valid email is required';
    }
    if (!formData.message.trim()) {
      newErrors.message = 'Message is required';
    }

    setErrors(newErrors);
    
    if (Object.keys(newErrors).length === 0) {
      console.log('Form submitted:', formData);
    }
  };

  return (
    <Card variant="outlined" padding="lg">
      <h2>Contact Us</h2>
      <form onSubmit={handleSubmit}>
        <Input
          label="Name"
          value={formData.name}
          onChange={(e) => setFormData({ ...formData, name: e.target.value })}
          error={errors.name}
          required
        />
        
        <Input
          label="Email"
          type="email"
          value={formData.email}
          onChange={(e) => setFormData({ ...formData, email: e.target.value })}
          error={errors.email}
          required
        />
        
        <Input
          label="Message"
          as="textarea"
          rows={4}
          value={formData.message}
          onChange={(e) => setFormData({ ...formData, message: e.target.value })}
          error={errors.message}
          required
        />
        
        <Button type="submit" variant="primary" fullWidth>
          Send Message
        </Button>
      </form>
    </Card>
  );
}
```

### Data Table with Pagination

```javascript
import { Table, Button, Input, Pagination } from '@hello-world/components';
import { useState, useEffect } from 'react';
import { debounce } from '@hello-world/utils';

function UserTable() {
  const [users, setUsers] = useState([]);
  const [loading, setLoading] = useState(false);
  const [searchQuery, setSearchQuery] = useState('');
  const [currentPage, setCurrentPage] = useState(1);
  const [totalPages, setTotalPages] = useState(1);

  const fetchUsers = async (page = 1, search = '') => {
    setLoading(true);
    try {
      const response = await fetch(
        `/api/users?page=${page}&search=${search}&limit=10`
      );
      const data = await response.json();
      setUsers(data.users);
      setTotalPages(data.totalPages);
    } catch (error) {
      console.error('Failed to fetch users:', error);
    } finally {
      setLoading(false);
    }
  };

  const debouncedSearch = debounce((query) => {
    setCurrentPage(1);
    fetchUsers(1, query);
  }, 300);

  useEffect(() => {
    fetchUsers();
  }, []);

  useEffect(() => {
    debouncedSearch(searchQuery);
  }, [searchQuery]);

  const handlePageChange = (page) => {
    setCurrentPage(page);
    fetchUsers(page, searchQuery);
  };

  const columns = [
    { key: 'id', label: 'ID' },
    { key: 'name', label: 'Name' },
    { key: 'email', label: 'Email' },
    { key: 'role', label: 'Role' },
    {
      key: 'actions',
      label: 'Actions',
      render: (user) => (
        <div style={{ display: 'flex', gap: '0.5rem' }}>
          <Button size="sm" variant="outline">
            Edit
          </Button>
          <Button size="sm" variant="outline">
            Delete
          </Button>
        </div>
      )
    }
  ];

  return (
    <div>
      <div style={{ marginBottom: '1rem' }}>
        <Input
          placeholder="Search users..."
          value={searchQuery}
          onChange={(e) => setSearchQuery(e.target.value)}
        />
      </div>

      <Table
        columns={columns}
        data={users}
        loading={loading}
        emptyMessage="No users found"
      />

      <div style={{ marginTop: '1rem', display: 'flex', justifyContent: 'center' }}>
        <Pagination
          currentPage={currentPage}
          totalPages={totalPages}
          onPageChange={handlePageChange}
        />
      </div>
    </div>
  );
}
```

## Advanced Patterns

### Custom Hook for API Data

```javascript
import { useState, useEffect } from 'react';
import { retry } from '@hello-world/utils';

function useApiData(url, options = {}) {
  const [data, setData] = useState(null);
  const [loading, setLoading] = useState(true);
  const [error, setError] = useState(null);

  const fetchData = async () => {
    setLoading(true);
    setError(null);

    try {
      const result = await retry(
        async () => {
          const response = await fetch(url);
          if (!response.ok) {
            throw new Error(`HTTP ${response.status}`);
          }
          return response.json();
        },
        { retries: 3, delay: 1000 }
      );
      
      setData(result);
    } catch (err) {
      setError(err.message);
    } finally {
      setLoading(false);
    }
  };

  useEffect(() => {
    fetchData();
  }, [url]);

  return { data, loading, error, refetch: fetchData };
}

// Usage
function UserProfile({ userId }) {
  const { data: user, loading, error } = useApiData(`/api/users/${userId}`);

  if (loading) return <div>Loading...</div>;
  if (error) return <div>Error: {error}</div>;

  return (
    <Card>
      <h2>{user.name}</h2>
      <p>{user.email}</p>
    </Card>
  );
}
```

### Form Validation Hook

```javascript
import { useState, useCallback } from 'react';
import { validateEmail, validatePassword } from '@hello-world/utils';

function useFormValidation(initialValues, validationRules) {
  const [values, setValues] = useState(initialValues);
  const [errors, setErrors] = useState({});
  const [touched, setTouched] = useState({});

  const validate = useCallback((fieldName, value) => {
    const rule = validationRules[fieldName];
    if (!rule) return null;

    if (rule.required && !value.trim()) {
      return 'This field is required';
    }

    if (rule.email && !validateEmail(value).isValid) {
      return 'Please enter a valid email';
    }

    if (rule.password) {
      const validation = validatePassword(value, rule.password);
      if (!validation.isValid) {
        return validation.errors[0];
      }
    }

    if (rule.minLength && value.length < rule.minLength) {
      return `Must be at least ${rule.minLength} characters`;
    }

    if (rule.custom) {
      return rule.custom(value);
    }

    return null;
  }, [validationRules]);

  const setValue = (fieldName, value) => {
    setValues(prev => ({ ...prev, [fieldName]: value }));
    
    if (touched[fieldName]) {
      const error = validate(fieldName, value);
      setErrors(prev => ({ ...prev, [fieldName]: error }));
    }
  };

  const setTouched = (fieldName) => {
    setTouched(prev => ({ ...prev, [fieldName]: true }));
    const error = validate(fieldName, values[fieldName]);
    setErrors(prev => ({ ...prev, [fieldName]: error }));
  };

  const validateAll = () => {
    const newErrors = {};
    const newTouched = {};

    Object.keys(validationRules).forEach(fieldName => {
      newTouched[fieldName] = true;
      const error = validate(fieldName, values[fieldName]);
      if (error) {
        newErrors[fieldName] = error;
      }
    });

    setTouched(newTouched);
    setErrors(newErrors);

    return Object.keys(newErrors).length === 0;
  };

  return {
    values,
    errors,
    touched,
    setValue,
    setTouched,
    validateAll,
    isValid: Object.keys(errors).length === 0
  };
}

// Usage
function RegistrationForm() {
  const { values, errors, touched, setValue, setTouched, validateAll } = useFormValidation(
    { username: '', email: '', password: '' },
    {
      username: { required: true, minLength: 3 },
      email: { required: true, email: true },
      password: { 
        required: true, 
        password: { minLength: 8, requireUppercase: true } 
      }
    }
  );

  const handleSubmit = (e) => {
    e.preventDefault();
    if (validateAll()) {
      console.log('Form is valid:', values);
    }
  };

  return (
    <form onSubmit={handleSubmit}>
      <Input
        label="Username"
        value={values.username}
        onChange={(e) => setValue('username', e.target.value)}
        onBlur={() => setTouched('username')}
        error={touched.username ? errors.username : null}
      />
      
      <Input
        label="Email"
        type="email"
        value={values.email}
        onChange={(e) => setValue('email', e.target.value)}
        onBlur={() => setTouched('email')}
        error={touched.email ? errors.email : null}
      />
      
      <Input
        label="Password"
        type="password"
        value={values.password}
        onChange={(e) => setValue('password', e.target.value)}
        onBlur={() => setTouched('password')}
        error={touched.password ? errors.password : null}
      />
      
      <Button type="submit">Register</Button>
    </form>
  );
}
```

## Real-World Applications

### E-commerce Product List

```javascript
import { Card, Button, Badge, Input, Select } from '@hello-world/components';
import { useState, useEffect } from 'react';
import { formatCurrency, debounce } from '@hello-world/utils';

function ProductList() {
  const [products, setProducts] = useState([]);
  const [filters, setFilters] = useState({
    search: '',
    category: '',
    priceRange: '',
    sortBy: 'name'
  });
  const [loading, setLoading] = useState(false);

  const fetchProducts = async (searchFilters) => {
    setLoading(true);
    try {
      const params = new URLSearchParams(searchFilters);
      const response = await fetch(`/api/products?${params}`);
      const data = await response.json();
      setProducts(data.products);
    } catch (error) {
      console.error('Failed to fetch products:', error);
    } finally {
      setLoading(false);
    }
  };

  const debouncedFetch = debounce(fetchProducts, 300);

  useEffect(() => {
    debouncedFetch(filters);
  }, [filters]);

  const updateFilter = (key, value) => {
    setFilters(prev => ({ ...prev, [key]: value }));
  };

  return (
    <div>
      <div style={{ display: 'flex', gap: '1rem', marginBottom: '2rem' }}>
        <Input
          placeholder="Search products..."
          value={filters.search}
          onChange={(e) => updateFilter('search', e.target.value)}
        />
        
        <Select
          value={filters.category}
          onChange={(value) => updateFilter('category', value)}
          options={[
            { value: '', label: 'All Categories' },
            { value: 'electronics', label: 'Electronics' },
            { value: 'clothing', label: 'Clothing' },
            { value: 'books', label: 'Books' }
          ]}
        />
        
        <Select
          value={filters.sortBy}
          onChange={(value) => updateFilter('sortBy', value)}
          options={[
            { value: 'name', label: 'Name' },
            { value: 'price-low', label: 'Price: Low to High' },
            { value: 'price-high', label: 'Price: High to Low' },
            { value: 'rating', label: 'Rating' }
          ]}
        />
      </div>

      {loading ? (
        <div>Loading products...</div>
      ) : (
        <div style={{ display: 'grid', gridTemplateColumns: 'repeat(auto-fill, minmax(300px, 1fr))', gap: '1rem' }}>
          {products.map(product => (
            <ProductCard key={product.id} product={product} />
          ))}
        </div>
      )}
    </div>
  );
}

function ProductCard({ product }) {
  const [addingToCart, setAddingToCart] = useState(false);

  const handleAddToCart = async () => {
    setAddingToCart(true);
    try {
      await fetch('/api/cart/items', {
        method: 'POST',
        headers: { 'Content-Type': 'application/json' },
        body: JSON.stringify({ productId: product.id, quantity: 1 })
      });
    } catch (error) {
      console.error('Failed to add to cart:', error);
    } finally {
      setAddingToCart(false);
    }
  };

  return (
    <Card variant="outlined" hoverable>
      <img src={product.image} alt={product.name} style={{ width: '100%', height: '200px', objectFit: 'cover' }} />
      
      <div style={{ padding: '1rem' }}>
        <h3>{product.name}</h3>
        <p style={{ color: '#666', fontSize: '0.9rem' }}>{product.description}</p>
        
        <div style={{ display: 'flex', justifyContent: 'space-between', alignItems: 'center', marginTop: '1rem' }}>
          <span style={{ fontSize: '1.2rem', fontWeight: 'bold' }}>
            {formatCurrency(product.price)}
          </span>
          
          <div style={{ display: 'flex', gap: '0.5rem' }}>
            <Badge variant={product.inStock ? 'success' : 'error'}>
              {product.inStock ? 'In Stock' : 'Out of Stock'}
            </Badge>
            
            <Badge variant="secondary">
              ⭐ {product.rating}
            </Badge>
          </div>
        </div>
        
        <Button
          fullWidth
          variant="primary"
          disabled={!product.inStock || addingToCart}
          loading={addingToCart}
          onClick={handleAddToCart}
          style={{ marginTop: '1rem' }}
        >
          {addingToCart ? 'Adding...' : 'Add to Cart'}
        </Button>
      </div>
    </Card>
  );
}
```

### Dashboard with Real-time Updates

```javascript
import { Card, Chart, Table, Badge } from '@hello-world/components';
import { useState, useEffect } from 'react';
import { formatNumber, formatDate } from '@hello-world/utils';

function Dashboard() {
  const [metrics, setMetrics] = useState({
    totalUsers: 0,
    activeUsers: 0,
    revenue: 0,
    orders: 0
  });
  const [chartData, setChartData] = useState([]);
  const [recentOrders, setRecentOrders] = useState([]);

  useEffect(() => {
    // Set up WebSocket for real-time updates
    const ws = new WebSocket('wss://api.example.com/dashboard');
    
    ws.onmessage = (event) => {
      const data = JSON.parse(event.data);
      
      switch (data.type) {
        case 'metrics':
          setMetrics(data.metrics);
          break;
        case 'chart_data':
          setChartData(data.chartData);
          break;
        case 'new_order':
          setRecentOrders(prev => [data.order, ...prev.slice(0, 9)]);
          break;
      }
    };

    // Initial data fetch
    fetchDashboardData();

    return () => ws.close();
  }, []);

  const fetchDashboardData = async () => {
    try {
      const [metricsRes, chartRes, ordersRes] = await Promise.all([
        fetch('/api/dashboard/metrics'),
        fetch('/api/dashboard/chart-data'),
        fetch('/api/dashboard/recent-orders')
      ]);

      const [metricsData, chartData, ordersData] = await Promise.all([
        metricsRes.json(),
        chartRes.json(),
        ordersRes.json()
      ]);

      setMetrics(metricsData);
      setChartData(chartData);
      setRecentOrders(ordersData);
    } catch (error) {
      console.error('Failed to fetch dashboard data:', error);
    }
  };

  return (
    <div style={{ padding: '2rem' }}>
      <h1>Dashboard</h1>
      
      {/* Metrics Cards */}
      <div style={{ display: 'grid', gridTemplateColumns: 'repeat(auto-fit, minmax(250px, 1fr))', gap: '1rem', marginBottom: '2rem' }}>
        <MetricCard
          title="Total Users"
          value={formatNumber(metrics.totalUsers)}
          change="+12%"
          positive
        />
        <MetricCard
          title="Active Users"
          value={formatNumber(metrics.activeUsers)}
          change="+5%"
          positive
        />
        <MetricCard
          title="Revenue"
          value={`$${formatNumber(metrics.revenue)}`}
          change="+8%"
          positive
        />
        <MetricCard
          title="Orders"
          value={formatNumber(metrics.orders)}
          change="-2%"
          positive={false}
        />
      </div>

      <div style={{ display: 'grid', gridTemplateColumns: '2fr 1fr', gap: '2rem' }}>
        {/* Revenue Chart */}
        <Card variant="outlined" padding="lg">
          <h3>Revenue Over Time</h3>
          <Chart
            type="line"
            data={chartData}
            options={{
              responsive: true,
              scales: {
                y: {
                  beginAtZero: true,
                  ticks: {
                    callback: (value) => `$${formatNumber(value)}`
                  }
                }
              }
            }}
          />
        </Card>

        {/* Recent Orders */}
        <Card variant="outlined" padding="lg">
          <h3>Recent Orders</h3>
          <Table
            columns={[
              { key: 'id', label: 'Order ID' },
              { 
                key: 'total', 
                label: 'Total',
                render: (order) => `$${order.total}`
              },
              {
                key: 'status',
                label: 'Status',
                render: (order) => (
                  <Badge variant={getStatusVariant(order.status)}>
                    {order.status}
                  </Badge>
                )
              }
            ]}
            data={recentOrders}
          />
        </Card>
      </div>
    </div>
  );
}

function MetricCard({ title, value, change, positive }) {
  return (
    <Card variant="outlined" padding="lg">
      <h4 style={{ margin: '0 0 0.5rem 0', color: '#666' }}>{title}</h4>
      <div style={{ fontSize: '2rem', fontWeight: 'bold', marginBottom: '0.5rem' }}>
        {value}
      </div>
      <span style={{ color: positive ? 'green' : 'red', fontSize: '0.9rem' }}>
        {change} from last month
      </span>
    </Card>
  );
}

function getStatusVariant(status) {
  switch (status) {
    case 'completed': return 'success';
    case 'pending': return 'warning';
    case 'cancelled': return 'error';
    default: return 'secondary';
  }
}
```

## Integration Examples

### Third-party Service Integration

```javascript
import { useState, useEffect } from 'react';
import { Button, Card, Alert } from '@hello-world/components';
import { retry } from '@hello-world/utils';

// Stripe Payment Integration
function PaymentForm({ amount, onSuccess, onError }) {
  const [loading, setLoading] = useState(false);
  const [stripe, setStripe] = useState(null);
  const [elements, setElements] = useState(null);

  useEffect(() => {
    // Load Stripe
    const script = document.createElement('script');
    script.src = 'https://js.stripe.com/v3/';
    script.onload = () => {
      const stripeInstance = window.Stripe(process.env.REACT_APP_STRIPE_PUBLIC_KEY);
      setStripe(stripeInstance);
      setElements(stripeInstance.elements());
    };
    document.body.appendChild(script);
  }, []);

  const handlePayment = async () => {
    if (!stripe || !elements) return;

    setLoading(true);
    try {
      // Create payment intent with retry
      const paymentIntent = await retry(
        () => fetch('/api/create-payment-intent', {
          method: 'POST',
          headers: { 'Content-Type': 'application/json' },
          body: JSON.stringify({ amount })
        }).then(res => res.json()),
        { retries: 3 }
      );

      const { error } = await stripe.confirmCardPayment(paymentIntent.client_secret);
      
      if (error) {
        onError(error.message);
      } else {
        onSuccess();
      }
    } catch (error) {
      onError('Payment failed. Please try again.');
    } finally {
      setLoading(false);
    }
  };

  return (
    <Card variant="outlined" padding="lg">
      <h3>Payment Details</h3>
      <div style={{ marginBottom: '1rem' }}>
        <strong>Amount: ${amount}</strong>
      </div>
      
      {/* Stripe Elements would go here */}
      <div id="card-element" style={{ marginBottom: '1rem' }}>
        {/* Card input */}
      </div>
      
      <Button
        variant="primary"
        loading={loading}
        disabled={!stripe}
        onClick={handlePayment}
        fullWidth
      >
        Pay ${amount}
      </Button>
    </Card>
  );
}

// Google Maps Integration
function LocationPicker({ onLocationSelect }) {
  const [map, setMap] = useState(null);
  const [marker, setMarker] = useState(null);

  useEffect(() => {
    // Load Google Maps
    if (!window.google) {
      const script = document.createElement('script');
      script.src = `https://maps.googleapis.com/maps/api/js?key=${process.env.REACT_APP_GOOGLE_MAPS_KEY}&libraries=places`;
      script.onload = initMap;
      document.body.appendChild(script);
    } else {
      initMap();
    }
  }, []);

  const initMap = () => {
    const mapInstance = new window.google.maps.Map(
      document.getElementById('map'),
      {
        center: { lat: 37.7749, lng: -122.4194 }, // San Francisco
        zoom: 13
      }
    );

    const markerInstance = new window.google.maps.Marker({
      map: mapInstance,
      draggable: true
    });

    markerInstance.addListener('dragend', () => {
      const position = markerInstance.getPosition();
      onLocationSelect({
        lat: position.lat(),
        lng: position.lng()
      });
    });

    setMap(mapInstance);
    setMarker(markerInstance);
  };

  return (
    <Card variant="outlined" padding="lg">
      <h3>Select Location</h3>
      <div id="map" style={{ height: '400px', width: '100%' }}></div>
    </Card>
  );
}
```

## Performance Optimizations

### Virtual Scrolling for Large Lists

```javascript
import { useState, useEffect, useMemo } from 'react';
import { Card } from '@hello-world/components';

function VirtualizedList({ items, itemHeight = 50, containerHeight = 400 }) {
  const [scrollTop, setScrollTop] = useState(0);

  const visibleItems = useMemo(() => {
    const startIndex = Math.floor(scrollTop / itemHeight);
    const endIndex = Math.min(
      startIndex + Math.ceil(containerHeight / itemHeight),
      items.length - 1
    );

    return items.slice(startIndex, endIndex + 1).map((item, index) => ({
      ...item,
      index: startIndex + index
    }));
  }, [items, scrollTop, itemHeight, containerHeight]);

  const totalHeight = items.length * itemHeight;
  const offsetY = Math.floor(scrollTop / itemHeight) * itemHeight;

  return (
    <div
      style={{
        height: containerHeight,
        overflow: 'auto'
      }}
      onScroll={(e) => setScrollTop(e.target.scrollTop)}
    >
      <div style={{ height: totalHeight, position: 'relative' }}>
        <div style={{ transform: `translateY(${offsetY}px)` }}>
          {visibleItems.map(item => (
            <ListItem
              key={item.id}
              item={item}
              style={{ height: itemHeight }}
            />
          ))}
        </div>
      </div>
    </div>
  );
}

function ListItem({ item, style }) {
  return (
    <div style={style}>
      <Card variant="outlined" padding="sm">
        <h4>{item.name}</h4>
        <p>{item.description}</p>
      </Card>
    </div>
  );
}
```

### Optimized Image Loading

```javascript
import { useState, useRef, useEffect } from 'react';

function LazyImage({ src, alt, placeholder, ...props }) {
  const [loaded, setLoaded] = useState(false);
  const [inView, setInView] = useState(false);
  const imgRef = useRef();

  useEffect(() => {
    const observer = new IntersectionObserver(
      ([entry]) => {
        if (entry.isIntersecting) {
          setInView(true);
          observer.disconnect();
        }
      },
      { threshold: 0.1 }
    );

    if (imgRef.current) {
      observer.observe(imgRef.current);
    }

    return () => observer.disconnect();
  }, []);

  return (
    <div ref={imgRef} {...props}>
      {inView && (
        <img
          src={src}
          alt={alt}
          onLoad={() => setLoaded(true)}
          style={{
            opacity: loaded ? 1 : 0,
            transition: 'opacity 0.3s'
          }}
        />
      )}
      {!loaded && inView && placeholder && (
        <div style={{ 
          backgroundColor: '#f0f0f0',
          display: 'flex',
          alignItems: 'center',
          justifyContent: 'center'
        }}>
          {placeholder}
        </div>
      )}
    </div>
  );
}
```

This comprehensive examples documentation provides practical, real-world usage patterns that developers can copy and adapt for their own projects. Each example includes best practices for performance, error handling, and user experience.