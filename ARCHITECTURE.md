# Frontend-Backend Architecture Diagram

## System Architecture

```
┌─────────────────────────────────────────────────────────────────────────┐
│                        MERN Stack Architecture                          │
└─────────────────────────────────────────────────────────────────────────┘

                          DEPLOYMENT LAYER
                          
     ┌──────────────────┐              ┌──────────────────┐
     │     NETLIFY      │              │      RENDER      │
     │  (Frontend)      │              │    (Backend)     │
     │                  │              │                  │
     │ blog-app-3b5173  │              │ backend-server   │
     │ .netlify.app     │              │ -blog-trial      │
     │                  │              │ .onrender.com    │
     └────────┬─────────┘              └────────┬─────────┘
              │                                  │
              │         HTTP/HTTPS              │
              │         CORS Enabled            │
              │◄────────────────────────────────►│
              │                                  │


                    APPLICATION LAYER

┌──────────────────────────┐        ┌──────────────────────────┐
│    REACT FRONTEND        │        │  EXPRESS BACKEND         │
│   (Vite + React Router)  │        │   (REST API)             │
├──────────────────────────┤        ├──────────────────────────┤
│ • HomePage               │        │ • Auth Routes            │
│ • LoginPage              │        │ • Post Routes            │
│ • RegisterPage           │        │ • Middleware             │
│ • CreatePostPage         │        │ • JWT Validation         │
│ • PostDetailPage         │        │ • CORS Handler           │
│ • NavBar                 │        │ • Error Handler          │
└────────┬─────────────────┘        └────────┬─────────────────┘
         │                                   │
         │  .env                            │  .env
         │  ─────────────────────           │  ──────────────────
         │  VITE_API_BASE_URL               │  MONGODB_URI
         │  ◄─ https://backend-...          │  JWT_SECRET
         │                                  │  NODE_ENV
         │                                  │  PORT


                    API SERVICE LAYER

┌─────────────────────────────────────────────────────────────────┐
│              AXIOS API SERVICE (Api.js)                         │
├─────────────────────────────────────────────────────────────────┤
│                                                                 │
│  Request Interceptor:                                          │
│  ┌──────────────────────────────────────────────────────────┐  │
│  │ 1. Get token from localStorage                          │  │
│  │ 2. Attach to Authorization header: Bearer {token}       │  │
│  │ 3. Forward request to backend                           │  │
│  └──────────────────────────────────────────────────────────┘  │
│                                                                 │
│  Response Interceptor:                                         │
│  ┌──────────────────────────────────────────────────────────┐  │
│  │ 1. Check response status                                │  │
│  │ 2. Handle errors/unauthorized                           │  │
│  │ 3. Return data to component                             │  │
│  └──────────────────────────────────────────────────────────┘  │
│                                                                 │
└─────────────────────────────────────────────────────────────────┘


                    DATA FLOW

USER ACTIONS → REACT COMPONENT → API SERVICE → INTERCEPTOR
                                                    │
                                                    ▼
                                            BACKEND API
                                                    │
RESPONSE ◄── UPDATE STATE ◄── RESPONSE ◄── DATABASE


            AUTHENTICATION FLOW

┌─────────────────────────────────────────────────────────────┐
│                      REGISTER/LOGIN                         │
└─────────────────────────────────────────────────────────────┘

  1. USER FORM
     ┌──────────────────┐
     │ Email: ...       │
     │ Password: ...    │
     │ [Submit]         │
     └────────┬─────────┘
              │
              ▼
  2. FRONTEND VALIDATION
     ┌──────────────────────────────┐
     │ • Email format valid?         │
     │ • Password min 6 chars?       │
     │ • Passwords match?            │
     └────────┬─────────────────────┘
              │
              ▼
  3. API CALL
     ┌─────────────────────────────────┐
     │ POST /api/auth/register          │
     │ { email, password, name }        │
     │ Header: Content-Type: JSON       │
     └────────┬────────────────────────┘
              │
              ▼
  4. BACKEND VALIDATION
     ┌────────────────────────────────┐
     │ • Email already exists?         │
     │ • Hash password with bcrypt     │
     │ • Save to MongoDB               │
     └────────┬───────────────────────┘
              │
              ▼
  5. TOKEN GENERATION
     ┌──────────────────────────────┐
     │ JWT.sign({id: user._id})      │
     │ Expires in 30 days            │
     └────────┬─────────────────────┘
              │
              ▼
  6. RESPONSE
     ┌──────────────────────────────┐
     │ { token, user }              │
     │ HTTP 200 OK                  │
     └────────┬─────────────────────┘
              │
              ▼
  7. FRONTEND STORAGE
     ┌──────────────────────────────┐
     │ localStorage.setItem('token')│
     │ localStorage.setItem('user') │
     │ Set authContext state        │
     │ Redirect to home page        │
     └──────────────────────────────┘


            PROTECTED ROUTE FLOW

┌─────────────────────────────────────────────────────────────┐
│             ACCESSING PROTECTED ENDPOINT                    │
└─────────────────────────────────────────────────────────────┘

  1. USER ACTION
     ┌──────────────────────┐
     │ Click "Create Post"  │
     └────────┬─────────────┘
              │
              ▼
  2. INTERCEPTOR ADDS TOKEN
     ┌──────────────────────────────────────┐
     │ POST /api/posts                      │
     │ {title, content, ...}                │
     │ Headers: {                           │
     │   Authorization: Bearer {jwt_token}  │
     │   Content-Type: application/json     │
     │ }                                    │
     └────────┬─────────────────────────────┘
              │
              ▼
  3. BACKEND MIDDLEWARE (auth.js)
     ┌──────────────────────────────────────┐
     │ 1. Extract token from header         │
     │ 2. Verify token with JWT_SECRET      │
     │ 3. Decode user ID                    │
     │ 4. Load user from MongoDB            │
     │ 5. Attach to req.user                │
     │ 6. Call next()                       │
     └────────┬─────────────────────────────┘
              │
              ▼
  4. ROUTE HANDLER
     ┌──────────────────────────────────────┐
     │ • Create post with req.user.id       │
     │ • Save to MongoDB                    │
     │ • Return created post                │
     └────────┬─────────────────────────────┘
              │
              ▼
  5. RESPONSE
     ┌──────────────────────────────────────┐
     │ { _id, title, content, author, ... } │
     │ HTTP 201 Created                     │
     └────────┬─────────────────────────────┘
              │
              ▼
  6. FRONTEND UPDATE
     ┌──────────────────────────────────────┐
     │ • Update component state             │
     │ • Show success message               │
     │ • Redirect to post detail page       │
     └──────────────────────────────────────┘


           DATABASE SCHEMA

┌─────────────────────────────────────────────────────────────┐
│                       MONGODB                               │
├─────────────────────────────────────────────────────────────┤
│                                                             │
│  Collection: users                                         │
│  ┌──────────────────────────────────────────────────────┐  │
│  │ {                                                    │  │
│  │   _id: ObjectId,                                     │  │
│  │   name: String,                                      │  │
│  │   email: String (unique),                            │  │
│  │   password: String (hashed),                         │  │
│  │   createdAt: Date,                                   │  │
│  │   updatedAt: Date                                    │  │
│  │ }                                                    │  │
│  └──────────────────────────────────────────────────────┘  │
│                                                             │
│  Collection: posts                                         │
│  ┌──────────────────────────────────────────────────────┐  │
│  │ {                                                    │  │
│  │   _id: ObjectId,                                     │  │
│  │   title: String,                                     │  │
│  │   content: String,                                   │  │
│  │   excerpt: String,                                   │  │
│  │   coverImage: String (URL),                          │  │
│  │   category: String,                                  │  │
│  │   author: ObjectId (ref: users),                     │  │
│  │   tags: [String],                                    │  │
│  │   readTime: Number,                                  │  │
│  │   featured: Boolean,                                 │  │
│  │   likes: Number,                                     │  │
│  │   createdAt: Date,                                   │  │
│  │   updatedAt: Date                                    │  │
│  │ }                                                    │  │
│  └──────────────────────────────────────────────────────┘  │
│                                                             │
└─────────────────────────────────────────────────────────────┘


              REQUEST/RESPONSE EXAMPLES

┌─────────────────────────────────────────────────────────────┐
│                      LOGIN REQUEST                         │
└─────────────────────────────────────────────────────────────┘

URL: POST https://backend-server-blog-trial.onrender.com/api/auth/login

Headers:
  Content-Type: application/json
  
Body:
  {
    "email": "user@example.com",
    "password": "password123"
  }

Response (200 OK):
  {
    "token": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...",
    "user": {
      "id": "507f1f77bcf86cd799439011",
      "name": "John Doe",
      "email": "user@example.com"
    }
  }

Response (401 Unauthorized):
  {
    "message": "Invalid credentials"
  }


┌─────────────────────────────────────────────────────────────┐
│                   CREATE POST REQUEST                       │
└─────────────────────────────────────────────────────────────┘

URL: POST https://backend-server-blog-trial.onrender.com/api/posts

Headers:
  Content-Type: application/json
  Authorization: Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...

Body:
  {
    "title": "My Amazing Blog Post",
    "content": "Long form content here...",
    "excerpt": "Short summary...",
    "coverImage": "https://example.com/image.jpg",
    "category": "Technology",
    "tags": ["javascript", "react"],
    "readTime": 5,
    "featured": false
  }

Response (201 Created):
  {
    "_id": "507f1f77bcf86cd799439012",
    "title": "My Amazing Blog Post",
    "content": "Long form content here...",
    "excerpt": "Short summary...",
    "coverImage": "https://example.com/image.jpg",
    "category": "Technology",
    "author": {
      "_id": "507f1f77bcf86cd799439011",
      "name": "John Doe"
    },
    "tags": ["javascript", "react"],
    "readTime": 5,
    "featured": false,
    "likes": 0,
    "createdAt": "2024-11-24T10:30:00Z",
    "updatedAt": "2024-11-24T10:30:00Z"
  }


           COMPONENT DEPENDENCY TREE

App.jsx
├── AuthProvider (context)
│   └── AuthContext.jsx
│       └── Uses Api.js service
├── Navbar.jsx
│   └── Uses useAuth hook
├── Router
│   ├── Home.jsx
│   │   └── Uses api.get()
│   ├── Login.jsx
│   │   └── Uses login() from AuthContext
│   ├── Register.jsx
│   │   └── Uses register() from AuthContext
│   ├── CreatePost.jsx
│   │   └── Uses api.post()
│   └── PostDetail.jsx
│       ├── Uses api.get()
│       ├── Uses api.put()
│       └── Uses api.delete()


                 ENVIRONMENT VARIABLES

Client (.env):
  VITE_API_BASE_URL=https://backend-server-blog-trial.onrender.com
  VITE_BACKEND_URL=https://backend-server-blog-trial.onrender.com

Server (.env):
  MONGODB_URI=mongodb+srv://user:pass@cluster.mongodb.net/blogdb
  JWT_SECRET=punit-6969
  NODE_ENV=production
  PORT=5000


                    ERROR HANDLING FLOW

API Request
    │
    ├─► Success (2xx)
    │   └─► Return data
    │
    ├─► Client Error (4xx)
    │   ├─► 400 Bad Request
    │   ├─► 401 Unauthorized (invalid token)
    │   ├─► 403 Forbidden (not author)
    │   └─► 404 Not Found
    │
    └─► Server Error (5xx)
        ├─► 500 Internal Server Error
        └─► Display error message to user

```

---

## Key Points

✅ **Separation of Concerns** - Frontend and backend are independent
✅ **Security** - JWT tokens for authentication
✅ **CORS** - Properly configured for cross-origin requests
✅ **Scalability** - Easy to add new endpoints
✅ **Error Handling** - Proper HTTP status codes
✅ **Real-time Data** - Components update on API response

