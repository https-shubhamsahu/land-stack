# LandStack

LandStack is a modern digital land-governance and property information platform designed to make land records, property services, and administrative workflows more accessible, transparent, and structured.

## Deploy to Vercel

The frontend and Express API are deployed together. The frontend calls `/api`, so it always reaches the API function on the same Vercel deployment.

Before deploying, add these environment variables under **Vercel → Project Settings → Environment Variables** for Production (and Preview if you use previews):

- `DATABASE_URL` — a reachable PostgreSQL connection string. Include `?sslmode=require` when your provider requires SSL.
- `JWT_SECRET` — a long random secret, for example from `openssl rand -base64 32`.
- `FRONTEND_URL` — the production site URL, such as `https://your-domain.example`, when using a custom domain or a separately hosted frontend. Vercel's deployment URL is allowed automatically.

The repository config runs `npm run vercel-build`, which applies pending Prisma migrations before building the Vite app. Do not set `VITE_API_BASE_URL` in Vercel unless the API is intentionally hosted on a different domain; the default `/api` is the correct value for this repository.

For local development, keep `VITE_API_BASE_URL=http://localhost:5000/api` in `.env.development` and put the database and JWT values in an untracked `.env` file based on `.env.example`.

The platform follows a GIS and Digital Public Infrastructure–inspired approach, connecting citizens, land/property records, service requests, and government-style workflows through a unified web application.

## 🚀 Features

### 👤 Citizen Portal
- Citizen registration and secure login
- JWT-based authentication
- Personal citizen dashboard
- Submit and track service requests
- View request status and updates
- Access land and property information
- Explore parcels through the GIS-based Land Explorer
- Notifications for important updates

### 🏛️ Officer Portal
- Secure officer authentication
- Officer dashboard
- Service request management
- Search and review land/property records
- ULPIN-based parcel lookup
- Verify and approve requests
- Request additional information
- Flag requests for further review
- Update request statuses
- Officer notifications

### 🗺️ Land Explorer
- GIS-inspired land/property exploration
- Search by location
- Search by land record
- Find parcels on map
- View citizen's properties
- Parcel details and ownership information
- Authentication-protected access

### 🔐 Security
- JWT authentication
- Password hashing with bcrypt
- Role-based access control
- Protected API routes
- Citizen/Officer access separation
- Backend authorization for property and service data
- Environment-based secrets and configuration

### 🔔 Notifications
- Unread notification count
- Mark individual notifications as read
- Mark all notifications as read
- Request status notifications

### 🤖 AI Alerts
- Land/property related alert interface
- Alert review workflow
- Alert summaries
- Administrative review support

### 🔗 Connected Systems
LandStack includes a prototype interface for connected government-style systems and services. These integrations are represented as a sandbox/prototype layer and do not represent live government integrations.

## 🏗️ Tech Stack

### Frontend
- React
- Vite
- JavaScript
- REST API integration
- Responsive web UI

### Backend
- Node.js
- Express.js
- REST APIs
- JWT
- bcryptjs
- Helmet
- CORS
- Morgan

### Database
- PostgreSQL
- Prisma ORM

## 🧩 Architecture

```text
Citizen / Officer
       │
       ▼
React + Vite Frontend
       │
       ▼
REST API
       │
       ▼
Node.js + Express
       │
       ▼
Prisma ORM
       │
       ▼
PostgreSQL

