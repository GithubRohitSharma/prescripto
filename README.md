# Prescripto – Full‑Stack Doctor Appointment Platform

A production‑ready MERN application for discovering doctors, booking appointments, managing profiles, and administering the platform. This monorepo contains three apps:

- backend: Node.js/Express REST API with MongoDB, JWT auth, Cloudinary uploads, Stripe/Razorpay payments
- frontend: Patient‑facing React app (Vite + Tailwind)
- admin: Admin/Doctor portal React app (Vite + Tailwind)

## Live Links

- Patient Frontend: https://prescripto-frontend-dun-phi.vercel.app
- Admin Portal: https://prescripto-admin-bay.vercel.app
- Backend API: https://prescripto-fawn.vercel.app

## Tech Stack

- Backend: Node.js, Express, MongoDB (Mongoose), JWT, Multer, Cloudinary SDK, Validator
- Frontend/Admin: React 18, Vite, React Router, TailwindCSS, Axios, React‑Toastify
- Payments: Stripe, Razorpay (optional)
- Deployment/Infra: Vercel, Environment variables (.env)

## Folder Structure

```
prescripto-main/
  backend/            # Express API
    config/           # DB & cloudinary config
    controllers/      # Route handlers
    middleware/       # Auth, multer
    models/           # Mongoose schemas
    routes/           # Express routers
    server.js         # App entry
    package.json
  frontend/           # Patient app (Vite + React)
    src/
    index.html
    package.json
  admin/              # Admin/Doctor app (Vite + React)
    src/
    index.html
    package.json
  README.md
```

## Features

- Patient: register/login, browse doctors by speciality, book/cancel appointments, payments (Stripe/Razorpay), profile management
- Doctor: login, view appointments, mark complete/cancel, toggle availability, profile updates
- Admin: login, add doctors (Cloudinary image upload), list doctors, appointments list, dashboard metrics
- JWT authentication with role‑specific middlewares (user/doctor/admin)
- Image uploads via Multer → Cloudinary
- Responsive UI with Tailwind and toast notifications

## Getting Started (Local)

Prerequisites:

- Node.js 18+
- MongoDB database (local or Atlas)
- Cloudinary account
- Optional: Stripe and/or Razorpay credentials

1. Install dependencies

```bash
# From repo root
cd backend && npm install
cd ../frontend && npm install
cd ../admin && npm install
```

2. Configure environment variables

Create the following .env files (do not commit them):

backend/.env

```env
PORT=4000
MONGODB_URI=mongodb+srv://<user>:<pass>@<cluster>/<db>?retryWrites=true&w=majority
JWT_SECRET=your_long_random_secret

# Admin credentials (used for admin login)
ADMIN_EMAIL=admin@example.com
ADMIN_PASSWORD=supersecret

# Cloudinary
CLOUDINARY_CLOUD_NAME=xxxx
CLOUDINARY_API_KEY=xxxx
CLOUDINARY_API_SECRET=xxxx

# Payments (optional)
STRIPE_SECRET=sk_live_or_test
RAZORPAY_KEY_ID=rzp_test_xxxx
RAZORPAY_KEY_SECRET=xxxx

# CORS / payment redirects
FRONTEND_URL=http://localhost:5173
```

frontend/.env

```env
VITE_BACKEND_URL=http://localhost:4000
```

admin/.env

```env
VITE_BACKEND_URL=http://localhost:4000
```

3. Run services

```bash
# Terminal 1
cd backend && npm run server

# Terminal 2
cd frontend && npm run dev

# Terminal 3
cd admin && npm run dev
```

Local URLs:

- Backend API: http://localhost:4000
- Frontend (patients): http://localhost:5173
- Admin/Doctor portal: http://localhost:5174 (or as printed by Vite)

## NPM Scripts

Backend (backend/package.json):

- npm run start – production start
- npm run server – dev with nodemon

Frontend/Admin (frontend and admin):

- npm run dev – Vite dev server
- npm run build – production build
- npm run preview – preview build locally

## API Overview

Base path: /api

User routes (/api/user):

- POST /register – register user
- POST /login – login (returns token)
- GET /get-profile – get profile (Auth: user)
- POST /update-profile – update profile with optional image (multipart) (Auth: user)
- POST /book-appointment – book appointment (Auth: user)
- GET /appointments – list appointments (Auth: user)
- POST /cancel-appointment – cancel appointment (Auth: user)
- POST /payment-razorpay – start payment (Auth: user)
- POST /verifyRazorpay – verify razorpay payment (Auth: user)
- POST /payment-stripe – start stripe payment (Auth: user)
- POST /verifyStripe – verify stripe payment (Auth: user)

