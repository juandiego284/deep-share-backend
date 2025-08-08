# DeepShare

This is the backend for a full-stack real-time social media platform built with **Node.js**, **Express**, **MongoDB**, and **Socket.IO**.  
It supports JWT authentication, user management, post creation, likes, comments, profile updates, and real-time updates through sockets.



## 🚀 Features

- ✅ User Registration & Login with JWT
- 🧑 Unique Usernames
- 🔐 Password Hashing with bcrypt
- 📸 Profile & Cover Photo Upload Support
- 📝 Post Creation, Likes, Comments, and Sharing
- 📰 Real-Time News Feed Updates with Socket.IO
- 🔍 Search Users by Username or Name
- 👥 Follow/Unfollow Users
- 📦 REST API Architecture
- 🔄 Real-time Events (like posts, likes, comments)
- 🧵 Modular Codebase (MVC Pattern)



## 🛠 Tech Stack

- [Node.js](https://nodejs.org/)
- [Express.js](https://expressjs.com/)
- [MongoDB + Mongoose](https://mongoosejs.com/)
- [JWT](https://jwt.io/) for authentication
- [Socket.IO](https://socket.io/)
- [Multer](https://github.com/expressjs/multer) for file uploads
- [Cloudinary](https://cloudinary.com/) or Firebase Storage (recommended for production)



## 📁 Project Structure

- deep-share-backend/
- ├── controllers/
- │ ├── authController.js
- │ ├── userController.js
- │ ├── postController.js
- │ └── commentController.js
- ├── models/
- │ ├── User.js
- │ ├── Post.js
- │ ├── Comment.js
- ├── routes/
- │ ├── auth.routes.js
- │ ├── user.routes.js
- │ ├── post.routes.js
- │ ├── comment.routes.js
- ├── middleware/
- │ ├── authMiddleware.js
- │ ├── uploadMiddleware.js
- ├── sockets/
- │ └── socket.js
- ├── utils/
- │ ├── cloudinary.js
- ├── .env
- ├── server.js
- └── package.json

## 📡 API Endpoints
🔐 Auth Routes

- POST	`/api/auth/register`	Register user
- POST	`/api/auth/login`	Login user

👤 User Routes

- GET	`/api/users/:username`	Get public profile
- PUT	`/api/users/update`	Update profile info
- PUT	`/api/users/follow/:id`	Follow a user
- PUT	`/api/users/unfollow/:id`	Unfollow a user
- GET	`/api/users/search?q=`	Search users

📝 Post Routes

- POST	`/api/posts/`	Create post
- GET	`/api/posts/feed	Get` feed
- PUT	`/api/posts/:id/like`	Like/unlike a post
- DELETE	`/api/posts/:id`	Delete post

💬 Comment Routes

- POST	`/api/comments/:postId`	Add comment
- GET	`/api/comments/:postId`	Get post comments

## ⚡ Real-Time Events via Socket.IO
Socket events are initialized when a user connects. Examples:

- new-post
- new-like
- new-comment
- user-connected
- notify

These events emit updates to other online users in real-time.
