# DEVSPHERE - API Design

## 🌐 API Overview

**Base URL:** `https://api.devsphere.com/v1`  
**Authentication:** Bearer Token (JWT)  
**Response Format:** JSON  
**Error Format:** 
```json
{
  "success": false,
  "message": "Error description",
  "error": "Error code",
  "details": {}
}
```

---

## 🔐 AUTHENTICATION ENDPOINTS

### POST `/api/auth/register`
**Description:** Register a new user

**Request Body:**
```json
{
  "username": "johndoe",
  "email": "john@example.com",
  "password": "securepassword123",
  "profile": {
    "bio": "Full-stack developer",
    "skills": ["JavaScript", "React", "Node.js"]
  }
}
```

**Response:**
```json
{
  "success": true,
  "message": "User registered successfully",
  "data": {
    "user": {
      "id": "user_id",
      "username": "johndoe",
      "email": "john@example.com",
      "profile": {
        "bio": "Full-stack developer",
        "skills": ["JavaScript", "React", "Node.js"]
      }
    },
    "token": "jwt_token_here"
  }
}
```

### POST `/api/auth/login`
**Description:** Login user

**Request Body:**
```json
{
  "email": "john@example.com",
  "password": "securepassword123"
}
```

**Response:** Same as register response

### POST `/api/auth/github`
**Description:** Login with GitHub OAuth

**Request Body:**
```json
{
  "code": "github_oauth_code"
}
```

### GET `/api/auth/me`
**Description:** Get current user profile  
**Authentication:** Required

**Response:**
```json
{
  "success": true,
  "data": {
    "user": {
      "id": "user_id",
      "username": "johndoe",
      "email": "john@example.com",
      "profile": { ... },
      "stats": { ... },
      "createdAt": "2023-01-01T00:00:00.000Z"
    }
  }
}
```

### POST `/api/auth/logout`
**Description:** Logout user  
**Authentication:** Required

### POST `/api/auth/refresh-token`
**Description:** Refresh JWT token

---

## 👤 USER ENDPOINTS

### GET `/api/users`
**Description:** Get all users (with pagination and filtering)

**Query Parameters:**
- `page` (number) - Page number
- `limit` (number) - Results per page
- `search` (string) - Search by username or skills
- `skills` (string) - Filter by skills (comma-separated)

**Response:**
```json
{
  "success": true,
  "data": {
    "users": [...],
    "pagination": {
      "page": 1,
      "limit": 10,
      "total": 100,
      "pages": 10
    }
  }
}
```

### GET `/api/users/:userId`
**Description:** Get user by ID

**Response:**
```json
{
  "success": true,
  "data": {
    "user": {
      "id": "user_id",
      "username": "johndoe",
      "profile": { ... },
      "stats": {
        "projectCount": 15,
        "followerCount": 120,
        "followingCount": 85,
        "likeCount": 450
      },
      "recentProjects": [...],
      "isFollowing": false
    }
  }
}
```

### PUT `/api/users/:userId`
**Description:** Update user profile  
**Authentication:** Required (only own profile)

**Request Body:**
```json
{
  "profile": {
    "bio": "Updated bio",
    "location": "New York",
    "website": "https://johndoe.com",
    "skills": ["JavaScript", "React", "Node.js", "Python"]
  }
}
```

### GET `/api/users/:userId/projects`
**Description:** Get user's projects

### POST `/api/users/:userId/follow`
**Description:** Follow a user  
**Authentication:** Required

### DELETE `/api/users/:userId/follow`
**Description:** Unfollow a user  
**Authentication:** Required

### GET `/api/users/:userId/followers`
**Description:** Get user's followers

### GET `/api/users/:userId/following`
**Description:** Get users followed by this user

---

## 💼 PROJECT ENDPOINTS

### GET `/api/projects`
**Description:** Get all projects (with filtering and pagination)

**Query Parameters:**
- `page` (number) - Page number
- `limit` (number) - Results per page
- `search` (string) - Search in title/description
- `technologies` (string) - Filter by technologies
- `tags` (string) - Filter by tags
- `sort` (string) - Sort by: newest, popular, trending

