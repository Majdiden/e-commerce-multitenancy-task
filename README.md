# E-commerce Multi-tenancy Prototype

Preview the app here: [https://e-commerce-multitenancy-front.vercel.app/](https://e-commerce-multitenancy-front.vercel.app/)

## Architecture Overview

The application follows a multi-tenant architecture where each tenant (store) has its own dedicated MongoDB database. This ensures complete data isolation between different tenants while maintaining a centralized admin database for tenant management.

### Key Components

- **Admin Database**: Stores tenant configurations and user mappings
- **Tenant Databases**: Individual databases for each store's data
- **Connection Manager**: Handles dynamic database connections with LRU caching
- **Authentication System**: JWT-based authentication with tenant context

## Technical Implementation

### Application Layers

1. **Controllers**: Handle HTTP requests and responses
2. **Services**: Implement business logic and data processing
3. **Repositories**: Handle database operations
4. **Routes**: Define API endpoints and request routing
5. **Middleware**: Process requests and manage tenant context

### Database Structure

#### Admin Database Collections
- **Tenant**: Stores tenant configurations including database URIs
- **TenantUser**: Maps users to their respective tenants

#### Tenant Database Collections
- **User**: Store-specific user data with role-based access
- **Product**: Product catalog with category relationships and variants

### API Routes Structure

```javascript
/api
├── /auth
│   ├── POST /register   # Register new tenant
│   └── POST /login      # User login
│
├── /:tenantName/products
   ├── GET /           # List all products
   ├── POST /          # Create new product
   ├── GET /:id        # Get product details
   ├── PUT /:id        # Update product
   └── DELETE /:id     # Delete product
```

### Service Layer Implementation

#### Product Service
- Product CRUD operations with error handling
- Database connection injection via middleware
- Response standardization
- Transaction management


#### Authentication Service
- User authentication
- JWT token generation
- Tenant context management
- Session handling

### Connection Management

1. **LRU Cache**: 
   - Maintains active database connections
   - Maximum 5000 connections
   - 1-hour connection TTL

2. **Dynamic Connection Creation**:
   - Lazy loading of tenant connections
   - Automatic connection caching
   - Graceful connection handling

### Authentication Flow

1. User provides credentials and tenant domain
2. System validates credentials against tenant database
3. JWT token issued containing:
   - User ID
   - Tenant name
   - Authentication scope

### Middleware Pipeline

1. **Database Resolver**:
   - Extracts tenant context from request
   - Establishes database connection
   - Injects connection into request context

2. **Authentication**:
   - Validates JWT tokens
   - Enforces tenant isolation
   - Manages user sessions

### Product Management

- Basic product information
- Stock tracking

## Security Features

- Database isolation per tenant
- JWT-based authentication
- Role-based access control
- Secure password hashing

## Environment Variables

```
ADMIN_DB_URI=mongodb+srv://admin:admin@devdb01.cdzut.mongodb.net/admin
PORT=3000
```

## Error Handling

- Database connection errors
- Authentication failures
- Transaction rollbacks
- Graceful shutdown management
- Service-level try-catch blocks
- Standardized error responses

## Performance Optimizations

1. **Connection Pooling**:
   - LRU cache for connection management
   - Configurable connection limits
   - Automatic cleanup of stale connections


## Development Setup

1. Clone the repository
2. Install dependencies:
```bash
npm install
```
3. Set up environment variables
4. Start the development server:
```bash
npm run dev
```

## Production Deployment

1. Configure production environment variables
2. Build the application:
```bash
npm run build
```
3. Start the production server:
```bash
npm start
```
