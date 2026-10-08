# 4D Results — পরবর্তী Developer/Hosting Operator-এর জন্য Handoff

## বর্তমান live site

- **Live URL:** https://fourdresults-csxoxkqk.manus.space
- **Published version:** `044e0b4adfb6103a1738b9c11ef9446dff94e39f`
- **Repository branch:** `main`
- **Stack:** React + Vite + Tailwind + Express + tRPC + Drizzle ORM + MySQL-compatible database
- **Production health endpoint:** `/api/health`

## এই ZIP-এ যা আছে

- Frontend source: `client/`
- Backend/API source: `server/`
- Database schema/migrations: `drizzle/`
- Shared configuration/types: `shared/`
- Android wrapper configuration: `android-wrapper/`
- `Dockerfile`, `package.json`, `pnpm-lock.yaml`
- `PROJECT_HANDOFF.md`
- `OWNER_SECURITY_CHECKLIST.md`
- `STATUS_REPORT_BN.md`
- এই handoff guide এবং `.env.example`

`node_modules`, `dist`, `.git`, runtime logs, local session data, `.env` এবং secret values ইচ্ছাকৃতভাবে ZIP-এ নেই।

## Local setup

```bash
pnpm install --frozen-lockfile
pnpm check
pnpm test -- --run
pnpm build
pnpm dev --host 0.0.0.0
```

## Production deployment requirements

1. Node.js 22 এবং pnpm ব্যবহার করুন।
2. `DATABASE_URL` দিয়ে managed MySQL-compatible database যুক্ত করুন।
3. Server-side secret manager-এ `.env.example`-এর required values দিন।
4. `pnpm db:push` চালিয়ে schema/migrations apply করুন।
5. Build command: `pnpm install --frozen-lockfile && pnpm build`
6. Production command: `pnpm start`
7. Container health check: `/api/health`
8. Application port: `PORT` environment value, default `3000`

## Required secret names — values ZIP-এ নেই

- `DATABASE_URL`
- `JWT_SECRET`
- `OWNER_EMAIL`
- `OWNER_PROVIDER=google`
- `OWNER_OPEN_ID` (প্রয়োজন হলে)
- `OWNER_MFA_REQUIRED=true`
- `OWNER_MFA_SECRET`
- Storage/OAuth/notification provider values, যদি deployment platform চায়

কোনো password, MFA secret, JWT secret, OAuth secret, API key, payment gateway secret বা Android keystore এই ZIP-এ যোগ করবেন না। এগুলো chat, Git বা public folder-এ পাঠাবেন না।

## Custom domain সংযোগের কাজ

বর্তমান default Manus domain live আছে। Custom domain যুক্ত করার সময়:

1. Hosting dashboard-এ domain add করুন।
2. Dashboard যে exact A/CNAME/TXT records দেবে, DNS provider-এ বসান।
3. পুরোনো conflicting `@`/`www` records সরান; MX/SPF/DKIM/DMARC অকারণে মুছবেন না।
4. DNS verification শেষ করুন।
5. SSL/HTTPS active হওয়া যাচাই করুন।
6. Root এবং `www`-এর একটি primary URL ঠিক করুন।
7. OAuth callback, payment return/webhook ও analytics domain নতুন domain অনুযায়ী update করুন।

কোনো IP বা CNAME value অনুমান করে ব্যবহার করবেন না; hosting dashboard-এর exact value ব্যবহার করুন।

## ইতিমধ্যে সচল feature

- Public landing page এবং responsive UI
- Malaysia/Singapore game coverage cards
- Free daily prediction feed এবং public posts
- VIP deposit submission ও admin review
- Payment screenshot upload
- Scheduled/pinned daily posts
- Media upload
- KYC submission ও admin review
- Member portal, profile, social, groups, messages, notifications, support ও wallet history
- Public Owner profile route
- Admin command center, roles, audit/security controls

## ইচ্ছাকৃত security/business lock — bypass করা যাবে না

- Owner identity replacement/transfer/delete/downgrade
- Owner MFA verification
- Ordinary admin থেকে Owner/Super Admin privilege escalation
- Private member data, payment history, wallet/membership records
- Gambling, winnings বা prize withdrawal
- Deposit approval-এর আগে VIP entitlement

এগুলো feature failure নয়; account security, privacy, compliance এবং business rule।

## এখনো production configuration/implementation দরকার

- Official live results provider/feed
- Real payment gateway/webhook
- Approved VIP entitlement duration, renewal, refund/chargeback rules
- Provider-specific 2FA challenge delivery
- Final KYC document storage policy
- Legal Terms, Privacy, age/responsible-use text
- Android SDK/Gradle দিয়ে signed APK build
- Analytics provider values
- Rate limiting, CSRF এবং final jurisdiction-specific compliance review

## গ্রহণ করার পর অবশ্যই যাচাই করুন

```bash
pnpm check
pnpm test -- --run
pnpm build
```

তারপর:

- `https://<custom-domain>/api/health` → HTTP 200
- Homepage ও mobile layout
- OAuth login/logout
- Anonymous user-এর private API denial
- Admin/MFA boundary
- Deposit review এবং KYC review
- Custom domain HTTPS ও OAuth callback

## গুরুত্বপূর্ণ সতর্কতা

এই ZIP কেবল trusted developer/hosting operator-কে দিন। Public file-sharing link, public Git repository বা website `client/public/` folder-এ source ZIP আপলোড করবেন না।
