# lab_06_local_passport_auth_service

Minimal Node.js + Express example demonstrating local authentication with Passport.js (passport-local) and sessions.

This project implements:

- User registration (passwords hashed with `bcryptjs`)
- Login via Passport local strategy (server-side sessions)
- Protected profile endpoint requiring an authenticated session
- Logout that ends the session

## Project layout

- `app.js` - app entrypoint, session and passport initialization
- `config/passport.js` - passport local strategy, serialize/deserialize
- `routes/auth.js` - register, login, profile, logout routes
- `models/User.js` - Mongoose user model with password hashing
- `package.json` - dependencies

## Requirements

- Node.js (>= 16 recommended)
- npm (or yarn)
- MongoDB running locally at `mongodb://127.0.0.1:27017`

By default the app connects to the `passport_local_demo` database and listens on port 3000.

## Install

From the `local_passport_auth_service` folder run:

```powershell
cd local_passport_auth_service
npm install
```

## Run

Start the server:

```powershell
node app.js
```

You should see:

```
MongoDB connected
Server running on http://localhost:3000
```

## API Endpoints

Base path: `/auth`

- POST `/auth/register`
  - Body (JSON): `{ "username": "<username>", "password": "<password>" }`
  - Creates a new user (password hashed) and returns a success message.

- POST `/auth/login`
  - Body (form or JSON): `{ "username": "<username>", "password": "<password>" }`
  - Uses Passport local strategy. On success, a session is created and subsequent requests (with the session cookie) will be authenticated.

- GET `/auth/profile`
  - Protected endpoint. Returns the authenticated user's data (passport attaches `req.user`). Returns 401 if not authenticated.

- GET `/auth/logout`
  - Logs out the current user (calls `req.logout`) and returns a confirmation.

## Example (curl, PowerShell)

1) Register:

```powershell
curl -X POST http://localhost:3000/auth/register -H "Content-Type: application/json" -d '{"username":"bob","password":"secret"}'
```

2) Login (save cookie):

```powershell
curl -c cookies.txt -X POST http://localhost:3000/auth/login -H "Content-Type: application/json" -d '{"username":"bob","password":"secret"}'
```

3) Access profile with cookie:

```powershell
curl -b cookies.txt http://localhost:3000/auth/profile
```

4) Logout:

```powershell
curl -b cookies.txt http://localhost:3000/auth/logout
```

If you prefer, use Postman/Insomnia for cookie management and testing.

## Configuration notes

- The MongoDB URI and session secret are hard-coded in `app.js`:
  - MongoDB: `mongodb://127.0.0.1:27017/passport_local_demo`
  - Session secret: `mysecretkey`

- For production, move these values to environment variables and enable secure cookie options.

## Security considerations

- Passwords are hashed using `bcryptjs` before saving to the database.
- The sample uses express-session with in-memory store (default). For production, use a persistent session store (e.g., `connect-mongo`).
- Add input validation and rate limiting on auth endpoints before production use.

## Possible improvements

- Add `dotenv` and a `.env.example` so configuration is not hard-coded.
- Replace the default session store with `connect-mongo` or Redis.
- Add CSRF protection and helmet for common security headers.
- Add unit/integration tests for auth flows.

## License

ISC
