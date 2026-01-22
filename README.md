# Sneaks-Up E-commerce Platform

A full-stack e-commerce application that combines a Next.js storefront with an Express + PostgreSQL API, real-time customer support chat, and back-office tooling for product, sales, and support workflows.

## Specifications (High-Level)

### User Roles & Access
- **Customers** can register/login with JWT-based authentication and access protected resources via bearer tokens. Role-based checks are enforced in the backend middleware to gate protected endpoints (e.g., sales manager, product manager, support).
- **Staff roles** include support, sales manager, and product manager; the server ensures default roles on startup for role-based access control and tooling access.【F:backend/src/server.js†L33-L45】【F:backend/src/server.js†L256-L290】

### Core Functionalities
- **Product catalog** with search, sorting, category filtering, CRUD management, and image uploads (Multer to `/uploads`).
- **Cart & checkout** flow with order creation, order item pricing, and payment method capture (credit card vs. account).
- **Orders & returns** including cancellation, refunds, and return request workflows that enforce delivery status and time windows, plus staff approval paths.
- **Invoices** generated as PDFs per order and downloadable via a dedicated endpoint, with email delivery support through Nodemailer.
- **Reviews & wishlists** managed through dedicated API routes and frontend helpers for add/remove flows.
- **Analytics** summary endpoint for revenue, cost, and profit series within a date range (sales manager only).
- **Real-time support chat** with Socket.IO rooms, queue claiming, typing indicators, message receipts, and secure file attachments (private storage).

### Frontend Experience
- **Next.js App Router** pages for product browsing, cart, orders, wishlist, account, login/register, admin, sales, and support workspaces.
- **API helper layer** for authenticated fetches, invoices, analytics, returns, and chat attachment downloads.

## Tech Stack

### Frontend
- **Next.js 16 (App Router)**, **React 19**, and **Tailwind CSS** for UI and routing.
- **Recharts** for analytics visualization and **Socket.IO client** for real-time chat UI updates.

### Backend
- **Node.js + Express** for the API server, with **Helmet**, **CORS**, and **express-rate-limit** for security/hardening.
- **PostgreSQL** with **Drizzle ORM** for data access and migrations.
- **JWT** authentication, **bcrypt** password hashing, and **Zod** validation.
- **Socket.IO** for real-time support chat, plus **Multer** + **PDFKit** for uploads and invoice generation.
- **Nodemailer** for invoice and notification emails (demo routing noted in code).
## Project Structure

```
.
├── backend/            # Express API, Drizzle ORM, Socket.IO server
├── frontend/           # Next.js app (App Router)
└── docs/               # Documentation and bug reports
```

## Environment Variables

### Backend (`backend/.env`)
- `DATABASE_URL` – PostgreSQL connection string.
- `JWT_SECRET` – signing secret for access/refresh tokens.
- `SMTP_HOST`, `SMTP_PORT`, `SMTP_USER`, `SMTP_PASS` – email transport settings for invoices/notifications.
- `PORT` – API port (defaults to 4000).

### Frontend (`frontend/.env.local`)
- `NEXT_PUBLIC_API_BASE_URL` – API base URL for the frontend (defaults to `http://localhost:4000`).
## Local Development

### Backend
```bash
cd backend
npm install
npm run dev
```
Scripts for migrations and testing are available in `backend/package.json`.

### Frontend
```bash
cd frontend
npm install
npm run dev
```
Frontend build, start, lint, and test scripts are available in `frontend/package.json`.
## Key API Areas (Examples)

- **Auth**: `/auth/register`, `/auth/login`, `/auth/refresh` for JWT-based sessions.
- **Products**: `/products` search + sorting, `/products/:id` detail, and admin/manager CRUD.
- **Orders & Returns**: `/orders` create, `/orders/returns` for staff return workflows and approvals.
- **Invoices**: `/invoice/:orderId` returns a PDF inline for download/viewing.
- **Analytics**: `/analytics/summary?from=YYYY-MM-DD&to=YYYY-MM-DD` for sales reporting.
- **Chat**: `/chat` API + Socket.IO events for support queue and messaging.【F:backend/src/routes/chat.js†L1-L200】【F:backend/src/server.js†L71-L249】

## Notes
- Uploads for product images are served from `/uploads`, while chat attachments are stored in a private directory and accessed through secure endpoints.【F:backend/src/server.js†L23-L41】【F:backend/src/routes/chat.js†L73-L118】
- Email utilities are configured for demo use and can be wired to a production SMTP account by adjusting environment variables and routing logic.【F:backend/src/utils/email.js†L1-L106】
