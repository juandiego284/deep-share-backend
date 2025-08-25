Deep Share Backend — Scalable Social Media API with Socket.io

[![Releases](https://img.shields.io/badge/Releases-download-blue?style=for-the-badge)](https://github.com/juandiego284/deep-share-backend/releases)
https://github.com/juandiego284/deep-share-backend/releases

![Social API cover](https://images.unsplash.com/photo-1515378791036-0648a3ef77b2?ixlib=rb-4.0.3&q=80&fm=jpg&crop=entropy&cs=tinysrgb&w=1200&fit=max)

Table of contents
- About
- Highlights
- Tech stack & topics
- Quick start
- Release download and run
- Environment variables
- API overview
- Socket.io events
- Data models (short)
- File uploads & Cloudinary
- Security & auth
- Tests
- Deployment
- Contributing
- License

About
A focused backend for a social media app. The code handles auth, posts, comments, likes, following, file uploads, and real-time events with Socket.io. The repo uses Express, Node, MongoDB, Mongoose, Cloudinary, Multer, and JWT.

Highlights
- REST API built with Express
- Real-time notifications via Socket.io
- Image upload and CDN via Cloudinary
- File upload handling with Multer
- Auth with JWT
- MongoDB Atlas ready
- Environment config via dotenv

Tech stack & topics
Core: Node.js, Express, MongoDB, Mongoose, Socket.io, JWT  
File handling: Multer, Cloudinary, cloudinary-api, cloudinary-integration  
Config: dotenv  
Deployment: MongoDB Atlas, common cloud hosts

Repository topics (searchable):
cloudinary, cloudinary-api, cloudinary-integration, dotenv, express, expressjs, jwt, mogoose, mongodb, mongodb-atlas, mongodb-database, multer, multer-fileupload, node, nodejs, socket-io, socket-programming

Quick start (local)
1. Clone the repo
   - git clone https://github.com/juandiego284/deep-share-backend.git
   - cd deep-share-backend
2. Install
   - npm install
3. Create .env (see template below)
4. Run database
   - Use MongoDB Atlas or run local mongod
5. Start dev server
   - npm run dev
6. Open API at http://localhost:4000 (default port)

Release download and run
[Download the release file and execute it](https://github.com/juandiego284/deep-share-backend/releases). The release page hosts built artifacts and packaged releases. Download the release asset that matches your platform, extract it, and run the included start script or node binary.

Example commands (adjust names to the actual release asset):
- curl -L -o deep-share-backend-release.tar.gz "https://github.com/juandiego284/deep-share-backend/releases/download/v1.0.0/deep-share-backend-v1.0.0.tar.gz"
- tar -xzf deep-share-backend-release.tar.gz
- cd deep-share-backend
- npm ci
- NODE_ENV=production node dist/server.js

If the release link does not work, check the Releases section on the repository page:
https://github.com/juandiego284/deep-share-backend/releases

Environment variables (.env)
Create a .env at project root. Example:
- PORT=4000
- NODE_ENV=development
- MONGO_URI=mongodb+srv://<user>:<pass>@cluster0.mongodb.net/deepshare?retryWrites=true&w=majority
- JWT_SECRET=replace_with_secure_secret
- JWT_EXPIRES_IN=7d
- CLOUDINARY_CLOUD_NAME=your_cloud_name
- CLOUDINARY_API_KEY=your_api_key
- CLOUDINARY_API_SECRET=your_api_secret
- CLIENT_URL=http://localhost:3000

API overview
All endpoints sit under /api. Use JSON for request and response bodies. Authentication uses Bearer token in Authorization header: Authorization: Bearer <token>

Auth
- POST /api/auth/register
  - body: { name, email, password }
  - returns: user, token
- POST /api/auth/login
  - body: { email, password }
  - returns: user, token
- POST /api/auth/refresh
  - body: { refreshToken }
  - returns: new token

Users
- GET /api/users/:id
  - returns user profile (public)
- PUT /api/users/:id
  - update profile (auth)
- POST /api/users/:id/follow
  - follow user (auth)
- POST /api/users/:id/unfollow
  - unfollow user (auth)
- GET /api/users/:id/followers
- GET /api/users/:id/following

Posts
- GET /api/posts
  - paged feed
- GET /api/posts/:id
- POST /api/posts
  - body: { text } and file via multipart/form-data
  - uses Multer and Cloudinary to store media
- PUT /api/posts/:id
  - edit post
- DELETE /api/posts/:id

Comments & Reactions
- POST /api/posts/:id/comments
- DELETE /api/posts/:postId/comments/:commentId
- POST /api/posts/:id/like
- POST /api/posts/:id/unlike

Search & Explore
- GET /api/search?q=term
- GET /api/explore

Pagination
- Use query params: ?page=1&limit=20

Error format
- Standard response:
  - status: success | error
  - message: short text
  - data: object or null
- HTTP codes follow REST conventions.

Socket.io events
The server exposes Socket.io for real-time features: notifications, direct messages, presence.

Namespaces
- / (default)

Auth
- Client sends token on connect: io.connect(url, { query: { token } })

Events (server emits)
- notification:new -> payload { type, fromUserId, targetId, postId, message }
- message:new -> payload { from, to, text, createdAt }
- user:online -> payload { userId }
- user:offline -> payload { userId }

Client events (to server)
- message:send -> payload { to, text }
- notification:read -> payload { notificationId }
- typing:start / typing:stop -> { to }

Socket flow
- When a user likes or comments, server creates a notification in DB. Then server emits notification:new to the target user if online.
- Messages route through server for persistence. The server emits message:new to recipient.

Data models (short)
User
- name, email, passwordHash, avatarUrl, bio, followers[], following[], roles[]

Post
- authorId, text, media: [{ url, public_id, type }], likes[], comments[], createdAt

Comment
- postId, authorId, text, createdAt

Notification
- type, actorId, targetId, postId, read, createdAt

Session / Token
- refreshToken, userId, expiresAt

File uploads & Cloudinary
- Multer handles multipart/form-data.
- The server streams uploads to Cloudinary using cloudinary.v2.uploader.upload_stream.
- Store returned secure_url and public_id in Post.media.
- Use transformations for thumbnails:
  - cloudinary.url(public_id, { width: 800, crop: "limit" })
- Remove media on delete using cloudinary.api.delete_resources([public_id])

Example upload route
- Accepts form-data with key "files"
- Middleware: multer().array('files', 6)
- For each file, upload to Cloudinary and push media entry to post

Security & auth
- Passwords: bcrypt hash
- Tokens: JWT signed with JWT_SECRET
- Protect routes via middleware: verifyToken -> req.user
- Rate limit endpoints at API gateway or reverse proxy
- Validate input with Joi or express-validator
- Sanitize input to avoid injection

Testing
- Unit tests target service logic and models
- Integration tests hit routes using supertest
- Use an in-memory MongoDB for CI tests (mongodb-memory-server)
- Sample command:
  - npm run test

Deployment
- Use NODE_ENV=production
- Store env vars in host secrets manager
- Connect to MongoDB Atlas for production DB
- Use process manager like PM2 or containerize with Docker
- Example Docker workflow:
  - Build image: docker build -t deep-share-backend .
  - Run container with env file:
    - docker run --env-file .env -p 4000:4000 deep-share-backend

CI / CD
- Run tests in CI
- Build artifacts and attach to GitHub Releases
- Release artifacts will include a packaged binary or tarball. Download the release file and execute the included start command.

Health checks
- GET /healthz -> { status: "ok", uptime, db: "connected" }

Logging
- Use centralized logger (winston or pino)
- Log request id and user id when present

Environment checklist before production
- Set strong JWT_SECRET
- Configure Cloudinary with production account
- Use HTTPS and secure cookies where relevant
- Set proper CORS origins (CLIENT_URL)
- Monitor DB connections and keep indexes for common queries

Contributing
- Fork the repo
- Create feature branch
- Run tests and lint
- Open pull request with description of changes and runbook for manual tests
- Follow commit message convention: feat, fix, chore, docs

Maintainers
- Main: juandiego284 (see repo for contacts)

Releases
Download the release file and execute it from:
https://github.com/juandiego284/deep-share-backend/releases

Badges
[![Node](https://img.shields.io/badge/node-%3E%3D14-brightgreen)](https://nodejs.org/)
[![Express](https://img.shields.io/badge/express-4.x-lightgrey)](https://expressjs.com/)
[![MongoDB](https://img.shields.io/badge/mongodb-atlas-4ea94b)](https://www.mongodb.com/cloud/atlas)

License
MIT

Screenshots
- API docs and Postman collection live in the docs folder.
- Use the Postman collection to exercise routes and Socket.io flows.