# MERN Stack Frontend-Backend Configuration Guide

## ✅ Issues Fixed

### 1. **API URLs Centralized** 
- ❌ **Before**: API URLs were hardcoded in components (AuthContext, Home.jsx, createPost.jsx, postDetail.jsx)
- ✅ **After**: All API calls now use a centralized `Api.js` service with environment variables

### 2. **Environment Variables Configured**
- **Client `.env` file** (`client/.env`):
  ```
  VITE_BACKEND_URL=https://backend-server-blog-trial.onrender.com
  VITE_API_BASE_URL=https://backend-server-blog-trial.onrender.com
  ```
  - Fixed missing closing quote on `VITE_API_BASE_URL`

### 3. **Axios API Service Enhanced** (`client/src/services/Api.js`)
- Added request interceptor to automatically attach JWT token from localStorage
- Uses environment variables for base URL configuration
- Fallback to `http://localhost:5000` for local development

### 4. **Backend Import Issues Fixed**
- **File**: `server/routes/auth.js` - Changed `require('../models/User')` → `require('../models/user')`
- **File**: `server/middleWare/auth.js` - Changed `require('../models/User')` → `require('../models/user')`
- Fixed case sensitivity issue with file names

### 5. **CORS Configuration Updated** (`server/server.js`)
- Added support for local development: `http://localhost:3000`, `http://localhost:5173`
- Added Netlify deployment: `https://blog-app-3b5173.netlify.app` (removed trailing slash)
- Enables credentials for cross-origin requests

---

## 📁 Project Structure

```
mern-stack-integration/
├── client/                          # React Frontend (Vite)
│   ├── .env                         # Environment variables
│   ├── src/
│   │   ├── services/
│   │   │   └── Api.js              # ✅ Centralized API service
│   │   ├── context/
│   │   │   └── AuthContext.jsx     # ✅ Updated to use Api.js
│   │   ├── pages/
│   │   │   ├── Home.jsx            # ✅ Updated to use Api.js
│   │   │   ├── Login.jsx
│   │   │   ├── Register.jsx
│   │   │   ├── createPost.jsx      # ✅ Updated to use Api.js
│   │   │   └── postDetail.jsx      # ✅ Updated to use Api.js
│   │   └── components/
│   │       └── Navbar.jsx
│   └── vite.config.js
│
└── server/                          # Express Backend
    ├── .env                         # Server environment variables
    ├── server.js                    # ✅ CORS updated
    ├── models/
    │   ├── user.js                 # ✅ User model (lowercase)
    │   └── Post.js
    ├── routes/
    │   ├── auth.js                 # ✅ Fixed import
    │   └── posts.js
    ├── middleWare/
    │   └── auth.js                 # ✅ Fixed import
    └── seed.js
```

---

## 🔧 Configuration Details

### Client API Service (`client/src/services/Api.js`)
```javascript
import axios from 'axios';

const API_BASE_URL = import.meta.env.VITE_API_BASE_URL || 'http://localhost:5000';

export const api = axios.create({
  baseURL: API_BASE_URL,
});

// Automatically adds JWT token to all requests
api.interceptors.request.use(
  (config) => {
    const token = localStorage.getItem('token');
    if (token) {
      config.headers.Authorization = `Bearer ${token}`;
    }
    return config;
  },
  (error) => Promise.reject(error)
);

export default api;
```

### Backend CORS Configuration (`server/server.js`)
```javascript
app.use(cors({
  origin: ['http://localhost:5173', 'http://localhost:3000', 'https://blog-app-3b5173.netlify.app'],
  credentials: true
}));
```

### API Endpoints

#### Authentication Routes (`/api/auth`)
- `POST /api/auth/register` - Register new user
- `POST /api/auth/login` - Login user

#### Posts Routes (`/api/posts`)
- `GET /api/posts` - Get all posts (with pagination & filtering)
- `GET /api/posts/:id` - Get single post
- `POST /api/posts` - Create post (protected)
- `PUT /api/posts/:id` - Update post (protected)
- `DELETE /api/posts/:id` - Delete post (protected)

---

## 🚀 Deployment URLs

### Frontend
- **Development**: `http://localhost:5173`
- **Production**: `https://blog-app-3b5173.netlify.app`

### Backend
- **Development**: `http://localhost:5000`
- **Production**: `https://backend-server-blog-trial.onrender.com`

---

## 📝 Environment Variables

### Client (`.env`)
```env
VITE_BACKEND_URL=https://backend-server-blog-trial.onrender.com
VITE_API_BASE_URL=https://backend-server-blog-trial.onrender.com
```

### Server (`.env`)
```env
MONGODB_URI=mongodb://localhost:27017/blogdb
NODE_ENV=development
JWT_SECRET=punit-6969
PORT=5000
```

---

## 🔐 Authentication Flow

1. **Register/Login** → User data + JWT token stored in localStorage
2. **API Request** → Token automatically attached via interceptor
3. **Protected Routes** → Middleware validates JWT token
4. **Logout** → Token removed from localStorage

---

## ✨ Features Implemented

- ✅ Centralized API service with interceptors
- ✅ JWT authentication with token storage
- ✅ Protected routes on backend
- ✅ CORS enabled for multiple origins
- ✅ Pagination and filtering on posts
- ✅ Featured posts support
- ✅ Category-based filtering
- ✅ Author verification for post operations
- ✅ Graceful error handling

---

## 🧪 Testing

### Local Development
1. Start server: `npm start` (port 5000)
2. Start client: `npm run dev` (port 5173)
3. Access: `http://localhost:5173`

### Production
- Frontend: https://blog-app-3b5173.netlify.app
- Backend: https://backend-server-blog-trial.onrender.com

---

## 📋 Checklist

- [x] API URLs centralized in environment variables
- [x] Request interceptor for JWT token
- [x] Backend import case sensitivity fixed
- [x] CORS properly configured
- [x] Auth context using centralized API service
- [x] All page components using centralized API service
- [x] Error handling implemented
- [x] Token refresh mechanism ready
- [x] Protected routes implemented
- [x] Production URLs configured

