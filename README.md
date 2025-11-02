# Technical Assessment: Full Stack Application Development

## Objective
Build a production-ready CRUD application for managing "items" using enterprise-level architecture patterns. This assessment evaluates your proficiency in backend development, API design, frontend integration, state management, and adherence to professional coding standards.

## Technology Stack (Required)

### Backend
- **Framework**: Express.js
- **Language**: TypeScript
- **Database**: MySQL
- **ORM**: Prisma
- **Validation**: Zod
- **Authentication**: JWT (JSON Web Tokens)
- **Testing**: Jest (optional but recommended)

### Frontend
- **Framework**: React 18+
- **Language**: TypeScript
- **State Management**: Redux Toolkit
- **UI Library**: Ant Design
- **Form Handling**: React Hook Form (recommended)
- **HTTP Client**: Axios
- **Build Tool**: Vite

---

## Backend Requirements

### 1. Architecture Pattern
Implement a **layered architecture** with clear separation of concerns:

```
Controller → Service → Repository → Database
```

- **Controllers**: Handle HTTP requests/responses, validate input, call services
- **Services**: Contain business logic, orchestrate operations
- **Repositories**: Handle database queries (Prisma operations only)
- **Middleware**: Authentication, error handling, logging

**Important:** Use **functional programming** approach. Do NOT use Object-Oriented Programming (classes). All code should use functions and exported function expressions.

### 2. Authentication & Authorization

Implement JWT-based authentication system:

**User Schema:**
```prisma
model User {
  id        String   @id @default(uuid())
  username  String   @unique @db.VarChar(50)
  password  String   @db.VarChar(255)  // Hashed password
  email     String?  @unique @db.VarChar(100)
  status    Boolean  @default(true)
  createdAt DateTime @default(now())
  updatedAt DateTime @updatedAt
}
```

**Authentication Endpoints:**
```
POST /api/v1.0/auth/register     - User registration
POST /api/v1.0/auth/login        - User login (returns JWT token)
POST /api/v1.0/auth/refresh      - Refresh access token (optional)
GET  /api/v1.0/auth/me           - Get current user info (protected)
```

**Implementation Requirements:**
- **Password Hashing**: Use bcryptjs or bcrypt to hash passwords
- **JWT Token Generation**: Sign tokens with a secret key from environment variables
- **Token Validation**: Create middleware to verify JWT tokens
- **Protected Routes**: Apply auth middleware to item endpoints
- **Token Expiration**: Set reasonable expiration (e.g., 24 hours for access tokens)

### 3. Database Schema

Use Prisma ORM with the following schema:

```prisma
model Item {
  id          String   @id @default(uuid())
  name        String   @db.VarChar(100)
  description String?  @db.VarChar(500)
  price       Decimal  @db.Decimal(10, 2)
  status      Boolean  @default(true)
  createdAt   DateTime @default(now())
  updatedAt   DateTime @updatedAt
  createdBy   String   // User ID from JWT token
  updatedBy   String   // User ID from JWT token
}
```

**Additional Fields Explained:**
- `status`: For soft delete implementation (true = active, false = deleted)
- `createdBy` / `updatedBy`: Audit trail (user ID from authenticated user)

### 3. API Endpoints

Implement versioned RESTful endpoints:

```
POST   /api/v1.0/items              - Create a new item
GET    /api/v1.0/items              - Get all items (with pagination & search)
GET    /api/v1.0/items/:id          - Get a single item by ID
PUT    /api/v1.0/items/:id          - Update an existing item
DELETE /api/v1.0/items/:id          - Soft delete an item (set status=false)
```

### 4. Query Features

**Pagination:**
```
GET /api/v1.0/items?page=1&pageSize=10
```

**Search:**
```
GET /api/v1.0/items?name=laptop&status=true
```

**Sorting:**
```
GET /api/v1.0/items?sortField=createdAt&sortOrder=desc
```

**Response Format:**
```json
{
  "data": ["array of items"],
  "pagination": {
    "total": 100,
    "page": 1,
    "pageSize": 10
  }
}
```

### 5. Validation Requirements

Use **Zod** for all input validation:

- Create separate schemas:
  - `createItemSchema` - For POST requests
  - `updateItemSchema` - For PUT requests (all fields optional)
  - `urlParamsSchema` - For query parameters validation
  - `loginSchema` - For login validation
  - `registerSchema` - For registration validation

- Return user-friendly error messages

### 6. Error Handling

Implement centralized error handling:

- **Error Handler Middleware**: Catch all errors and format responses
- **Zod Error Formatting**: Transform validation errors to user-friendly messages
- **Proper Status Codes**: 400 (validation), 404 (not found), 500 (server error)

**Error Response Format:**
```json
{
  "statusCode": 400,
  "message": "Name is required, Price must be positive"
}
```

### 7. Middleware Requirements

- **CORS**: Configure cross-origin resource sharing
- **Helmet**: Security headers (X-XSS-Protection, CSP, etc.)
- **Error Handler**: Centralized error middleware

