# StatHawk — Staging Backend & Database Setup Guide

**Version:** 1.0 | **Date:** May 2026 | **For:** First TestFlight build

This guide lists the **accounts the client must create** and the steps to deploy the StatHawk API and database so the iPad app works on TestFlight (not localhost).

**Rule:** The client owns all accounts. The dev team gets invited access only.

---

## 1. What we are setting up

| Item | Local (today) | Staging (needed) |
|------|---------------|------------------|
| API | http://localhost:3000 | https://api-staging.stathawk.app (example) |
| Database | PostgreSQL on Mac | Managed Postgres (Neon) |
| iOS app | Secrets.plist → localhost | HTTPS staging API URL |
| Auth | Clerk test keys | Clerk production keys |

---

## 2. Recommended services (client creates accounts)

| Service | Purpose | Sign up |
|---------|---------|---------|
| **Neon** | PostgreSQL database | https://neon.tech |
| **Railway** or **Render** | Host the API | https://railway.app / https://render.com |
| **Clerk** | Login (email, Apple, Google) | https://clerk.com |
| **RevenueCat** | Subscriptions | https://www.revenuecat.com |
| **Cloudflare** | R2 storage (photos/shares) | https://dash.cloudflare.com |
| **Domain DNS** | api-staging.yourdomain.com | Your registrar or Cloudflare |

*Alternative:* Client VPS + Coolify (https://coolify.io/docs) for database and API on one server.

---

## 3. Client checklist

### Step A — Database (Neon)
- [ ] Create Neon account and project
- [ ] Create database named **stathawk**
- [ ] Send **DATABASE_URL** to dev team (secure channel)

### Step B — API hosting (Railway or Render)
- [ ] Create account and connect GitHub repo **stathawk_backend**
- [ ] Add environment variables (Section 4)
- [ ] Point DNS to staging URL (e.g. api-staging.stathawk.app)
- [ ] Confirm HTTPS: **/health** returns OK

### Step C — Clerk
- [ ] Create **Production** Clerk application
- [ ] Enable sign-in: Email, Apple, Google
- [ ] Send **pk_live_...** and **sk_live_...** to dev team

### Step D — RevenueCat
- [ ] Create project for iOS **com.stathawk.app**
- [ ] Webhook: `https://api-staging.../v1/webhooks/revenuecat`
- [ ] Send webhook secret to dev team

### Step E — Invite dev team
- [ ] Add dev emails to Neon, Railway/Render, Clerk, RevenueCat

---

## 4. API environment variables

| Variable | Required | Notes |
|----------|----------|-------|
| NODE_ENV | Yes | production |
| PORT | Yes | 3000 (or host default) |
| DATABASE_URL | Yes | From Neon (not localhost) |
| CLERK_SECRET_KEY | Yes | Production secret |
| CLERK_PUBLISHABLE_KEY | Yes | Production publishable key |
| REVENUECAT_WEBHOOK_SECRET | Yes | From RevenueCat |
| R2_* (4 vars) | Later | When testing photos/shares |
| RESEND_API_KEY | Later | When testing email invites |

---

## 5. Dev team tasks

1. Run `npx prisma migrate deploy` on staging database
2. Deploy API: build → migrate → `npm run start:prod`
3. Update iOS staging: **API_BASE_URL** = staging HTTPS URL
4. Update iOS: **CLERK_PUBLISHABLE_KEY** = production key
5. Test on device: sign in → create team → create game

---

## 6. Information to send the dev team

| # | Item | Done? |
|---|------|-------|
| 1 | DATABASE_URL (Postgres) | ☐ |
| 2 | Staging API URL (https://...) | ☐ |
| 3 | Clerk pk_live + sk_live | ☐ |
| 4 | RevenueCat webhook secret | ☐ |
| 5 | DNS / domain access | ☐ |
| 6 | GitHub deploy access | ☐ |

---

## 7. Smoke test

| Test | Pass? |
|------|-------|
| https://api-staging.../health works | ☐ |
| Sign in on iPad TestFlight build | ☐ |
| Create team and game | ☐ |
| RevenueCat webhook (if billing enabled) | ☐ |

---

## 8. Helpful links

- Prisma migrations: https://www.prisma.io/docs/orm/prisma-migrate/workflows/deploy-production
- Neon: https://neon.tech/docs/introduction
- Railway: https://docs.railway.com/guides/nodejs
- Clerk: https://clerk.com/docs/deployments/overview
- RevenueCat: https://www.revenuecat.com/docs/integrations/webhooks
- Apple TestFlight: https://developer.apple.com/help/app-store-connect/test-a-beta-version/testflight-overview/

---

*StatHawk — Private document*
