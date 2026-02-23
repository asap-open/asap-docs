# Authentication API

All API endpoints (except authentication endpoints) require a valid JWT token in the `Authorization` header.

## Base URL

```
http://localhost:3000/api
```

## Endpoints

### Sign Up

Create a new user account.

**Endpoint:** `POST /auth/signup`

**Request Body:**

```json
{
  "username": "johndoe",
  "email": "john@example.com",
  "password": "SecurePass123!",
  "fullName": "John Doe"
}
```

**Fields:**

- `username` (required): Unique username
- `email` (required): Unique email address
- `password` (required): Account password
- `fullName` (optional): Display name — creates a profile entry if provided

**Response:** `201 Created`

```json
{
  "message": "User created successfully",
  "token": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...",
  "user": {
    "id": "user-uuid",
    "username": "johndoe",
    "email": "john@example.com"
  }
}
```

**Errors:**

- `400` - `username`, `email`, or `password` missing
- `400` - Username or email already in use

---

### Sign In

Authenticate and receive a JWT token.

**Endpoint:** `POST /auth/signin`

**Request Body:**

```json
{
  "identifier": "johndoe",
  "password": "SecurePass123!"
}
```

**Fields:**

- `identifier` (required): Either the user's **email** or **username**
- `password` (required): Account password

**Response:** `200 OK`

```json
{
  "message": "Login successful",
  "token": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...",
  "user": {
    "id": "user-uuid",
    "username": "johndoe",
    "email": "john@example.com"
  }
}
```

**Errors:**

- `400` - `identifier` or `password` missing
- `401` - Invalid credentials

---

### Logout

Client-side token invalidation. Clears any server-side session state.

**Endpoint:** `POST /auth/logout`

**Response:** `200 OK`

```json
{
  "message": "Logout successful"
}
```

---

## Using the Token

Include the JWT token in all authenticated requests:

```bash
curl -H "Authorization: Bearer YOUR_TOKEN_HERE" \
  http://localhost:3000/api/profile
```

### Token Structure

The JWT contains the `userId` and an expiration claim. Expiry is controlled by the `TOKEN_EXP` environment variable (default `7d`).

### Token Expiration

When a token expires the API returns `401 Unauthorized` and the user must sign in again.

## Example: Complete Authentication Flow

```javascript
// Sign up
const signupRes = await fetch("http://localhost:3000/api/auth/signup", {
  method: "POST",
  headers: { "Content-Type": "application/json" },
  body: JSON.stringify({
    username: "johndoe",
    email: "john@example.com",
    password: "SecurePass123!",
    fullName: "John Doe",
  }),
});
const { token, user } = await signupRes.json();

// Sign in (using email or username as identifier)
const signinRes = await fetch("http://localhost:3000/api/auth/signin", {
  method: "POST",
  headers: { "Content-Type": "application/json" },
  body: JSON.stringify({
    identifier: "john@example.com", // or "johndoe"
    password: "SecurePass123!",
  }),
});
const { token } = await signinRes.json();

// Use token for authenticated requests
const profileRes = await fetch("http://localhost:3000/api/profile", {
  headers: { Authorization: `Bearer ${token}` },
});

// Logout
await fetch("http://localhost:3000/api/auth/logout", {
  method: "POST",
});
```