---

## Frontend Requirements

### 1. Features

Build a single-page application with:

- **Login Page**:
  - Username/email and password fields
  - Form validation
  - Login button
  - Error handling for invalid credentials
  - Redirect to items page on successful login

- **Registration Page** (Optional but recommended):
  - Registration form with validation
  - Password confirmation
  - Success/error feedback

- **Item List Page** (Protected):
  - Table displaying all items with pagination
  - Search/filter functionality
  - Sort by columns
  - Delete button with confirmation modal
  - Edit button navigating to edit form
  - Logout button

- **Create Item Form** (Protected):
  - Form with validation
  - Submit button
  - Success/error feedback

- **Edit Item Form** (Protected):
  - Pre-filled form with existing data
  - Update button
  - Success/error feedback

- **UI States**:
  - Loading indicators during API calls
  - Error messages for failed operations
  - Success notifications
  - Empty state when no items exist

- **Authentication Flow**:
  - Store JWT token in localStorage or Redux state
  - Attach token to API requests via Axios interceptors
  - Redirect to login on 401 responses
  - Persist authentication state across page refreshes
  - Protected routes that require authentication

### 2. State Management

- Use Redux Toolkit for global state management
- Organize with proper separation: Slices, Thunks, and Services
- Authentication state (user, token, isAuthenticated, loading, error)
- Item state (items list, current item, loading, error, pagination)

### 3. Form Handling

- **React Hook Form** + Zod validation (recommended)
- Client-side validation matching backend schemas
- Real-time validation feedback
- Field-level error display
- Use zodResolver to integrate Zod schemas with React Hook Form

### 4. API Integration

- **Axios Instance**: Centralized configuration with baseURL and timeout
- **Request Interceptors**: Attach JWT token to Authorization header
- **Response Interceptors**: Handle 401 errors and redirect to login
- **Service Layer**: Separate API calls from Redux thunks
- All API calls should go through the centralized Axios instance

### 5. UI/UX Requirements

- **Ant Design Components**: Table, Form, Button, Modal, Input, etc.
- **Responsive Design**: Mobile-friendly layout
- **Professional Styling**: Clean, consistent design
- **User Feedback**: Toast notifications, loading spinners
- **Confirmation Dialogs**: For destructive actions (delete)

### 6. Component Structure

**Important:** Use **functional components** only. Do NOT use class components. All React components should be functional components using hooks.

---

## Bonus Points (Optional but Highly Valued)

### Backend Bonuses:
- [ ] Prisma migrations with proper migration files
- [ ] Seed script for sample data
- [ ] Unit tests for services using Jest
- [ ] Integration tests for API endpoints using Supertest
- [ ] Docker support (Dockerfile + docker-compose.yml)

### Frontend Bonuses:
- [ ] Unit tests for components/Redux using Jest + React Testing Library
- [ ] Form unit tests with validation scenarios
- [ ] Export items to Excel functionality
- [ ] Responsive design for mobile/tablet
- [ ] Loading skeleton screens
- [ ] Debounced search input

---

## Deliverables

### Repository Structure
You can submit either:
1. **Monorepo**: Single repository with `/backend` and `/frontend` folders
2. **Separate Repos**: Two repositories (one for backend, one for frontend)

### Required Files

**Backend:**
- Source code with proper structure
- `package.json` with all dependencies
- `prisma/schema.prisma`
- `.env.example` (do not commit actual `.env`)
- `README.md` with:
  - Setup instructions
  - Environment variables required
  - Database setup guide (migrations, seeding)
  - API endpoint documentation
  - How to run tests (if implemented)

**Frontend:**
- Source code with proper structure
- `package.json` with all dependencies
- `.env.example` (API base URL, etc.)
- `README.md` with:
  - Setup instructions
  - Environment variables required
  - How to run the development server
  - Build instructions
  - How to run tests (if implemented)

### README Requirements

Both backend and frontend READMEs must include:

1. **Prerequisites**: Node.js version, MySQL version
2. **Installation Steps**: Clear step-by-step guide
3. **Configuration**: Environment variables explanation
4. **Database Setup**: Migration and seeding commands
5. **Running the Application**: Development and production commands
6. **API Documentation**: Endpoint list with request/response examples
7. **Testing**: How to run tests (if implemented)
8. **Project Structure**: Brief explanation of folder organization
9. **Known Issues**: Any limitations or known bugs
10. **Future Enhancements**: Potential improvements

---

## Submission Guidelines

1. **Code Quality**: Write clean, readable, and maintainable code
2. **Git Commits**: Clear, descriptive commit messages
3. **Documentation**: Comprehensive README files
4. **Working Application**: Ensure the application runs without errors
5. **Follow Requirements**: Implement all core requirements listed above

### What We're Looking For:
- Production-quality code that could be deployed to a real environment
- Understanding of software architecture and design patterns
- Attention to detail in validation, error handling, and user experience
- Ability to follow specifications and industry best practices

Good luck! We look forward to reviewing your submission.