**Response:**
```json
{
  "success": true,
  "data": {
    "projects": [
      {
        "id": "project_id",
        "title": "Awesome React Project",
        "description": "Project description...",
        "author": {
          "id": "user_id",
          "username": "johndoe",
          "avatar": "avatar_url"
        },
        "content": {
          "technologies": ["React", "Node.js"],
          "liveUrl": "https://project.com",
          "repoUrl": "https://github.com/user/repo"
        },
        "tags": ["web-dev", "react"],
        "stats": {
          "likes": 25,
          "views": 150,
          "commentCount": 8
        },
        "isLiked": false,
        "createdAt": "2023-01-01T00:00:00.000Z"
      }
    ],
    "pagination": { ... }
  }
}
```

### POST `/api/projects`
**Description:** Create a new project  
**Authentication:** Required

**Request Body:**
```json
{
  "title": "My Awesome Project",
  "description": "This is a detailed description of my project",
  "content": {
    "codeSnippet": "console.log('Hello World');",
    "technologies": ["React", "Node.js", "MongoDB"],
    "liveUrl": "https://myproject.com",
    "repoUrl": "https://github.com/user/repo"
  },
  "tags": ["web-dev", "javascript", "beginner"],
  "visibility": "public"
}
```

### GET `/api/projects/:projectId`
**Description:** Get project by ID

**Response:** Full project details with comments

### PUT `/api/projects/:projectId`
**Description:** Update project  
**Authentication:** Required (only author)

### DELETE `/api/projects/:projectId`
**Description:** Delete project  
**Authentication:** Required (only author)

### POST `/api/projects/:projectId/like`
**Description:** Like/unlike a project  
**Authentication:** Required

### GET `/api/projects/:projectId/likes`
**Description:** Get users who liked this project

### POST `/api/projects/:projectId/collaborators`
**Description:** Add collaborator to project  
**Authentication:** Required (only author)

**Request Body:**
```json
{
  "userId": "collaborator_user_id",
  "role": "editor"
}
```

### DELETE `/api/projects/:projectId/collaborators/:userId`
**Description:** Remove collaborator  
**Authentication:** Required (only author)

---

## 💬 COMMENT ENDPOINTS

### GET `/api/projects/:projectId/comments`
**Description:** Get comments for a project

**Query Parameters:**
- `page` (number)
- `limit` (number)

**Response:**
```json
{
  "success": true,
  "data": {
    "comments": [
      {
        "id": "comment_id",
        "content": "Great project!",
        "author": {
          "id": "user_id",
          "username": "commenter",
          "avatar": "avatar_url"
        },
        "likes": 5,
        "isLiked": false,
        "replies": [...],
        "createdAt": "2023-01-01T00:00:00.000Z"
      }
    ],
    "pagination": { ... }
  }
}
```

### POST `/api/projects/:projectId/comments`
**Description:** Add comment to project  
**Authentication:** Required

**Request Body:**
```json
{
  "content": "This is an amazing project!",
  "parentComment": "parent_comment_id" // Optional, for replies
}
```

### PUT `/api/comments/:commentId`
**Description:** Update comment  
**Authentication:** Required (only author)

### DELETE `/api/comments/:commentId`
**Description:** Delete comment  
**Authentication:** Required (only author)

### POST `/api/comments/:commentId/like`
**Description:** Like/unlike a comment  
**Authentication:** Required

---

## 🏆 CHALLENGE ENDPOINTS (PostgreSQL)

### GET `/api/challenges`
**Description:** Get all coding challenges

**Query Parameters:**
- `difficulty` (string) - beginner, intermediate, advanced
- `category` (string) - algorithm, frontend, backend
- `status` (string) - active, upcoming, completed

### POST `/api/challenges`
**Description:** Create a new challenge  
**Authentication:** Required (admin/moderator)

### GET `/api/challenges/:challengeId`
**Description:** Get challenge details

### POST `/api/challenges/:challengeId/submit`
**Description:** Submit solution for challenge  
**Authentication:** Required

**Request Body:**
```json
{
  "code": "function solution() { return true; }",
  "language": "javascript"
}
```

### GET `/api/challenges/:challengeId/leaderboard`
**Description:** Get challenge leaderboard

### POST `/api/challenges/:challengeId/join`
**Description:** Join a challenge  
**Authentication:** Required

---

## 🤖 AI ENDPOINTS

### POST `/api/ai/suggest-projects`
**Description:** Get AI-powered project suggestions  
**Authentication:** Required

**Request Body:**
```json
{
  "skills": ["JavaScript", "React"],
  "difficulty": "intermediate",
  "interests": ["web development", "APIs"]
}
```

