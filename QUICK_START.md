# Quick Start Guide - MERN Blog App

## 🚀 Deployed Application
**Live URL**: https://blog-app-3b5173.netlify.app

---

## 🛠️ Development Setup

### Prerequisites
- Node.js (v14+)
- MongoDB (local or Atlas)
- Git

### Step 1: Clone Repository
```bash
git clone <repository-url>
cd mern-stack-integration-iampunit123
```

### Step 2: Setup Backend
```bash
cd server
npm install

# Create .env file
echo "MONGODB_URI=mongodb://localhost:27017/blogdb
NODE_ENV=development
JWT_SECRET=punit-6969
PORT=5000" > .env

npm start
# Backend runs on http://localhost:5000
```

### Step 3: Setup Frontend
```bash
cd ../client
npm install

# .env already configured with production backend
# For local development, create .env.local:
echo "VITE_API_BASE_URL=http://localhost:5000" > .env.local

npm run dev
# Frontend runs on http://localhost:5173
```

---

## 📱 Features Overview

### User Features
- ✅ Register new account
- ✅ Login/Logout
- ✅ Create blog posts
- ✅ Edit own posts
- ✅ Delete own posts
- ✅ View all posts
- ✅ Filter by category
- ✅ View featured posts
- ✅ View post details

### Admin/Author Features
- ✅ Mark posts as featured
- ✅ Add tags to posts
- ✅ Set read time
- ✅ Upload cover images

---

## 🔑 Test Credentials

### Option 1: Create New Account
1. Visit https://blog-app-3b5173.netlify.app/register
2. Fill in name, email, password
3. Click "Create account"

### Option 2: Use Sample Data
After running `npm install` in server:
```bash
npm run seed
# Populates database with sample posts and users
```

---

## 📡 API Endpoints Quick Reference

### Auth
- `POST /api/auth/register` - Create account
- `POST /api/auth/login` - Login

### Posts
- `GET /api/posts` - List all posts
- `GET /api/posts/:id` - Get single post
- `POST /api/posts` - Create (auth required)
- `PUT /api/posts/:id` - Update (auth required)
- `DELETE /api/posts/:id` - Delete (auth required)

### Health
- `GET /api/health` - Check server status

---

## 🔧 Environment Variables

### Frontend (`client/.env`)
```env
VITE_BACKEND_URL=https://backend-server-blog-trial.onrender.com
VITE_API_BASE_URL=https://backend-server-blog-trial.onrender.com
```

### Backend (`server/.env`)
```env
MONGODB_URI=mongodb://localhost:27017/blogdb
NODE_ENV=development
JWT_SECRET=punit-6969
PORT=5000
```

---

## 📚 Project Structure

```
client/
├── src/
│   ├── services/Api.js       # Axios instance with interceptors
│   ├── context/AuthContext.jsx
│   ├── pages/
│   │   ├── Home.jsx
│   │   ├── Login.jsx
│   │   ├── Register.jsx
│   │   ├── createPost.jsx
│   │   └── postDetail.jsx
│   ├── components/Navbar.jsx
│   └── App.jsx
└── .env                      # Configuration

server/
├── routes/
│   ├── auth.js              # Authentication
│   └── posts.js             # Blog posts
├── models/
│   ├── user.js              # User schema
│   └── Post.js              # Post schema
├── middleWare/auth.js       # JWT validation
├── server.js                # Express app
├── seed.js                  # Sample data
└── .env                     # Configuration
```

---

## 🧪 Testing Workflow

### 1. Register
```
Visit: /register
Email: testuser@example.com
Password: Test@123
Name: Test User
```

### 2. Create Post
```
Visit: /create
Title: My First Blog Post
Content: Write something interesting...
Cover Image: Paste image URL
Category: Technology
Read Time: 5 min
Tags: blog, test
```

### 3. View Posts
```
Visit: / (Home)
See all posts in grid
Click post to read full content
```

### 4. Edit Post
```
On post detail page (if author):
Click "Edit Post" button
Modify content
Save changes
```

### 5. Delete Post
```
On post detail page (if author):
Click "Delete Post" button
Confirm deletion
```

---

## 🐛 Troubleshooting

### Frontend Won't Start
```bash
# Clear node_modules and reinstall
rm -rf node_modules package-lock.json
npm install
npm run dev
```

### Backend Connection Error
```bash
# Check MongoDB is running
# Verify MONGODB_URI in .env
# Restart server: npm start
```

### CORS Error
```
✅ Backend CORS already configured for:
   - http://localhost:5173 (dev)
   - http://localhost:3000 (dev)
   - https://blog-app-3b5173.netlify.app (prod)
```

### Login Not Working
```bash
# Check token in localStorage:
# 1. Open DevTools (F12)
# 2. Go to Application > LocalStorage
# 3. Look for 'token' and 'user' keys
# 4. Clear if needed: localStorage.clear()
```

### Posts Not Loading
```bash
# Check API endpoint:
curl https://backend-server-blog-trial.onrender.com/api/posts

# Check token attached:
# DevTools > Network > XHR requests > Check headers
```

---

## 📝 Database Models

### User
```javascript
{
  name: String,
  email: String (unique),
  password: String (hashed),
  createdAt: Date,
  updatedAt: Date
}
```

### Post
```javascript
{
  title: String,
  content: String,
  excerpt: String,
  coverImage: String,
  category: String,
  author: ObjectId (ref: User),
  tags: [String],
  readTime: Number,
  featured: Boolean,
  likes: Number,
  createdAt: Date,
  updatedAt: Date
}
```

---

## 🚀 Deployment Commands

### Deploy to Netlify (Frontend)
```bash
cd client
npm run build
# Login to Netlify and connect repository
```

### Deploy to Render (Backend)
```bash
# Push to GitHub
# Connect repo to Render.com
# Set environment variables
# Deploy
```

---

## 💡 Tips & Tricks

1. **localStorage Debugging**: Open DevTools → Application → LocalStorage
2. **API Testing**: Use curl or Postman to test endpoints
3. **MongoDB Atlas**: Use free tier for development
4. **Dark Mode**: Add to future features
5. **Comments**: Feature coming soon
6. **Image Upload**: Replace URL input with file upload

---

## 📞 Support

- **GitHub**: https://github.com/PLP-MERN-Stack-Development/
- **Issues**: Report on GitHub
- **Documentation**: See CONFIGURATION_GUIDE.md, INTEGRATION_SUMMARY.md

---

## ✅ Checklist Before Deployment

- [ ] All environment variables set
- [ ] Backend running and accessible
- [ ] Frontend connects to backend
- [ ] Register/Login works
- [ ] Can create posts
- [ ] Can edit/delete own posts
- [ ] CORS errors resolved
- [ ] No console errors
- [ ] Database backup created
- [ ] Performance tested

---

## 📚 Learn More

- [Express.js Docs](https://expressjs.com/)
- [React Docs](https://react.dev/)
- [MongoDB Docs](https://docs.mongodb.com/)
- [JWT.io](https://jwt.io/)
- [Axios Docs](https://axios-http.com/)

---

**Version**: 1.0
**Last Updated**: November 24, 2025
**Status**: ✅ Production Ready

