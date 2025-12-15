Task Manager – Advanced Features (Bonus Submission)

Advanced Web Development – Fall 2025
Student: [Your Name]

📌 Overview

This project extends the base Task Manager template with four advanced features:

JWT Authentication with Refresh Token Flow

JWT Blacklist using a Bloom Filter (Logout Protection)

Task Caching with Deno KV

WebSocket Real-Time Task Updates

All features are fully implemented end-to-end in both the backend (Deno) and frontend (HTML/JS).

📂 Project Structure
project/
│
├── backend/
│   ├── src/
│   │   ├── routes/
│   │   ├── lib/
│   │   ├── controllers/
│   │   ├── kv.ts
│   │   ├── middleware.ts
│   │   ├── repo.ts
│   │   ├── taskRepo.ts
│   │   ├── db.ts
│   │   └── main.ts
│   ├── migration/
│   └── data/app.sqlite
│
├── public/
│   ├── index.html
│   ├── app.js
│   └── style.css
│
└── README.md   ← (this file)

1️⃣ JWT Authentication + Refresh Tokens
Endpoints

POST /api/register

POST /api/login

POST /api/auth/refresh

POST /api/logout

GET /api/me

Flow

Login returns:

accessToken (short-lived)

refreshToken (long-lived)

Frontend stores them in localStorage.

Auto-Refresh Logic (Frontend)

All API requests go through apiFetch():

Send request with Authorization: Bearer <accessToken>

If response is 401, try refresh:

POST /api/auth/refresh

If refresh succeeds:

Store new tokens

Retry the failed request automatically

If refresh fails:

User is logged out

This matches a production-grade authentication system.

2️⃣ JWT Blacklist with Bloom Filter

A Bloom filter protects the system from reused or leaked access tokens.

Implementation

Stored in Deno KV (jwt_blacklist_bits).

Uses 3 hash functions with bit array.

On logout:

Access token is added to blacklist

All refresh tokens for the user are revoked

JWT Verification

In verifyJWT():

Check Bloom filter BEFORE parsing the token

Verify token signature

Check Bloom filter again (double protection)

Admin Endpoints

GET /api/admin/blacklist-stats

POST /api/admin/clear-blacklist

This ensures logout is immediate even though access tokens normally expire later.

3️⃣ Task Caching with Deno KV

Reduces DB load and speeds up /api/tasks.

Cache Details

Key format → tasks:user:<userId>

Cache TTL → 30 seconds

On GET /api/tasks:

Check KV first

If cache hit → return instantly

If miss → query DB and store in KV

On task create/update/delete:

Cache is invalidated automatically

Result

Users experience instant task loading and the backend performs fewer DB queries.

4️⃣ WebSocket Real-Time Updates

Users instantly see task changes without refreshing the page.

Backend

WebSocket endpoint at /ws

Broadcasts events:

task_created

task_updated

task_deleted

Frontend

Opens WebSocket on login

Listens for task events

Updates:

Board (Kanban)

Calendar view

Analytics charts

This enables multi-tab real-time sync.

📊 Screenshots (from demo)

(Replace with your own images if needed)

Login

Adding tasks

Due date working

Token refresh flow

Real-time updates

Calendar view

Analytics view

Settings + theme toggle

Admin blacklist stats

WebSocket console logs

🎥 Demo Video

A short demo video is included in the submission showing:

Login & token flow

Creating tasks

Automatic refresh-token retry

WebSocket real-time updates

Calendar & analytics functionality