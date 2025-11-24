# Frontend-Backend Integration - Summary of Changes

## 🎯 Objective
Configure the MERN stack to properly communicate between React frontend (Netlify) and Express backend (Render.com).

---

## ✅ Changes Made

### 1️⃣ **Client Environment Variables** (`.env`)
```diff
- VITE_BACKEND_URL='https://backend-server-blog-trial.onrender.com'
- VITE_API_BASE_URL='https://backend-server-blog-trial.onrender.com
+ VITE_BACKEND_URL=https://backend-server-blog-trial.onrender.com
+ VITE_API_BASE_URL=https://backend-server-blog-trial.onrender.com
```
✅ Removed quotes and fixed missing closing quote

---

### 2️⃣ **Centralized API Service** (`client/src/services/Api.js`)
```diff
  import axios from 'axios';
  
  const API_BASE_URL = import.meta.env.VITE_API_BASE_URL || 'http://localhost:5000';
  
  export const api = axios.create({
    baseURL: API_BASE_URL,
  });
  
+ // Add request interceptor to include auth token
+ api.interceptors.request.use(
+   (config) => {
+     const token = localStorage.getItem('token');
+     if (token) {
+       config.headers.Authorization = `Bearer ${token}`;
+     }
+     return config;
+   },
+   (error) => Promise.reject(error)
+ );
  
  export default api;
```
✅ Added JWT token interceptor for automatic auth on all requests

---

### 3️⃣ **Auth Context** (`client/src/context/AuthContext.jsx`)
**Before**: Used hardcoded URLs and direct axios
```javascript
await axios.post('https://backend-server-blog-trial.onrender.com/api/auth/login', {...})
axios.defaults.headers.common['Authorization'] = `Bearer ${token}`;
```

**After**: Uses centralized API service
```javascript
import api from '../services/Api';
await api.post('/api/auth/login', {...})
// Token automatically added by interceptor
```
✅ Removed hardcoded URLs and axios header management

---

### 4️⃣ **Home Page** (`client/src/pages/Home.jsx`)
**Before**:
```javascript
import axios from 'axios';
const res = await axios.get('https://backend-server-blog-trial.onrender.com/api/posts?limit=6');
```

**After**:
```javascript
import api from '../services/Api';
const res = await api.get('/api/posts?limit=6');
```
✅ Switched to centralized API service

---

### 5️⃣ **Create Post Page** (`client/src/pages/createPost.jsx`)
```diff
- import axios from 'axios';
+ import api from '../services/Api';

- const res = await axios.post('https://backend-server-blog-trial.onrender.com/api/posts', postData);
+ const res = await api.post('/api/posts', postData);
```
✅ Uses API service instead of hardcoded URLs

---

### 6️⃣ **Post Detail Page** (`client/src/pages/postDetail.jsx`)
```diff
- import axios from 'axios';
+ import api from '../services/Api';

- const res = await axios.get(`https://backend-server-blog-trial.onrender.com/api/posts/${id}`);
+ const res = await api.get(`/api/posts/${id}`);

- await axios.delete(`https://backend-server-blog-trial.onrender.com/api/posts/${id}`);
+ await api.delete(`/api/posts/${id}`);
```
✅ All requests use centralized service

---

### 7️⃣ **Backend Auth Route** (`server/routes/auth.js`)
```diff
- const User = require('../models/User');
+ const User = require('../models/user');
```
✅ Fixed case sensitivity issue (file is `user.js` not `User.js`)

---

### 8️⃣ **Backend Auth Middleware** (`server/middleWare/auth.js`)
```diff
- const User = require('../models/User');
+ const User = require('../models/user');
```
✅ Fixed case sensitivity issue

---

### 9️⃣ **Backend CORS Configuration** (`server/server.js`)
```diff
  app.use(cors({
-   origin: ['http://localhost:5173', 'https://blog-app-3b5173.netlify.app/'],
+   origin: ['http://localhost:5173', 'http://localhost:3000', 'https://blog-app-3b5173.netlify.app'],
    credentials: true
  }));
```
✅ Removed trailing slash from Netlify URL
✅ Added local dev port 3000 support

---

## 📡 API Flow Diagram

```
Frontend (React)
    ↓
[Login/Register] → api.post('/api/auth/login')
                        ↓
                    [Interceptor adds JWT]
                        ↓
                    Backend (Express)
                        ↓
                    [Validate JWT]
                        ↓
                    [MongoDB Query]
                        ↓
                    [Return Response]
                        ↓
                    localStorage.setItem('token')
```

---

## 🌐 Deployment Configuration

| Component | Local | Production |
|-----------|-------|------------|
| Frontend | http://localhost:5173 | https://blog-app-3b5173.netlify.app |
| Backend | http://localhost:5000 | https://backend-server-blog-trial.onrender.com |
| DB | localhost:27017 | MongoDB Atlas (via URI) |

---

## 🔐 Security Improvements

✅ JWT tokens stored securely in localStorage
✅ Automatic token attachment to all requests
✅ CORS properly restricted to known domains
✅ Protected routes validated on backend
✅ Password hashing with bcrypt
✅ Token expiration set to 30 days

---

## 🧪 Testing Checklist

- [ ] Register new user at https://blog-app-3b5173.netlify.app/register
- [ ] Login with credentials
- [ ] Create a new blog post
- [ ] View post details
- [ ] Edit your own post
- [ ] Delete your own post
- [ ] View all posts on home page
- [ ] Filter posts by category
- [ ] Verify token is stored in browser localStorage
- [ ] Test logout and login again

---

## 📚 Backend API Reference

### Authentication
- `POST /api/auth/register` - Create account
- `POST /api/auth/login` - Login

### Posts (All with JWT auth where required)
- `GET /api/posts` - List all posts
  - Query: `?page=1&limit=10&category=Tech&featured=true`
- `GET /api/posts/:id` - Get single post
- `POST /api/posts` - Create post (protected)
- `PUT /api/posts/:id` - Update post (protected, author only)
- `DELETE /api/posts/:id` - Delete post (protected, author only)

### Health Check
- `GET /api/health` - Server status

---

## 🚀 Deployment Instructions

### Frontend (Netlify)
1. Push to GitHub (already deployed)
2. Set environment variables in Netlify dashboard:
   - `VITE_API_BASE_URL`: `https://backend-server-blog-trial.onrender.com`

### Backend (Render)
1. Push to GitHub
2. Connect GitHub repo to Render
3. Set environment variables:
   - `MONGODB_URI`: Your MongoDB connection string
   - `JWT_SECRET`: Your secret key
   - `NODE_ENV`: production

---

## ⚠️ Common Issues & Fixes

| Issue | Cause | Solution |
|-------|-------|----------|
| CORS Error | Origins not allowed | Check server CORS config |
| 401 Unauthorized | No/invalid token | Ensure token in localStorage |
| Module not found | Case sensitivity | Check file names match imports |
| 404 API endpoints | Wrong base URL | Check .env file |
| Blank page | Frontend not loading | Check Netlify deployment |

---

## 📝 Notes

- All components now use centralized `api` service
- JWT token automatically attached to requests via interceptor
- Environment variables properly configured
- Backend imports fixed for Windows file system
- CORS allows localhost development and Netlify production