**Response:**
```json
{
  "success": true,
  "data": {
    "suggestions": [
      {
        "title": "Build a Real-time Chat App",
        "description": "Create a chat application using React and Socket.io",
        "technologies": ["React", "Node.js", "Socket.io", "MongoDB"],
        "difficulty": "intermediate",
        "estimatedTime": "2-3 weeks"
      }
    ]
  }
}
```

### POST `/api/ai/code-review`
**Description:** Get AI code review  
**Authentication:** Required

**Request Body:**
```json
{
  "code": "function example() { return true; }",
  "language": "javascript"
}
```

### POST `/api/ai/debug-help`
**Description:** Get AI debugging assistance  
**Authentication:** Required

**Request Body:**
```json
{
  "code": "problematic code here",
  "error": "error message",
  "language": "javascript"
}
```

---

## 🔔 NOTIFICATION ENDPOINTS

### GET `/api/notifications`
**Description:** Get user notifications  
**Authentication:** Required

**Query Parameters:**
- `page` (number)
- `limit` (number)
- `unreadOnly` (boolean)

### PUT `/api/notifications/:notificationId/read`
**Description:** Mark notification as read  
**Authentication:** Required

### PUT `/api/notifications/read-all`
**Description:** Mark all notifications as read  
**Authentication:** Required

### GET `/api/notifications/unread-count`
**Description:** Get unread notifications count  
**Authentication:** Required

---

## 📊 STATISTICS ENDPOINTS

### GET `/api/stats/overview`
**Description:** Get platform overview statistics

**Response:**
```json
{
  "success": true,
  "data": {
    "totalUsers": 1500,
    "totalProjects": 3200,
    "totalComments": 12500,
    "activeChallenges": 8,
    "topTechnologies": ["JavaScript", "Python", "React", "Node.js"]
  }
}
```

### GET `/api/stats/user/:userId`
**Description:** Get user statistics

### GET `/api/stats/trending-technologies`
**Description:** Get trending technologies

### GET `/api/stats/project-analytics/:projectId`
**Description:** Get project analytics  
**Authentication:** Required (only author)

---

## 🔍 SEARCH ENDPOINTS

### GET `/api/search`
**Description:** Global search across users, projects, challenges

**Query Parameters:**
- `q` (string) - Search query
- `type` (string) - users, projects, challenges, all
- `page` (number)
- `limit` (number)

**Response:**
```json
{
  "success": true,
  "data": {
    "users": [...],
    "projects": [...],
    "challenges": [...],
    "pagination": { ... }
  }
}
```

### GET `/api/search/suggestions`
**Description:** Get search suggestions

---

## 🛠️ HEALTH & UTILITY ENDPOINTS

### GET `/api/health`
**Description:** API health check

**Response:**
```json
{
  "success": true,
  "data": {
    "status": "OK",
    "timestamp": "2023-01-01T00:00:00.000Z",
    "version": "1.0.0",
    "environment": "production"
  }
}
```

### GET `/api/status`
**Description:** System status with database connections

### POST `/api/upload`
**Description:** File upload endpoint  
**Authentication:** Required

---

## 📡 REAL-TIME ENDPOINTS (WebSocket Events)

### WebSocket Connection: `ws://api.devsphere.com/v1/ws`

**Events:**
- `connection` - User connects
- `join:project` - Join project room
- `leave:project` - Leave project room
- `comment:new` - New comment
- `comment:update` - Comment updated
- `comment:delete` - Comment deleted
- `project:like` - Project liked/unliked
- `notification:new` - New notification
- `user:online` - User online status
- `typing:start` - User started typing
- `typing:stop` - User stopped typing

---

## 🎯 PHASE 1 IMPLEMENTATION PRIORITY

### Must-Have for MVP:
1. `/api/auth/*` - Authentication
2. `/api/users/*` - User management
3. `/api/projects/*` - Project CRUD
4. `/api/comments/*` - Comment system
5. `/api/health` - Health check

### Phase 2:
1. `/api/ai/*` - AI features
2. `/api/notifications/*` - Notifications
3. `/api/search/*` - Search functionality

### Phase 3:
1. `/api/challenges/*` - Coding challenges
2. `/api/stats/*` - Analytics
3. WebSocket endpoints

---

## 🔒 RATE LIMITING

- **Authentication endpoints:** 5 requests per minute
- **General endpoints:** 100 requests per minute
- **AI endpoints:** 10 requests per minute
- **Upload endpoints:** 20 requests per minute

---