Doctor routes (/api/doctor):

- POST /login – login (returns token)
- GET /appointments – list assigned appointments (Auth: doctor)
- POST /cancel-appointment – cancel appointment (Auth: doctor)
- POST /complete-appointment – mark appointment complete (Auth: doctor)
- POST /change-availability – toggle availability (Auth: doctor)
- GET /dashboard – summary metrics (Auth: doctor)
- GET /profile – doctor profile (Auth: doctor)
- POST /update-profile – update doctor profile (Auth: doctor)

Admin routes (/api/admin):

- POST /login – login (returns token)
- POST /add-doctor – add doctor with image (multipart) (Auth: admin)
- GET /appointments – all appointments (Auth: admin)
- POST /cancel-appointment – cancel appointment (Auth: admin)
- GET /all-doctors – list doctors (Auth: admin)
- POST /change-availability – toggle doctor availability (Auth: admin)
- GET /dashboard – admin dashboard metrics (Auth: admin)

Auth headers:

- User: utoken: <jwt>
- Doctor: dtoken: <jwt>
- Admin: atoken: <jwt>

## Environment Variables (Summary)

Backend (backend/.env):

- PORT – API port
- MONGODB_URI – MongoDB connection string
- JWT_SECRET – JWT HMAC secret
- ADMIN_EMAIL, ADMIN_PASSWORD – admin static credentials
- CLOUDINARY_CLOUD_NAME, CLOUDINARY_API_KEY, CLOUDINARY_API_SECRET
- Optional payments: STRIPE_SECRET, RAZORPAY_KEY_ID, RAZORPAY_KEY_SECRET
- Optional: FRONTEND_URL (for CORS/payment redirects)

Frontends (frontend/.env, admin/.env):

- VITE_BACKEND_URL – absolute URL to backend, including protocol
  - Local: http://localhost:4000
  - Production: https://prescripto-fawn.vercel.app (or your backend Vercel domain)

Important: Always include the protocol (http:// or https://). Omitting it causes the browser to treat the URL as relative to the frontend origin.

## Deployment (Vercel)

This project is deployed on Vercel: frontend, admin, and backend.

### Backend

- Project root: backend
- Environment Variables: set all from the Backend .env section in Vercel → Settings → Environment Variables
- Node version: 18+
- Build & Output Settings:
  - Install Command: npm install
  - Build Command: (leave empty – Express server)
  - Output Directory: (leave empty)
- Runtime: Vercel Node runtime

### Frontend (Patients)

- Project root: frontend
- Env: VITE_BACKEND_URL=https://prescripto-fawn.vercel.app
- Build: npm run build
- Output: dist

### Admin (Doctors/Admin)

- Project root: admin
- Env: VITE_BACKEND_URL=https://prescripto-fawn.vercel.app
- Build: npm run build
- Output: dist

### CORS

- Backend enables permissive CORS via app.use(cors()). Restrict to your domains for production.

## Troubleshooting

- Admin login returns 405 (Axios ERR_BAD_REQUEST)

  - Cause: VITE_BACKEND_URL missing protocol, e.g. prescripto-fawn.vercel.app instead of https://prescripto-fawn.vercel.app
  - Fix: Set VITE_BACKEND_URL to the full absolute URL including https://

- 401 on protected routes

  - Ensure you pass the correct header: utoken, dtoken, or atoken (lowercase) with the JWT
  - Verify the token is stored in localStorage and included in Axios headers

- Image upload fails when adding a doctor

  - Ensure Cloudinary credentials are set and valid
  - Request must be multipart/form-data

- MongoDB connection errors
  - Check MONGODB_URI, IP allowlist (Atlas), and network egress

## Security Notes

- Never hardcode secrets; use environment variables
- Use strong JWT_SECRET and admin credentials
- Consider HTTPS and secure cookies when deploying behind a reverse proxy
- Validate payloads and sanitize user input

## Contributing

Contributions are welcome! Please open an issue to discuss changes, then submit a PR.

## License

This project is provided as‑is for learning and demonstration. Add your preferred license if you plan to distribute.

## Acknowledgements

- React, Vite, TailwindCSS
- Express, Mongoose
- Cloudinary, Stripe, Razorpay
