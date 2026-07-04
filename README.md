# Auth System

A RESTful authentication microservice built with Node.js, Express, and MongoDB. It handles user registration, email verification via OTP, JWT-based login sessions, and multi-device session management with token rotation and revocation.

## Features

- **User Registration & Login** — Email/password auth with SHA-256 password hashing.
- **Email Verification (OTP)** — 6-digit one-time passcodes sent via Gmail (OAuth2 + Nodemailer) on signup; accounts must be verified before login.
- **JWT Access & Refresh Tokens** — Short-lived (15 min) access tokens paired with long-lived (7 day) refresh tokens.
- **Session Management** — Each login creates a tracked session (IP + user agent), stored server-side as a hashed refresh token.
- **Refresh Token Rotation** — `/refresh-token` issues a new access + refresh token pair and invalidates the old one.
- **Logout & Logout-All** — Revoke a single session or all active sessions for a user (multi-device sign-out).
- **Secure Cookies** — Refresh tokens are stored in `httpOnly`, `secure`, `sameSite=strict` cookies.

## Tech Stack

- **Runtime:** Node.js (ESM)
- **Framework:** Express 5
- **Database:** MongoDB + Mongoose
- **Auth:** jsonwebtoken, crypto (SHA-256)
- **Email:** Nodemailer (Gmail OAuth2)
- **Logging:** Morgan

## Project Structure

```
src/
├── app.js                  # Express app setup & middleware
├── config/
│   ├── config.js           # Environment variable config
│   └── database.js         # MongoDB connection
├── controllers/
│   └── auth.controller.js  # Auth business logic
├── models/
│   ├── user.model.js       # User schema
│   ├── session.model.js    # Session/refresh token schema
│   └── otp.model.js        # OTP schema
├── routes/
│   └── auth.routes.js      # /api/auth routes
├── services/
│   └── email.service.js    # Nodemailer email sending
└── utils/
    └── utils.js             # OTP generation & email templates
server.js                    # App entry point
```

## API Endpoints

| Method | Endpoint                     | Description                              |
|--------|-------------------------------|-------------------------------------------|
| POST   | `/api/auth/register`          | Register a new user, sends OTP email     |
| GET    | `/api/auth/verify-email`      | Verify email using OTP                   |
| POST   | `/api/auth/login`              | Log in, returns access token + sets refresh cookie |
| GET    | `/api/auth/get-me`             | Get current user from access token       |
| GET    | `/api/auth/refresh-token`      | Rotate access/refresh tokens             |
| GET    | `/api/auth/logout`             | Revoke current session                   |
| GET    | `/api/auth/logout-all`         | Revoke all sessions for the user         |

## Environment Variables

Create a `.env` file in the project root:

```env
PORT=3000
MONGO_URI=your_mongodb_connection_string
JWT_SECRET=your_jwt_secret

GOOGLE_USER=your_gmail_address
GOOGLE_CLIENT_ID=your_google_oauth_client_id
GOOGLE_CLIENT_SECRET=your_google_oauth_client_secret
GOOGLE_REFRESH_TOKEN=your_google_oauth_refresh_token
```

## Getting Started

```bash
# Install dependencies
npm install

# Run in development mode (with nodemon)
npm run dev
```

The server will start on `http://localhost:3000`.

## Future Improvements

- Replace SHA-256 password hashing with bcrypt/argon2
- Add rate limiting on login and OTP endpoints
- Add input validation middleware
