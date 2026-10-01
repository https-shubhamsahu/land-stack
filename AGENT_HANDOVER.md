# LandStack deployment handover

## Goal

Deploy this React, Express, Prisma, and PostgreSQL application as one Vercel project. The React app and Express API must share one public origin, with API requests served at `/api/*`.

## Repository state

- GitHub repository: `https://github.com/https-shubhamsahu/land-stack`
- Branch: `main`
- Latest deployment-fix commit before this handover: `81e4833` (`Fix Vercel API deployment configuration`)
- The original repository remote is read-only for the signed-in GitHub account. Use the `personal` remote or the GitHub URL above for all future pushes.

## Changes already made

The deployment fix is in the repository.

- `.env` was renamed to `.env.development`. This prevents Vite production builds from embedding `http://localhost:5000/api`.
- In production, `src/api.js` defaults to `/api`; local development still uses `http://localhost:5000/api` through `.env.development`.
- `vercel.json` routes `/api/:path*` to the Express function before the React SPA fallback and runs `npm run vercel-build`.
- `npm run vercel-build` runs `prisma migrate deploy` before `vite build`.
- The API accepts its Vercel deployment origin for CORS through `VERCEL_URL`; use `FRONTEND_URL` for a custom domain.
- `.env.example` documents required values without committing any secrets.

## Validation already completed

- `npm run build` passed.
- `npm run lint` completed with pre-existing warnings only.
- The Vercel function entry point responded successfully to `GET /api/health`.
- The API returned the expected CORS header for a Vercel-style origin.
- `prisma validate` passed when given a syntactically valid `DATABASE_URL`.

## Current blockers

### Database

The linked Vercel project has no environment variables. It needs a reachable PostgreSQL database before `npm run vercel-build` can apply migrations.

An attempt to create a new Supabase project failed because the Supabase account has reached its two-active-free-project limit. Do not pause, delete, or repurpose any existing Supabase project without the user's explicit choice.

Choose one of these paths:

1. The user pauses or upgrades an existing Supabase project, then create a new `land-stack` project in `ap-south-1`.
2. The user authorizes use of a named existing Supabase PostgreSQL project.
3. Use another managed PostgreSQL provider, such as Neon, and obtain its connection string.

### Vercel

The local workspace is linked to a Vercel project named `land-stack`, but no variables are configured. Its Git connection may still point at the original repository; reconnect it to `https-shubhamsahu/land-stack` in the Vercel dashboard if automatic Git deployments are required.

## Required Vercel environment variables

Add these in Vercel Project Settings for **Production** and **Preview**:

| Name | Value |
| --- | --- |
| `DATABASE_URL` | PostgreSQL connection string for the selected managed database. Use the provider's serverless/pooler connection string when available. |
| `JWT_SECRET` | A new, high-entropy secret. Never commit it or place it in a `VITE_` variable. |
| `FRONTEND_URL` | Required for a custom domain or separately hosted frontend, for example `https://landstack.example`. |

Vercel provides `VERCEL_URL` to the runtime; it handles the default `*.vercel.app` origin automatically.

## Remaining deployment steps

1. Provision the PostgreSQL database using one of the approved paths above.
2. Add `DATABASE_URL` and a generated `JWT_SECRET` to Vercel. Add `FRONTEND_URL` if using a custom domain.
3. Ensure the Vercel project is connected to `https://github.com/https-shubhamsahu/land-stack`, or deploy the workspace directly with `npx vercel --prod --yes`.
4. Allow Vercel to run `npm run vercel-build`; it will apply `backend/prisma/migrations` then build `dist`.
5. Verify `https://<deployment-domain>/api/health` returns HTTP 200 and `{"success":true,"message":"LandStack backend is running"}`.
6. Register a user and check login, parcel search, requests, and notifications against the live site.
7. If demo data is needed, run the existing Prisma seed script only after confirming it is safe for the selected production database. Do not seed repeatedly on every deployment.

## Important constraints

- Never commit `.env`, database credentials, or JWT secrets.
- Do not change `VITE_API_BASE_URL` in Vercel for the single-deployment setup; `/api` is intentional.
- Do not remove the API rewrite before the SPA fallback in `vercel.json`.
- Do not create, pause, delete, or reuse a Supabase project without explicit user authorization.
- The project requires PostgreSQL; SQLite is not a drop-in deployment substitute.

## Copy-ready Antigravity prompt

```text
Continue the LandStack Vercel deployment using the repository at https://github.com/https-shubhamsahu/land-stack on main. Read AGENT_HANDOVER.md first and treat it as the current source of truth.

The React frontend, Express API, Prisma schema, migration, and Vercel routing are already fixed and validated. Do not undo the `/api` production routing or reintroduce a committed localhost VITE_API_BASE_URL.

First inspect the linked Vercel project and confirm whether it is connected to https-shubhamsahu/land-stack. Provision or connect a PostgreSQL database only after receiving explicit authorization for the specific provider/project. The Supabase account currently cannot create another free active project, so do not pause, delete, or reuse an existing Supabase project without the user's explicit choice.

When a database is available, add DATABASE_URL and a newly generated JWT_SECRET to Vercel Production and Preview environment variables. Add FRONTEND_URL if a custom domain is used. Deploy with the repository's existing npm run vercel-build command, which applies Prisma migrations and builds Vite.

Verify the final deployment by requesting /api/health and then exercising user registration/login and a database-backed feature. Never expose or commit secrets. Report the live deployment URL, the health-check result, and any remaining blocker.
```
