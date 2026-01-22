# Sneaks-Up E-commerce Platform

A full-stack e-commerce application that combines a Next.js storefront with an Express + PostgreSQL API, real-time customer support chat, and back-office tooling for product, sales, and support workflows.

## Specifications (High-Level)

### User Roles & Access
- **Customers** can register/login with JWT-based authentication and access protected resources via bearer tokens. Role-based checks are enforced in the backend middleware to gate protected endpoints (e.g., sales manager, product manager, support).【F:backend/src/routes/auth.js†L1-L121】【F:backend/src/middleware/auth.js†L1-L118】
- **Staff roles** include support, sales manager, and product manager; the server ensures default roles on startup for role-based access control and tooling access.【F:backend/src/server.js†L33-L45】【F:backend/src/server.js†L256-L290】

### Core Functionalities
- **Product catalog** with search, sorting, category filtering, CRUD management, and image uploads (Multer to `/uploads`).【F:backend/src/routes/products.js†L1-L173】
- **Cart & checkout** flow with order creation, order item pricing, and payment method capture (credit card vs. account).【F:frontend/lib/api.js†L92-L194】【F:backend/src/routes/orders.js†L1-L67】
- **Orders & returns** including cancellation, refunds, and return request workflows that enforce delivery status and time windows, plus staff approval paths.【F:backend/src/routes/orders.js†L69-L200】【F:backend/src/routes/returns.js†L1-L160】
- **Invoices** generated as PDFs per order and downloadable via a dedicated endpoint, with email delivery support through Nodemailer.【F:backend/src/routes/invoice.js†L1-L120】【F:backend/src/utils/email.js†L1-L106】
- **Reviews & wishlists** managed through dedicated API routes and frontend helpers for add/remove flows.【F:backend/src/routes/reviews.js†L1-L120】【F:backend/src/routes/wishlist.js†L1-L120】【F:frontend/lib/api.js†L245-L318】
- **Analytics** summary endpoint for revenue, cost, and profit series within a date range (sales manager only).【F:backend/src/routes/analytics.js†L1-L122】
- **Real-time support chat** with Socket.IO rooms, queue claiming, typing indicators, message receipts, and secure file attachments (private storage).【F:backend/src/server.js†L71-L249】【F:backend/src/routes/chat.js†L1-L200】

### Frontend Experience
- **Next.js App Router** pages for product browsing, cart, orders, wishlist, account, login/register, admin, sales, and support workspaces.【F:frontend/app/products/page.jsx†L1-L200】【F:frontend/app/cart/page.jsx†L1-L200】【F:frontend/app/orders/page.jsx†L1-L200】【F:frontend/app/login/page.jsx†L1-L200】【F:frontend/app/register/page.jsx†L1-L200】【F:frontend/app/admin/page.jsx†L1-L200】【F:frontend/app/sales-admin/page.jsx†L1-L200】【F:frontend/app/support/page.jsx†L1-L200】
- **API helper layer** for authenticated fetches, invoices, analytics, returns, and chat attachment downloads.【F:frontend/lib/api.js†L1-L381】

## Tech Stack

### Frontend
- **Next.js 16 (App Router)**, **React 19**, and **Tailwind CSS** for UI and routing.【F:frontend/package.json†L1-L29】
- **Recharts** for analytics visualization and **Socket.IO client** for real-time chat UI updates.【F:frontend/package.json†L1-L29】

### Backend
- **Node.js + Express** for the API server, with **Helmet**, **CORS**, and **express-rate-limit** for security/hardening.【F:backend/package.json†L1-L30】【F:backend/src/server.js†L1-L73】
- **PostgreSQL** with **Drizzle ORM** for data access and migrations.【F:backend/package.json†L1-L30】【F:backend/src/db/index.js†L1-L18】
- **JWT** authentication, **bcrypt** password hashing, and **Zod** validation.【F:backend/package.json†L1-L30】【F:backend/src/routes/auth.js†L1-L121】
- **Socket.IO** for real-time support chat, plus **Multer** + **PDFKit** for uploads and invoice generation.【F:backend/package.json†L1-L30】【F:backend/src/server.js†L1-L120】【F:backend/src/routes/invoice.js†L1-L120】
- **Nodemailer** for invoice and notification emails (demo routing noted in code).【F:backend/src/utils/email.js†L1-L106】

## Project Structure

```
.
├── backend/            # Express API, Drizzle ORM, Socket.IO server
├── frontend/           # Next.js app (App Router)
└── docs/               # Documentation and bug reports
```

## Environment Variables

### Backend (`backend/.env`)
- `DATABASE_URL` – PostgreSQL connection string.【F:backend/src/db/index.js†L1-L12】
- `JWT_SECRET` – signing secret for access/refresh tokens.【F:backend/src/middleware/auth.js†L1-L42】
- `SMTP_HOST`, `SMTP_PORT`, `SMTP_USER`, `SMTP_PASS` – email transport settings for invoices/notifications.【F:backend/src/utils/email.js†L1-L15】
- `PORT` – API port (defaults to 4000).【F:backend/src/server.js†L29-L31】

### Frontend (`frontend/.env.local`)
- `NEXT_PUBLIC_API_BASE_URL` – API base URL for the frontend (defaults to `http://localhost:4000`).【F:frontend/lib/api.js†L1-L6】

## Local Development

### Backend
```bash
cd backend
npm install
npm run dev
```
Scripts for migrations and testing are available in `backend/package.json`.【F:backend/package.json†L1-L15】

### Frontend
```bash
cd frontend
npm install
npm run dev
```
Frontend build, start, lint, and test scripts are available in `frontend/package.json`.【F:frontend/package.json†L1-L18】

## Key API Areas (Examples)

- **Auth**: `/auth/register`, `/auth/login`, `/auth/refresh` for JWT-based sessions.【F:backend/src/routes/auth.js†L60-L200】
- **Products**: `/products` search + sorting, `/products/:id` detail, and admin/manager CRUD.【F:backend/src/routes/products.js†L70-L219】
- **Orders & Returns**: `/orders` create, `/orders/returns` for staff return workflows and approvals.【F:backend/src/routes/orders.js†L69-L200】
- **Invoices**: `/invoice/:orderId` returns a PDF inline for download/viewing.【F:backend/src/routes/invoice.js†L102-L173】
- **Analytics**: `/analytics/summary?from=YYYY-MM-DD&to=YYYY-MM-DD` for sales reporting.【F:backend/src/routes/analytics.js†L1-L122】
- **Chat**: `/chat` API + Socket.IO events for support queue and messaging.【F:backend/src/routes/chat.js†L1-L200】【F:backend/src/server.js†L71-L249】

## Notes
- Uploads for product images are served from `/uploads`, while chat attachments are stored in a private directory and accessed through secure endpoints.【F:backend/src/server.js†L23-L41】【F:backend/src/routes/chat.js†L73-L118】
- Email utilities are configured for demo use and can be wired to a production SMTP account by adjusting environment variables and routing logic.【F:backend/src/utils/email.js†L1-L106】
