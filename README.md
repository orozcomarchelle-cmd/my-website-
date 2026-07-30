# SimpleBank — Demo Full-Stack Banking App

A minimal banking demo: React + Tailwind frontend, Node/Express backend.
**For learning purposes only — do not use as-is for a real financial product.**

## Structure

```
backend/     Express API (in-memory data, JWT auth)
frontend/    React app (Vite + Tailwind)
```

## Running it

**Backend**
```bash
cd backend
npm install
npm run dev        # starts on http://localhost:4000
```

**Frontend** (new terminal)
```bash
cd frontend
npm install
npm run dev         # starts on http://localhost:5173
```

Demo login: `jane@example.com` / `Password123!`

## Security notes (where to look in the code)

| Concern | Where it's handled |
|---|---|
| Password storage | `backend/data/store.js` — bcrypt hashing, never plaintext |
| Authentication | `backend/routes/auth.js` — JWT in an httpOnly cookie |
| Route protection | `backend/middleware/auth.js` — `requireAuth` verifies the token before any handler runs |
| Authorization (not just authentication) | `backend/routes/account.js` — always uses `req.userId` from the verified token, never a client-supplied id |
| Input validation | `express-validator` checks in `routes/auth.js` and `routes/account.js` |
| Brute-force protection | `express-rate-limit` on `/login` and globally in `server.js` |
| XSS mitigation | React escapes rendered text by default; avoid `dangerouslySetInnerHTML` |
| CSRF mitigation | `sameSite` cookie attribute + strict CORS origin |
| Transport security headers | `helmet()` in `server.js` |
| SQL/NoSQL injection | N/A in this demo (in-memory store) — when you add a real DB, always use parameterized queries / an ORM, never string-concatenated queries |

## What's intentionally simplified

- Data is stored in memory (`backend/data/store.js`) and resets on server restart.
- No password reset, email verification, 2FA, or audit logging — all recommended for a real product.
- No database transactions around the transfer logic — a production system needs atomic commits so a crash can't leave money debited without being credited.
