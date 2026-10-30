# path: docs/plan/07-file-ownership-map.md

# KD PAY LLC - FILE OWNERSHIP MAP

Rule: a path is created and edited only by its owning step. A later step that needs a change
either uses an extension point or records an ownership transfer in docs/handoff/DECISIONS-LOG.md.

Extension points that avoid editing another step's file:

| Concern | Aggregator | Extension file pattern |
| --- | --- | --- |
| Navigation | config/nav-map.ts | config/nav/{module}.ts |
| Permissions | config/permissions/index.ts | config/permissions/{module}.ts |
| Approval chains | config/approval-rules.ts | config/approval/{domain}.ts |
| Payment processors | lib/payments/registry.ts | lib/payments/adapters/{provider}.ts |
| Email templates | lib/email/templates/index.ts | lib/email/templates/{journey}.tsx |
| Job kinds | lib/jobs/kinds.ts | lib/jobs/handlers/{kind}.ts |
| Migrations | migrations directory | new timestamped file, never edited |

---

## Step 01 - Foundation

Owns: package.json, package-lock.json, tsconfig.json, next.config.mjs, postcss.config.mjs,
tailwind.config.ts, .eslintrc.json, .prettierrc, .editorconfig, .nvmrc, .gitignore,
.env.example, middleware.ts skeleton, app/layout.tsx, app/globals.css, app/not-found.tsx,
app/error.tsx, app/global-error.tsx, app/loading.tsx, app/(public)/layout.tsx,
app/(auth)/layout.tsx, app/(dashboard)/layout.tsx, app/(admin)/layout.tsx,
app/api/health/route.ts, components/ui/**, components/layout/**, components/shared/**,
lib/core/**, lib/utils/**, config/design-tokens.ts, config/nav-map.ts, config/nav/index.ts,
scripts/**, .husky/**, .github/**, netlify.toml, vercel.json, Dockerfile, docker-compose.yml,
.dockerignore, docs/ADR/**, types/api.ts, types/result.ts, README.md.

## Step 02 - Database core

Owns: migrations/0001 to 0004 plus its own later migrations, types/database.ts,
lib/db/**, lib/db/sql/**, seeds/**, docs/ERD-part-1.md, docs/RLS-MATRIX.md, lib/jobs/kinds.ts
(kind registry only), lib/jobs/queue.ts, lib/cache/tags.ts (initial tag registry).

## Step 03 - Security core

Owns: lib/security/**, config/rate-limits.ts, config/network-policy.ts, lib/http/**,
docs/SECURITY-THREAT-MODEL.md, docs/SECURITY-HEADERS.md, scripts/verify-headers.mjs,
scripts/check-secrets.mjs, app/api/security/**. Provides middleware chain extension for Step 04.

## Step 04 - Authentication

Owns: app/(auth)/**, app/api/auth/**, lib/auth/session.ts, lib/auth/password.ts,
lib/auth/two-factor.ts, lib/auth/lockout.ts, lib/auth/step-up.ts, components/auth/**.

## Step 05 - Access control and approvals

Owns: lib/auth/roles.ts, lib/auth/permissions.ts, lib/auth/guards.ts, config/permissions/**,
config/approval-rules.ts, config/approval/**, app/(admin)/layout.tsx,
app/(admin)/page.tsx, app/(admin)/approvals/**, app/api/approvals/**,
components/admin/approval/**, services/approval/**, config/nav/admin.ts, scripts/check-nav.mjs.

## Step 06 - Storage

Owns: lib/storage/**, services/storage/**, app/api/files/**, components/files/**,
scripts/relocate-files.mjs, config/file-policy.ts.

## Step 07 - Tenant

Owns: app/(dashboard)/layout.tsx, app/(dashboard)/page.tsx, app/(dashboard)/settings/**,
app/onboarding/**, app/api/company/**, services/company/**, components/company/**,
components/settings/**, config/nav/dashboard.ts, lib/fx/**.

## Step 08 - Compliance

Owns: app/(dashboard)/kyc/**, app/(admin)/kyc/**, app/api/kyc/**, services/compliance/**,
lib/compliance/**, components/compliance/**, config/compliance-requirements.ts.

## Step 09 - Clients and portal

Owns: app/(client-portal)/layout.tsx, app/(client-portal)/hub/[token]/**,
app/(client-portal)/expired/**, app/(dashboard)/clients/**, app/api/clients/**, app/api/portal/**,
services/client/**, services/portal/**, lib/portal/**, components/client/**, components/portal/**.

## Step 10 - Documents

Owns: app/(dashboard)/invoices/**, app/(dashboard)/estimates/**, app/(dashboard)/recurring/**,
app/(client-portal)/i/[token]/**, app/(client-portal)/e/[token]/**,
app/(client-portal)/r/[token]/**, app/api/invoices/**, app/api/estimates/**, app/api/documents/**,
services/invoice/**, services/estimate/**, services/document/**, lib/pdf/**, schemas/invoice.ts,
schemas/estimate.ts, components/invoice/**, components/pdf/**, config/nav/invoicing.ts.

## Step 11 - Payments core

Owns: app/api/payments/**, app/api/refunds/**, app/api/webhooks/framework/**,
lib/payments/adapter.ts, lib/payments/registry.ts, lib/payments/routing.ts,
lib/payments/card-policy.ts, lib/payments/verification.ts, lib/payments/idempotency.ts,
lib/payments/state-machine.ts, lib/payments/fees.ts, lib/payments/settlement.ts,
services/payment/**, services/refund/**, services/ledger/**, services/webhook/**,
schemas/payment.ts, config/payment-routing.ts, config/card-policy.ts,
app/(dashboard)/payments/**, components/payment/**.

## Step 12 - Processors A

Owns: lib/payments/adapters/nmi/**, lib/payments/adapters/stripe-connect.ts,
app/api/webhooks/nmi/**, app/api/webhooks/stripe-connect/**, components/payment/nmi/**,
docs/providers/nmi.md, docs/providers/stripe-connect.md, services/provider-mode/**.

## Step 13 - Processors B

Owns: lib/payments/adapters/payoneer.ts, lib/payments/adapters/airwallex.ts,
lib/payments/adapters/rainforest.ts, app/api/webhooks/payoneer/**,
app/api/webhooks/airwallex/**, app/api/webhooks/rainforest/**, app/api/payouts/**,
services/payout/**, services/reconciliation/**, docs/providers/payoneer.md,
docs/providers/airwallex.md, docs/providers/rainforest.md, app/(dashboard)/payouts/**.

## Step 14 - Checkout and email

Owns: app/pay/**, app/api/checkout/**, app/api/payment-links/**, lib/email/**,
lib/notifications/**, lib/jobs/handlers/email-send.ts, app/api/webhooks/email/**,
app/(admin)/emails/**, app/(dashboard)/settings/email/**,
app/(dashboard)/notifications/**, components/email/**, components/checkout/**,
emails/** (React Email sources), config/nav/email.ts.

## Step 15 - Operator console

Owns: app/(admin)/processors/**, app/(admin)/tenants/**, app/(admin)/fees/**,
app/(admin)/reviews/**, app/(admin)/fraud/**, app/(admin)/disputes/**, app/(admin)/audit/**,
app/(admin)/flags/**, app/(admin)/health/**, app/(admin)/revenue/**, app/api/admin/**,
services/dispute/**, services/blacklist/**, services/audit-export/**, components/admin/**,
config/nav/operator.ts.

## Step 16 - Public site, support, deploy

Owns: app/(public)/**, app/(public)/blog/**, app/(public)/legal/**,
app/(public)/help/**, app/api/support/**, app/api/cms/**, services/content/**,
services/support/**, components/marketing/**, components/blog/**, lib/seo/**,
public/** (static assets, icons, manifest), docs/RUNBOOKS/**, docs/PERFORMANCE-REPORT.md,
docs/GO-LIVE-CHECKLIST.md, .github/workflows/deploy.yml.

---

## Files that never move

docs/plan/**, docs/handoff/**, MASTER_PROMPT.md, FEATURE_LIST.md remain readable by every step
and are edited only by a planning change that also updates DECISIONS-LOG.md.

## Conflict checklist used before every commit

1. Did I edit a file outside my ownership? If yes, stop and use an extension point.
2. Did I create a table assigned to another step? If yes, stop.
3. Did I change an earlier migration? If yes, revert and add a new migration.
4. Did I add a permission key inside config/permissions/index.ts instead of my module file?
5. Did I register an adapter outside lib/payments/adapters? If yes, stop.
6. Did I add a route without a nav entry in my module nav file?
7. Did I duplicate a service that already exists in another step? If yes, extend it instead.
