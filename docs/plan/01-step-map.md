# path: docs/plan/01-step-map.md

# KD PAY LLC - 16 STEP MAP (ONE STEP PER CHAT)

Each numbered step is implemented in exactly one chat session.
A step is split into sub-steps (NN.1, NN.2, ...) that are implemented in order inside that
chat. If a single response cannot carry the whole step, the same chat continues with
"Continue STEP NN from sub-step NN.x" and the identical rule set applies.

Independent work in different steps is zero: every step consumes the output of the previous
step. This is what prevents conflicts and duplicates.

---

## Overview

| Step | Title | Sub-steps | Primary output |
| --- | --- | --- | --- |
| 01 | Foundation, toolchain, design system base, guards | 6 | Buildable Next.js strict shell with CI guards |
| 02 | Database core: tenancy, RLS, audit, idempotency, queue | 8 | Migration set plus SQL helpers |
| 03 | Security core: sanitize, headers, CSP, rate limit, network trust, encryption | 8 | Central security library |
| 04 | Authentication: signup, login lockout, 2FA, sessions, step-up | 7 | Working auth with lock policy |
| 05 | RBAC, permissions, approval chain engine, admin shell | 6 | Permission matrix plus approval inbox |
| 06 | Storage and files with AES-256-GCM | 7 | Encrypted file service and relocation tool |
| 07 | Tenant onboarding, company profile, branding, settings | 6 | Owner workspace ready |
| 08 | KYC and KYB, screening, risk tiers, limits | 6 | Compliance pipeline |
| 09 | Clients CRM and tokenized client portal | 6 | Client records and HMAC portal links |
| 10 | Invoicing core, numbering, PDF engine, sales tax on invoice | 8 | Full document system |
| 11 | Payments core engine: state machine, routing, fees, settlement, card policy | 8 | Provider agnostic payment engine |
| 12 | Processors A: NMI dual mode, Stripe Connect | 7 | Two live adapters and mode switching |
| 13 | Processors B: Payoneer, Airwallex, Rainforest, payouts, reconciliation | 6 | All five adapters registered |
| 14 | Checkout, payment links, email system, templates, activity emails | 7 | Collection journey complete |
| 15 | Super admin console: vault, mode, fee and hold, fraud, disputes, audit | 8 | Operator control plane |
| 16 | Public site, blog, support, SEO, performance, deploy, go-live | 9 | Production launch |

Global sequence rules:

- Steps run 01 to 16 in order. No step starts before the previous handoff exists.
- Migration timestamps are generated at creation time, which is always later than the previous
  step, so ordering is guaranteed by sequence, not by guesswork.
- A file is edited only by its owning step (docs/plan/07-file-ownership-map.md). Later steps
  extend behaviour through their own module files and registry entries.

---

## STEP 01 - Foundation, Toolchain, Design System Base, Guards

Goal: a repository that builds, lints, type checks and blocks every prohibited pattern before
any business feature exists.

Sub-steps:

- 01.1 Runtime and dependencies
  - package.json with pinned Next.js 14 App Router, React 18, TypeScript 5 strict,
    Tailwind CSS 3, zod, decimal.js, @supabase/supabase-js, @supabase/ssr, react-hook-form,
    @hookform/resolvers, lucide-react, clsx, tailwind-merge, class-variance-authority,
    sonner, @sentry/nextjs, pino, isomorphic-dompurify, framer-motion, date-fns,
    @tanstack/react-query, zustand
  - tsconfig.json strict flags from charter R6, path alias @/*
  - next.config.mjs with server actions body limit, image domains, typed routes
  - tailwind.config.ts with design tokens and content globs
  - postcss.config.mjs, .eslintrc.json, .prettierrc, .editorconfig, .nvmrc, .gitignore
- 01.2 Core contracts
  - types/result.ts, types/api.ts, lib/core/errors.ts (typed error classes and error codes)
  - lib/core/logger.ts (pino, redaction list, correlation field, pretty in dev only)
  - lib/core/http.ts (success and error envelope, correlation id issuance, no raw error leak)
  - lib/core/env.ts (zod validated environment, fail fast, split server and public)
  - lib/core/money.ts (Decimal helpers, USD constant, format, parse, round half up to 4dp)
  - lib/core/datetime.ts (UTC helpers, business day math)
  - lib/core/id.ts (uuid v7 generator for pre-insert ids)
  - lib/core/strings.ts (slug, safe filename, masking)
- 01.3 Design system base
  - app/layout.tsx, app/globals.css, font loading with subsetting and display swap
  - components/ui primitives: button, input, textarea, select, checkbox, label, card, badge,
    dialog, sheet, drawer, table, tabs, tooltip, dropdown-menu, skeleton, alert, toast,
    empty-state, error-state, pagination, data-table shell, form-field wrapper
  - components/layout: sidebar, topbar, mobile-nav, breadcrumbs, command-menu shell,
    page-header, section, container
  - providers: theme provider with dark mode, query client provider, toast provider
  - config/design-tokens.ts, config/nav-map.ts plus config/nav/index.ts aggregation pattern
- 01.4 App shell
  - app/layout.tsx root metadata, viewport, theme script
  - app/not-found.tsx, app/error.tsx, app/global-error.tsx, app/loading.tsx
  - app/(public)/layout.tsx shell only, marketing pages belong to Step 16
  - app/(auth)/layout.tsx shell only, auth pages belong to Step 04
  - app/(dashboard)/layout.tsx, app/(admin)/layout.tsx guarded shells reserved for later steps
  - app/api/health/route.ts returning build info and dependency flags without secrets
- 01.5 Guard scripts and hooks
  - scripts/check-english-only.mjs (fail on any byte above 0x7F in tracked text files)
  - scripts/check-no-tests.mjs, scripts/check-no-markers.mjs, scripts/check-no-any.mjs,
    scripts/check-console.mjs, scripts/check-path-comment.mjs, scripts/run-guards.mjs
  - package.json scripts: dev, build, start, lint, typecheck, guard, verify
  - .husky/pre-commit running lint staged files, typecheck, guard
  - .github/workflows/ci.yml: install, guard, typecheck, lint, build, secret scan
- 01.6 Ops configuration and documentation seeds
  - netlify.toml, vercel.json, Dockerfile, docker-compose.yml, .dockerignore
  - .env.example with every variable grouped and commented
  - docs/ADR/0001-stack-and-boundaries.md, docs/handoff/STEP-01-HANDOFF.md
  - README section pointing to docs/plan

Tables: none.
Env: NEXT_PUBLIC_APP_URL, NEXT_PUBLIC_SUPABASE_URL, NEXT_PUBLIC_SUPABASE_ANON_KEY,
SUPABASE_SERVICE_ROLE_KEY, LOG_LEVEL, SENTRY_DSN, ENCRYPTION_MASTER_KEY (declared, used Step 03).
Owned paths: package.json, tsconfig.json, next.config.mjs, tailwind.config.ts, app root files,
components/ui, components/layout, lib/core, scripts, config/design-tokens.ts, config/nav-map.ts,
config/nav/index.ts, .github, .husky, netlify.toml, vercel.json, Dockerfile, docs/ADR, README.md.
Exit criteria: npm run verify passes; guards block a seeded non-ASCII string, a test file, a
marker and an any; health route returns 200; dark mode and mobile 320 px verified.

---

## STEP 02 - Database Core: Tenancy, RLS, Audit, Idempotency, Queue, Settings

Goal: the data foundation every later step builds on, with tenancy and audit enforced in SQL.

Sub-steps:

- 02.1 Migrations framework
  - migrations/0001_extensions.sql and _down.sql: pgcrypto, citext, pg_trgm, btree_gin
  - migrations/0002_uuid_v7.sql: uuid_generate_v7() implemented in SQL, immutable wrapper
  - migrations/0003_updated_at.sql: set_updated_at() trigger function
  - migrations/0004_soft_delete_guard.sql: prevent hard delete on protected tables
  - Convention: migrations/{YYYYMMDDHHMMSS}_{step}_{description}.sql plus _down.sql
- 02.2 Tenancy tables
  - companies (id, name, slug, legal_name, country_code, base_currency fixed USD, status,
    platform company flag, settings jsonb, version)
  - users (id mirrors auth.users.id, email citext unique, full_name, phone, status,
    last_login_at, version)
  - memberships (user_id, company_id, role, sub_role, status, unique active pair)
  - company_invites excluded: owners cannot invite staff
  - role enum: super_admin, ceo, admin_staff, owner; admin_staff_sub_role enum:
    compliance_kyc, support, content
- 02.3 Audit core
  - audit_logs (id, company_id nullable, actor_user_id, actor_role, action, resource_type,
    resource_id, before jsonb, after jsonb, ip inet, user_agent, device_fingerprint,
    correlation_id, sequence bigint identity, previous_hash, current_hash, created_at,
    updated_at, deleted_at)
  - audit_chain SQL function, append only triggers, revoke update and delete grants
  - fn_audit_row() generic trigger attached to audited tables by a registration table
- 02.4 Idempotency, rate limit and locking helpers
  - idempotency_keys (scope, key, request_hash, status, response jsonb, expires_at) with
    unique partial index and 24 hour TTL cleanup job
  - rate_limit_buckets (bucket_key, window_start, counter) plus fn_rate_limit_consume()
    returning allowed, remaining, retry_after
  - fn_optimistic_update() helper pattern, version columns on editable tables
- 02.5 Job queue and scheduling
  - jobs (queue, kind, payload jsonb, run_at, attempts, max_attempts, locked_by, locked_at,
    status, last_error), job_attempts, job_dead_letter
  - fn_job_claim(batch int) using FOR UPDATE SKIP LOCKED
  - pg_cron registration table and cron_runs with leader lock function
- 02.6 Settings, flags and reference data
  - platform_settings (key, value jsonb, description, updated_by) seeded with defaults:
    platform_fee_percent, settlement_hold_days, velocity defaults, lock policy constants
  - feature_flags (key, enabled, scope)
  - countries (read only reference), payout_method_catalog (generic labels only),
    currency_display_rates (USD base, frozen snapshot table)
  - tenant_settings (company_id, key, value jsonb, version)
- 02.7 RLS bootstrap
  - fn_current_user_id(), fn_current_company_id(), fn_is_platform_staff(),
    fn_has_role(text[]), fn_has_sub_role(text) as STABLE security definer functions
  - enable_rls_default_deny(table) helper and per table policies:
    tenant isolation for owner scope, platform scope for admin_staff and up,
    service role bypass only through the server admin client
  - policy naming convention: pol_{table}_{scope}_{action}
- 02.8 Type generation and seeds
  - types/database.ts generated output committed, with a documented regeneration command
  - seeds/0001_platform.sql (idempotent platform company, platform settings defaults,
    countries, payout catalog, feature flags), seeds/0002_dev_tenants.sql template only
  - docs/ERD-part-1.md, docs/RLS-MATRIX.md
  - docs/handoff/STEP-02-HANDOFF.md

Tables: companies, users, memberships, audit_logs, idempotency_keys, rate_limit_buckets, jobs,
job_attempts, job_dead_letter, cron_runs, platform_settings, feature_flags, tenant_settings,
countries, payout_method_catalog, currency_display_rates.
Exit criteria: migrations apply cleanly forward and backward on an empty database; RLS blocks
cross tenant select, insert, update and delete; audit trigger captures before and after; rate
limit function returns deterministic windows; job claim is concurrency safe.

---

## STEP 03 - Security Core: Sanitizing, Headers, CSP, Rate Limit, Network Trust, Encryption

Goal: one security library that every later step imports, so no step invents its own rules.

Sub-steps:

- 03.1 Input sanitizing
  - lib/security/sanitize.ts: NFC normalization, zero width and RTL override strip, null byte
    reject, control character strip, length clamp, deep object sanitizing
  - lib/security/rich-text.ts: server side DOMPurify profile for CMS content and email HTML
  - lib/security/url.ts: SSRF allowlist, internal IP and metadata endpoint block, redirect
    allowlist, protocol allowlist
  - lib/security/headers-guard.ts: CRLF strip for header values
- 03.2 Transport and browser hardening
  - middleware.ts chain: correlation id, security headers, CSP nonce issuance, CSRF origin
    check, body size limit, method allowlist
  - lib/security/csp.ts: nonce based policy, no unsafe-inline, connect-src limited to Supabase
    and configured processor origins, frame-ancestors none, upgrade-insecure-requests
  - lib/security/headers.ts: HSTS, X-Content-Type-Options, Referrer-Policy,
    Permissions-Policy, Cross-Origin-Opener-Policy, Cross-Origin-Resource-Policy
  - lib/security/csrf.ts: double submit cookie plus origin and referer validation
- 03.3 Rate limiting and client identity
  - lib/security/rate-limit.ts wrapping fn_rate_limit_consume with named policies in
    config/rate-limits.ts (signup, login, password reset, payment intent, webhook, api)
  - lib/security/client-ip.ts: trusted proxy header resolution for Netlify, Vercel and
    Cloudflare, never trusting forwarded headers from untrusted sources
- 03.4 Network trust and bot defense
  - lib/security/turnstile.ts: server verification with hostname and action checks
  - lib/security/network-trust.ts: IP reputation vendor adapter, VPN, proxy, Tor, datacenter
    and residential proxy classification, cached in ip_reputation_cache with TTL
  - lib/security/disposable-email.ts (bundled list, normalized compare)
  - lib/security/bot-signals.ts: client hints plus server heuristics, headless detection
  - policy map config/network-policy.ts: block on signup, payment, KYC upload, admin; flag on
    login with 2FA requirement
- 03.5 Encryption
  - lib/security/encryption.ts: AES-256-GCM with envelope {v, kid, iv, tag, ciphertext},
    AAD binding company id and field name, HKDF per tenant derivation
  - lib/security/blind-index.ts: HMAC-SHA256 normalized searchable index for encrypted fields
  - lib/security/key-registry.ts: key ids from environment, rotation aware decrypt
  - lib/security/redaction.ts: PII scrubber used by logger and Sentry beforeSend
- 03.6 Audit writer and zero tolerance engine
  - lib/security/audit.ts: single writer, correlation propagation, actor context resolution
  - lib/security/zero-tolerance.ts: strike model, automatic ban thresholds, notification hook
  - lib/security/correlation.ts: AsyncLocalStorage context
- 03.7 Secret hygiene
  - lib/supabase/admin.ts marked server only, guarded against client import
  - scripts/check-secrets.mjs, gitleaks action in CI
  - environment schema extended with ENCRYPTION_MASTER_KEY, ENCRYPTION_KEY_ID,
    IP_REPUTATION_API_KEY, TURNSTILE_SECRET_KEY
- 03.8 Threat model and verification
  - docs/SECURITY-THREAT-MODEL.md, docs/SECURITY-HEADERS.md
  - scripts/verify-headers.mjs (assert response headers and CSP on a running build)
  - docs/handoff/STEP-03-HANDOFF.md

Tables: ip_reputation_cache, security_strikes, security_events.
Exit criteria: CSP nonce present and no unsafe-inline; cross site POST blocked; rate limit
policy verified; encryption round trip plus tamper detection; redaction removes email, phone,
card-like digits and tokens from logs.

---

## STEP 04 - Authentication, Lockout Policy, 2FA, Sessions, Step-Up

Goal: complete authentication with the exact lock policy from charter section 12.

Sub-steps:

- 04.1 Supabase auth plumbing
  - lib/supabase/client.ts, lib/supabase/server.ts, lib/supabase/middleware.ts session refresh
  - middleware route protection matrix: public, auth-only, dashboard, admin, client portal
  - app/(auth)/layout.tsx with brand panel and accessible forms
- 04.2 Signup
  - email and password with policy from 04.6, Turnstile, honeypot field, VPN and proxy block,
    disposable email block, generic messages, email verification flow
  - creates users row and a pending membership record only after verification
  - signup does not create a company: onboarding is Step 07
- 04.3 Login and lockout
  - login_attempts (user identifier hash, ip, outcome, reason, created_at), account_locks
  - engine: two consecutive failures on password or on 2FA code lock for 2 hours; failures
    reset only on success
  - locked account: correct password denied, reset denied, password change does not unlock
  - email alert to the account owner at lock start using the queued email job contract
  - IP rate limit plus per identifier limit, constant time responses, no account enumeration
  - login page surfaces only "invalid credentials" and "account temporarily locked"
- 04.4 Two factor authentication
  - TOTP enrolment with QR, secret encrypted through lib/security/encryption.ts
  - ten backup codes, hashed with HMAC, single use, regeneration flow
  - enforcement matrix: super_admin, ceo, admin_staff mandatory; owner mandatory at first
    payment configuration
  - wrong 2FA code counts toward lockout, recovery flow creates an approval request
- 04.5 Sessions, devices and step-up
  - user_sessions (session id, user id, device fingerprint, ip, user agent, last_seen,
    revoked_at), revoke one or all
  - idle timeout and absolute timeout from platform settings
  - step_up_tokens (5 minute validity, scoped action, single use)
  - super_admin impersonation with banner, time limit and audit
- 04.6 Password lifecycle
  - config/password-policy.ts, HIBP k-anonymity check, password_history last five
  - forgot, reset, change flows, all blocked while locked
  - security notification email on every change
- 04.7 Operator unlock
  - permission gated unlock screen: requires email contact confirmation checkbox and note
  - writes to account_locks and audit with actor, reason and correlation id
  - docs/handoff/STEP-04-HANDOFF.md

Tables: login_attempts, account_locks, user_sessions, totp_secrets, backup_codes,
password_history, step_up_tokens, user_devices.
Routes: /login, /signup, /verify-email, /2fa, /forgot-password, /reset-password, /locked,
/api/auth/*, /api/auth/unlock.
Exit criteria: two wrong passwords lock for two hours and survive a restart; correct password
during lock is refused; reset refused during lock; unlock requires the confirmation note and is
audited; 2FA wrong code increments the same counter; no enumeration.

---

## STEP 05 - RBAC, Permissions, Approval Chain Engine, Admin Shell

Goal: single source of truth for who may do what, and the approval machinery used by every
sensitive action afterwards.

Sub-steps:

- 05.1 Permission catalog
  - config/permissions/{module}.ts files, aggregated in config/permissions/index.ts
  - permission naming: resource.action, for example invoice.create, processor.enable,
    kyc.review, dispute.submit, tenant.suspend, audit.export
  - role to permission matrix in config/permissions/matrix.ts
  - lib/auth/guards.ts: requireUser, requireRole, requirePermission, requireCompany,
    requireStepUp, each returning a typed result and throwing typed errors
- 05.2 Platform staff model
  - admin_staff sub roles wired to permission bundles: compliance_kyc, support, content
  - least privilege defaults, explicit grant table for elevated actions
- 05.3 Approval engine
  - approval_requests, approval_steps, approval_delegations, approval_comments
  - config/approval-rules.ts: action to chain mapping with dual approval list and thresholds
  - engine: create, advance, reject, escalate on SLA, delegate, bulk decide, self approval
    blocked, idempotent decisions, optimistic locking on each step
  - every transition audited, every request renders a human readable diff
- 05.4 Approval interface
  - app/(admin)/approvals list with filters and SLA badges, detail drawer with diff, approve
    and reject with mandatory reason, bulk approve with count confirmation
  - realtime subscription limited to the approval inbox only
- 05.5 Admin shell
  - app/(admin)/layout.tsx guarded by role and step-up for vault routes
  - admin dashboard with real aggregate queries through one PostgreSQL function per widget
  - nav from config/nav/admin.ts, breadcrumbs derived from nav-map
- 05.6 Navigation integrity
  - scripts/check-nav.mjs comparing filesystem routes with nav-map entries
  - command palette filtered by permission, keyboard shortcut reference page
  - docs/handoff/STEP-05-HANDOFF.md

Tables: approval_requests, approval_steps, approval_delegations, approval_comments.
Routes: /admin, /admin/approvals, /admin/approvals/[id], /api/approvals/*.
Exit criteria: every guarded route returns 403 or 404 correctly; self approval impossible;
escalation fires from cron; bulk approve requires count confirmation; nav guard passes.

---

## STEP 06 - Storage and Files with AES-256-GCM

Goal: encrypted file handling with provider abstraction, ready for KYC documents, logos,
invoice PDFs and dispute evidence.

Sub-steps:

- 06.1 Provider abstraction
  - lib/storage/provider.ts interface: put, get, delete, exists, signUrl, move, list
  - lib/storage/adapters/supabase-storage.ts (primary)
  - lib/storage/adapters/drive.ts included only if decision D1 resolves to optional Drive
  - lib/storage/resolver.ts: company_storage_config with platform default fallback
- 06.2 Encryption pipeline
  - streaming AES-256-GCM, chunked for large PDFs, header carries key id and nonce
  - per company key derivation, content hash before and after encryption
  - fail closed when no key or no provider
- 06.3 Upload security
  - magic byte verification, MIME whitelist per category, size limits per category
  - image re-encode and EXIF strip, SVG sanitize, PDF JavaScript removal, archive bomb guard
  - malware scan hook, quarantine bucket, duplicate detection by content hash
  - filename sanitizing, per user and per tenant upload rate limits, quota
- 06.4 File model
  - files, file_versions, file_access_logs, storage_migrations
  - columns include storage_provider, provider_key, sha256, key_id, byte_size, mime,
    category, visibility, retention_until, company_id, version
- 06.5 Serve and download
  - app/api/files/[id]/route.ts: permission check, short lived signed URL, watermark for
    exports, download rate limit, access log, no directory listing
- 06.6 Relocation tool
  - scripts/relocate-files.mjs with dry run, batch verification by hash, resumable,
    audit per file, report output
- 06.7 Category wiring
  - categories: kyc_document, business_document, logo, invoice_pdf, receipt_pdf,
    dispute_evidence, attachment, export
  - docs/handoff/STEP-06-HANDOFF.md

Tables: files, file_versions, file_access_logs, storage_migrations, company_storage_config.
Exit criteria: upload produces only ciphertext in the bucket; download requires permission and
logs access; duplicate upload returns the existing file; relocation completes with hash parity.

---

## STEP 07 - Tenant Onboarding, Company Profile, Branding, Settings

Goal: an owner can create a company, finish onboarding and configure the workspace.

Sub-steps:

- 07.1 Company creation
  - server action creating company, membership and default settings in one transaction with
    advisory lock on slug, audit entry, optimistic lock
- 07.2 Onboarding wizard
  - steps: business identity, address and country, brand, payout profile, KYC handoff,
    first invoice pointer
  - resumable progress stored on company_onboarding, one query per step, no layout shift
- 07.3 Company profile and settings
  - business name, legal name, registration number, tax identifier for display, address,
    contact, timezone, invoice footer note, default payment terms
  - settings pages with server actions, Zod, sanitize, audit, version conflict handling UI
- 07.4 Branding
  - logo upload through Step 06 service, color tokens with contrast validation, invoice
    prefix and numbering pattern, email sender name, portal vanity path
  - live preview component, per tenant CSS variables resolved on the server
- 07.5 Display currency and FX
  - currency_display_rates snapshot, read only display conversion, frozen rate at invoice
    creation, cache with tag invalidation
- 07.6 Tenant settings service and cache
  - services/company/* with a cached tenant config loader (request memoized plus tag cache)
  - docs/handoff/STEP-07-HANDOFF.md

Tables: company_profiles, company_branding, company_settings, company_onboarding,
company_payout_profiles, currency_display_rates (extended), fx_snapshots.
Routes: /onboarding/*, /dashboard, /dashboard/settings/*.
Exit criteria: onboarding completes with one round trip per step; settings changes are audited
and version safe; branding renders on invoice preview and portal; cache invalidates on write.

---

## STEP 08 - KYC and KYB, Screening, Risk Tiers, Limits

Goal: a tenant cannot collect money until compliance approves it.

Sub-steps:

- 08.1 Case model
  - kyc_cases, kyc_requirements (by business type and country), kyc_documents, kyc_reviews
  - one active case per company enforced by partial unique index
- 08.2 Owner submission
  - multi step submission with encrypted document upload, field validation per country,
    draft save, submit for review, status timeline, resubmission after request for information
- 08.3 Review console
  - queue for compliance_kyc with checklist, document viewer with signed URLs, approve, reject,
    request information, hold reasons, SLA timer
  - approval chain integration for override and tier elevation
- 08.4 Screening
  - sanctions, PEP and adverse media adapter with provider abstraction, results persisted with
    raw response hash, decision recorded, weekly re screening cron
- 08.5 Risk tiers and limits
  - risk_scores, tenant_limits (per transaction, daily, monthly, payout cap), tier assignment
    rules, automatic hold on threshold breach, release through approval
- 08.6 Retention and privacy requests
  - data retention policy per category, export and erasure request flow through approval,
    audit of every access, docs/handoff/STEP-08-HANDOFF.md

Tables: kyc_cases, kyc_requirements, kyc_documents, kyc_reviews, screening_results,
risk_scores, tenant_limits, compliance_holds, privacy_requests.
Routes: /dashboard/kyc/*, /admin/kyc/*, /api/kyc/*.
Exit criteria: unverified tenant cannot create a payment intent; every review and access
audited; screening result reproducible; limits enforced server side.

---

## STEP 09 - Clients CRM and Tokenized Client Portal

Goal: client records and secure login free links.

Sub-steps:

- 09.1 Client model
  - clients, client_contacts, client_addresses, client_notes, client_tax_profiles
  - duplicate detection on email plus fuzzy name with merge flow, tag and archive
- 09.2 Token service
  - HMAC-SHA256 signed token with 128 bit random, scope, document id, company id, expiry,
    single purpose, masked in logs, no PII in URL
  - portal_tokens with revoke, reissue, usage counter, per token rate limit, optional email
    OTP gate
- 09.3 Portal routes
  - app/(client-portal)/layout.tsx, /hub/[token], /expired, error and loading states
  - invoice, estimate and receipt views are created in Step 10 inside this layout
- 09.4 Portal experience
  - tenant branding, mobile first, accessible, no login, locale fixed to English
- 09.5 Client events
  - client_events: view, download, payment start, consent, with IP, user agent, timestamp,
    used later as dispute evidence
- 09.6 Portal security
  - brute force guard, VPN and proxy check on payment entry, email forward warning,
    noindex headers, token rotation guidance, docs/handoff/STEP-09-HANDOFF.md

Tables: clients, client_contacts, client_addresses, client_notes, client_tax_profiles,
portal_tokens, client_events, client_merges.
Routes: /hub/[token], /expired, /api/portal/*.
Exit criteria: expired token renders a real recovery path; revoked token fails closed; every
portal query is scoped by token and company; no client record leaks across companies.

---

## STEP 10 - Invoicing Core, Numbering, PDF Engine, Sales Tax on Invoice

Goal: complete document system with correct money handling.

Sub-steps:

- 10.1 Invoice domain
  - invoices, invoice_lines, invoice_sequences, invoice_events, invoice_snapshots
  - fields: currency USD fixed, subtotal, discount, tax total, total, amount paid, balance,
    due date, status, notes, terms, tax mode inclusive or exclusive, exempt flag
  - all arithmetic through lib/core/money.ts, line rounding before summation
- 10.2 Numbering
  - per company prefix pattern with advisory lock, gapless within a fiscal year scope,
    retry safe, audit entry for every allocated number
- 10.3 Document family
  - estimates with approval to invoice conversion, credit notes linked to invoices,
    receipts generated on payment, recurring invoices with schedule rows
- 10.4 PDF engine
  - @react-pdf/renderer server components for invoice, estimate, receipt, credit note
  - Amazon acceptable layout rules: clear invoice number, dates, seller and buyer blocks,
    line table with tax column, totals block, payment instructions, footer terms
  - generated PDF stored through Step 06 encrypted storage, snapshot hash stored on invoice
- 10.5 API and actions
  - Zod schemas in schemas/invoice.ts used by both client and server
  - sanitize, permission guard, audit, optimistic locking, duplicate detection by hash,
    idempotent send, batch operations with count confirmation
- 10.6 Lists and dashboards
  - server rendered tables with keyset pagination, filters resolved on the server,
    one aggregate function per summary card, CSV export through the job queue
- 10.7 Portal document views
  - app/(client-portal)/i/[token], /e/[token], /r/[token] with download and accept actions,
    consent capture written to client_events
- 10.8 Billing lifecycle
  - reminder schedule, dunning ladder, late fee rules with cap and waiver, write off with
    approval, promise to pay, collection notes

Tables: invoices, invoice_lines, invoice_sequences, invoice_events, invoice_snapshots,
estimates, estimate_lines, estimate_approvals, receipts, credit_notes, recurring_invoices,
billing_schedules, dunning_steps, late_fee_rules.
Routes: /dashboard/invoices/*, /dashboard/estimates/*, /dashboard/recurring/*,
/api/invoices/*, /api/estimates/*.
Exit criteria: concurrent numbering is gapless under load; totals match a decimal reference for
inclusive and exclusive tax; PDF renders with tax column; duplicate invoice is flagged before
creation; portal document renders without login.

---

## STEP 11 - Payments Core Engine

Goal: a provider agnostic engine with the exact card policy, verification and settlement rules.

Sub-steps:

- 11.1 Payment domain and state machine
  - payment_intents, payments, payment_attempts, payment_status_history, refunds,
    ledger_entries, owner_balances, platform_fee_ledger, settlement_rules
  - states: created, requires_method, processing, requires_action, authorized, captured,
    failed, cancelled, refunded, partially_refunded, disputed, chargeback_lost
  - transitions only through the engine with guards, audit and idempotency
- 11.2 Adapter contract and registry
  - lib/payments/adapter.ts typed interface: capabilities, createIntent, confirmIntent,
    capture, refund, void, payout, verifyWebhook, testConnection, modeDescriptor
  - lib/payments/registry.ts registration API plus guard rejecting unknown providers
  - adapters are registered by Steps 12 and 13, never invented elsewhere
- 11.3 Routing
  - config/payment-routing.ts rules: USD, country, amount band, KYC tier, provider
    availability, active mode, owner preference
  - exactly one provider per intent, decision persisted in routing_decisions with rule ids
  - no provider match cancels the intent with a typed error and a client safe message
- 11.4 Idempotency and concurrency
  - Idempotency-Key required header, stored request hash, response replay, 24 hour TTL
  - row lock on intent confirmation, duplicate webhook safe, double capture impossible
- 11.5 Card policy engine
  - card_fingerprints (token hash, processor, first seen, blacklist flag)
  - invoice_card_usage (invoice, fingerprint, last success at) enforcing one card once per
    invoice per 24 hours
  - auth_failure_counters per fingerprint per 24 hours, blocking the third attempt,
    insufficient funds excluded by reason code map
  - velocity_counter windows per card, card plus IP, invoice, IP with config defaults
  - blacklist_entries by fingerprint, token hash, ip, email, device; never PAN
- 11.6 Verification and manual review
  - 3DS request policy per provider capability, challenge result stored
  - fallback rule: billing postal code match AND payer IP country match, both required
  - failure creates manual_review_cases with reason codes, invoice stays unpaid
- 11.7 Fees, settlement and payouts
  - platform fee percent resolution order: tenant override, plan, platform default, snapshot
    on payment
  - settlement hold days snapshot, release date computation, balance derivation from ledger
  - payout state machine: queued, in_review, approved, processing, paid, failed, reversed
  - payout failure returns funds to balance with ledger entry and audit
- 11.8 Webhook framework
  - webhook_events store with provider, external id, signature, raw body hash, processed_at
  - replay window, signature verification per adapter, idempotent processing, retry with
    backoff, dead letter, manual replay tool with permission and audit
  - docs/handoff/STEP-11-HANDOFF.md

Tables: payment_intents, payments, payment_attempts, payment_status_history, refunds,
ledger_entries, owner_balances, platform_fee_ledger, settlement_rules, routing_decisions,
card_fingerprints, invoice_card_usage, auth_failure_counters, velocity_counters,
blacklist_entries, manual_review_cases, payouts, payout_items, webhook_events,
payment_consents.
Exit criteria: double submit returns the same response; card reused on the same invoice within
24 hours is refused; third auth failure blocks the fingerprint; insufficient funds does not
increment; unmatched routing cancels without failover; fee and hold snapshots recorded.

---

## STEP 12 - Processors A: NMI Dual Mode and Stripe Connect

Precondition: read official documentation in this session for both providers and write
docs/providers/nmi.md and docs/providers/stripe-connect.md before coding.

Sub-steps:

- 12.1 NMI shared server module
  - REST client with security key auth, timeout, typed responses, error normalization
  - operations: sale with token, authorize and capture, void, refund, query transaction
  - request and response mapping validated by Zod, no card data touched
- 12.2 NMI mode A, Collect.js (default)
  - server action issuing tokenization key and nonce, client component that mounts the
    provider script and returns a single use token, 24 hour expiry documented
  - token posted to the shared server module for the charge
- 12.3 NMI mode B, Payment Component
  - npm package addition requested through the integrator, documented in
    docs/providers/nmi.md with the exact package name and version
  - component mount, tokenization, same shared server module for the charge
- 12.4 Mode switching
  - processor_modes table with one active mode per provider, enforced by unique partial index
  - switch action: super_admin, step-up, mandatory successful sandbox test in target mode,
    audit, cache invalidation, capability refresh
- 12.5 Stripe Connect adapter
  - connected account creation or onboarding link, capability and requirement handling
  - payment intent on connected account with application fee percent snapshot
  - 3DS and SCA handling, refund, partial refund, dispute mapping
  - payout schedule read and platform hold interaction documented
- 12.6 Webhooks
  - app/api/webhooks/nmi/route.ts and app/api/webhooks/stripe-connect/route.ts
  - raw body preservation, signature verification, replay window, idempotent processing
- 12.7 Sandbox verification and tests connection
  - test connection action per provider with typed result stored in processor_tests
  - documented sandbox card matrix in docs/providers/*.md, no test files in the repository
  - docs/handoff/STEP-12-HANDOFF.md

Tables: processor_credentials, processor_modes, processor_tests, connected_accounts,
provider_events, provider_capabilities.
Exit criteria: sandbox payment succeeds in both NMI modes and in Stripe Connect; application
fee appears on the platform ledger; signature invalid webhook rejected; mode switch without
passing test is refused; key material never returned to the browser.

---

## STEP 13 - Processors B: Payoneer, Airwallex, Rainforest, Payouts, Reconciliation

Precondition: read official documentation in this session for all three providers and write
docs/providers/payoneer.md, airwallex.md, rainforest.md before coding.

Sub-steps:

- 13.1 Payoneer adapter
  - collection where the account supports it, payout and mass payout to beneficiaries,
    status polling, webhook handling if provided, error normalization
- 13.2 Airwallex adapter
  - payment intent, 3DS challenge, refund, payout transfer, webhook signature verification
- 13.3 Rainforest adapter
  - charge, refund, webhook signature verification, capability discovery
- 13.4 Payout orchestration
  - payout_beneficiaries with generic payout method catalog labels only
  - beneficiary validation per provider, payout queue, retry policy, status sync,
    failure reversal to balance
- 13.5 Reconciliation
  - provider_settlements import, reconciliation_runs, mismatch table, daily cron,
    operator resolution screen with audit
- 13.6 Provider health and circuit breaker
  - provider_health snapshots, latency and error rate windows, circuit open state,
    routing excludes unhealthy provider for new intents only
  - registry now contains all five processors, docs/handoff/STEP-13-HANDOFF.md

Tables: payout_beneficiaries, provider_settlements, reconciliation_runs, reconciliation_items,
provider_health, circuit_breaker_state.
Exit criteria: all five providers appear in the registry and pass test connection in sandbox;
a beneficiary payout reaches processing state; reconciliation mismatch is visible and resolvable;
unhealthy provider is skipped for new intents.

---

## STEP 14 - Checkout, Payment Links, Email System, Templates, Activity Emails

Goal: the collection journey end to end and the complete email subsystem.

Sub-steps:

- 14.1 Checkout experience
  - hosted or embedded secure fields only, no card inputs in our DOM, 3DS challenge handling,
    retry messaging that respects card policy, success and failure states, mobile first,
    reduced motion, accessible
- 14.2 Payment links
  - create from invoice, expiry, usage limit, revoke, generic partner naming in every label
- 14.3 Email service
  - adapters: SMTP, Resend, SendGrid, SES, Mailgun, Postmark behind one interface
  - configuration by super admin at runtime, encrypted secrets, connection test, from domain
    verification guide, queue and retry, bounce and complaint handling
- 14.4 Templates
  - React Email templates stored with versioned rows, variables validated with Zod
  - editor with sanitized HTML preview, test send, per tenant overrides for branding only
- 14.5 Activity emails and journeys
  - signup verification, invoice sent, reminder ladder, payment succeeded, receipt,
    payment failed, KYC status change, payout sent, lock alert, security alert, weekly summary
  - quiet hours and timezone aware scheduling through the job queue
- 14.6 In app notifications and preferences
  - notifications table, per user preferences, unread counts through one aggregate query
- 14.7 Deliverability and duplicate guards
  - SPF, DKIM, DMARC documentation, send duplicate detection window, per recipient rate limit
  - docs/handoff/STEP-14-HANDOFF.md

Tables: email_providers, email_templates, email_template_versions, email_messages,
email_events, notifications, notification_preferences, payment_links.
Routes: /pay/[intentId] secure checkout host, /api/payments/*, /api/webhooks/email/*,
/dashboard/settings/email, /admin/emails/*.
Exit criteria: invoice email delivered in sandbox with correct branding; provider switch without
redeploy; template variables validated; duplicate send blocked; checkout completes and writes
consent evidence.

---

## STEP 15 - Super Admin Console

Goal: full operational control including the credential vault, processor modes, fee and hold,
fraud, disputes and audit.

Sub-steps:

- 15.1 Processor credential vault
  - pages under /admin/processors: list from registry, set credentials, test, enable, disable
  - AES-256-GCM encryption, last four and updated timestamp display only, step-up plus fresh
    identity re-verification, audit on every action
  - enable blocked until a passing test exists, unknown provider blocked by registry guard
- 15.2 Mode console
  - NMI dual mode switch with target mode test gate, one active mode enforcement, audit
- 15.3 Tenant management
  - list with server side search and keyset pagination, detail view, limits, suspension with
    approval chain, impersonation with banner and audit
- 15.4 Commerce controls
  - platform fee percent global and per tenant, settlement hold days, payout schedule, fee
    waiver with approval, effective date rules that never rewrite history
- 15.5 Fraud and manual review console
  - manual review queue with evidence, approve or decline with reason, blacklist management by
    fingerprint, IP, email and device, velocity configuration editor with validation
- 15.6 Disputes and chargebacks
  - disputes, dispute_evidence, evidence pack PDF assembly from snapshots and client events,
    deadline tracker, submission log, outcome analytics
- 15.7 Audit and observability
  - audit explorer with filters, hash chain verification tool, export through the job queue,
    feature flag editor, health dashboard with provider health, queue depth and cron status
- 15.8 Revenue surfaces
  - platform revenue, fees collected, payouts, settlement aging, all as single aggregate
    queries, no tax filing reports, sales tax shown only as invoice level figure
  - docs/handoff/STEP-15-HANDOFF.md

Tables: disputes, dispute_evidence, dispute_submissions, blacklist_entries (extended),
velocity_configurations, audit_exports, health_snapshots, impersonation_sessions.
Routes: /admin/processors, /admin/processors/[key], /admin/tenants, /admin/fees,
/admin/reviews, /admin/fraud, /admin/disputes, /admin/audit, /admin/health, /admin/flags.
Exit criteria: fee change applies to new payments only; vault never returns plaintext; enable
without test impossible; evidence pack PDF produced; hash chain verification passes.

---

## STEP 16 - Public Site, Blog, Support, SEO, Performance, Deploy, Go-Live

Goal: production launch with measured performance and a verified security posture.

Sub-steps:

- 16.1 Public site
  - home, features, pricing, coverage, security, how it works, about, contact, blog index and
    post, help center, legal pages (terms, privacy, refund, acceptable use, DPA)
  - all copy final in English, no unfinished sections, real content per page
- 16.2 Content and support
  - posts, post_versions, categories, media through the file service, draft review and publish
    through approval for the content role
  - support tickets with status, replies, attachments, internal notes, SLA
- 16.3 SEO
  - metadata per route, canonical, robots, sitemap, structured data for organization, product,
    FAQ and breadcrumbs, Open Graph images
- 16.4 Performance hardening
  - enforce docs/plan/05-performance-budget.md per page, index audit, query plan review,
    aggregate functions, cache tags, ISR where safe, bundle budgets, image and font policy
  - measured report written to docs/PERFORMANCE-REPORT.md
- 16.5 Accessibility and resilience
  - WCAG 2.1 AA pass, keyboard paths, focus order, reduced motion, offline banner,
    error boundaries on every route group
- 16.6 Deploy
  - Netlify production configuration, Vercel parity, Docker path, environment provisioning,
    migration apply procedure with rollback, cron registration, backup schedule
- 16.7 Monitoring and alerting
  - Sentry with PII scrubbing, uptime checks, alert rules, cron dead man switch,
    queue depth alert, payment failure rate alert
- 16.8 CI and release
  - deploy workflow, migration gate, guard gate, secret scan, tag release, changelog
- 16.9 Go-live verification
  - security checklist executed (headers, RLS penetration SQL, lockout, card policy, webhook
    signature, rate limits), live smoke payment with minimum amount, DNS, TLS, SPF DKIM DMARC,
    runbooks, docs/handoff/STEP-16-HANDOFF.md and docs/GO-LIVE-CHECKLIST.md

Tables: posts, post_versions, post_categories, support_tickets, support_messages,
support_attachments, page_views (aggregated only), seo_redirects.
Exit criteria: Lighthouse targets met on public pages and dashboard, all guards green, live
payment captured and refunded, audit chain verified, alert test delivered, go-live checklist
signed.

---

## Handoff Chain

Every step ends by writing docs/handoff/STEP-NN-HANDOFF.md with these sections:

1. Scope delivered, sub-step by sub-step
2. Files created and files modified with one line purpose
3. Tables created, columns, indexes, policies
4. Routes added
5. Environment variables added
6. Contracts exposed to later steps (functions, types, job kinds, permission keys)
7. Decisions taken and deviations, also appended to DECISIONS-LOG.md
8. Verification evidence: commands run and their outcome
9. Exact starting point for the next step
