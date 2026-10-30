# path: docs/plan/00-charter.md

# KD PAY LLC - PROJECT CHARTER (AUTHORITATIVE)

Version: 1.0
Status: Active governing document
Supersedes: MASTER_PROMPT.md (v2.0), FEATURE_LIST.md (KD SOLUTION IT, 10-chat plan)
Applies to: every step chat, every pull request, every migration

If MASTER_PROMPT.md or FEATURE_LIST.md contradicts this charter, this charter wins.
If this charter contradicts the owner's latest written instruction, the instruction wins and
the charter is amended in the same step that implements it.

---

## 1. Product Identity

- Legal name: KD PAY LLC
- Product type: enterprise multi-tenant SaaS invoicing and payment platform
- Commercial model: platform of record for invoice collection, with platform fee and
  settlement hold controlled by the platform operator
- Launch sequence: Asia first, global second
- Launch regions (collection enabled at go-live, expandable by configuration):
  Bangladesh, India, Pakistan, Sri Lanka, Nepal, Philippines, Thailand, Singapore, Malaysia,
  Vietnam, Indonesia, Japan, South Korea, United Arab Emirates, United Kingdom, United States
- Primary infrastructure region: Singapore. Secondary: Tokyo.
- Interface language: English ONLY, single locale, no translator layer
- Base currency: USD ONLY
- Deployment targets: Netlify (primary), Vercel (parity), Docker on VPS (alternative)

## 2. Document Map (read order for every step chat)

1. docs/plan/00-charter.md (this file) - rules and policy
2. docs/plan/01-step-map.md - the 16 steps and sub-steps
3. docs/plan/02-chat-playbook.md - the identical instruction pattern for every chat
4. docs/plan/03-data-model.md - table ownership, conventions, RLS and audit patterns
5. docs/plan/04-feature-catalog.md - the verified feature list in scope
6. docs/plan/05-performance-budget.md - query, cache and bundle budgets
7. docs/plan/06-open-decisions.md - decisions that must be locked before Step 01
8. docs/plan/07-file-ownership-map.md - which step owns which path
9. docs/handoff/STEP-NN-HANDOFF.md - the immediately preceding step handoff
10. docs/handoff/DECISIONS-LOG.md - every decision made so far

## 3. Scope In

- Multi-tenant company onboarding, branding and settings
- KYC and KYB review, AML and sanctions screening, risk tiers and limits
- Clients CRM, tokenized login-free client portal
- Invoices, invoice lines, estimates, receipts, credit notes, recurring invoices
- Invoice level sales tax collection (rate, inclusive or exclusive, exempt flag)
- PDF document engine, document snapshots, immutable evidence
- Payments: intents, attempts, refunds, disputes, chargebacks, evidence packs
- Platform fee percentage and settlement hold days, both operator configurable
- Payouts to owners, beneficiary management, settlement reconciliation
- Five payment processors only: NMI, Stripe Connect, Payoneer, Airwallex, Rainforest
- Processor credential vault, connection testing, mode switching, enable and disable
- Email system: SMTP and API providers, templates, activity emails, transactional journeys
- Super admin, CEO, compliance and KYC, support, content roles with approval chain
- Public marketing site, blog and CMS, support center, legal pages, SEO
- File storage with AES-256-GCM encryption at rest and at upload
- Audit chain, rate limiting, fraud controls, velocity limits, blacklist management
- Performance budgets, caching, monitoring, CI, deploy and go-live runbooks

## 4. Scope Out (explicitly excluded, must not appear in code, database or UI)

| Excluded | Note |
| --- | --- |
| WhatsApp automation and Meta Cloud API | Removed |
| SMS channel | Removed, email and in-app only |
| Payroll and payslips | Removed |
| HR module, attendance, leave | Removed |
| Double-entry accounting, chart of accounts, journals, financial statements | Removed |
| Tax filing, tax returns, tax remittance, tax reporting module | Removed |
| Social media automation and publishing | Removed |
| Owner or sub-merchant staff invitation and staff seats | Removed, owner accounts are single seat |
| Eleven of the sixteen processors (Adyen, Braintree, BlueSnap, Square, FastSpring, Paddle, PayPro Global, PayPal standalone, Payarc, Rapyd, Verifone, Custom) | Removed, five processors remain |
| Generic custom gateway adapter | Removed with the processor list |
| QR business card module | Removed |
| AI provider layer | Removed |
| Affiliate, reseller, white label partner, accountant roles | Removed |
| Automated test files of any kind | Prohibited by project rule |
| Google Drive as a storage backend | See decision D1 in docs/plan/06-open-decisions.md |

Sales tax is collected on invoices and shown on documents. Tax filing, tax remittance,
tax reports and jurisdiction tax engines are out of scope. A single read-only
"tax collected" figure may appear inside the revenue summary because it is part of the
payment amount, and it never becomes a separate reporting module.

## 5. Role Model

Hierarchy: super_admin > ceo > admin_staff > owner. Clients are tokenized and have no login.

| Role | Scope | Notes |
| --- | --- | --- |
| super_admin | Platform owner | Full authority, 2FA mandatory, step-up for critical actions, sole role able to open the processor credential vault |
| ceo | Platform operator | Operational leadership, approves admin_staff actions, cannot open the credential vault |
| admin_staff: compliance_kyc | Platform operator | KYC and KYB review, screening, compliance holds |
| admin_staff: support | Platform operator | Support tickets, tenant assistance, read-mostly access |
| admin_staff: content | Platform operator | Blog, CMS, legal and marketing content |
| owner | Tenant | Own company data only, single seat, cannot invite staff |
| client | Tokenized visitor | No account, no login, HMAC-signed scoped links only |

Platform staff rows are company scoped to the platform company. Tenant data access by
platform staff is explicit, permission based and audit logged.

## 6. Absolute Rules (restated, enforced by scripts and CI)

R1. English only. Zero non-ASCII characters in code, comments, UI strings, seeds, filenames,
    email templates and database values. Guard: scripts/check-english-only.mjs.
R2. USD only. Every monetary value is numeric(18,4). All arithmetic uses decimal.js.
    JavaScript Number is never used for money. Guard: ban of float math on money columns
    plus code review checklist item.
R3. Text and structured data live in Supabase PostgreSQL. Files live in encrypted storage
    with a storage_provider column on every file record.
R4. No test files. No .test.ts, .spec.ts, __tests__, tests directory, or coverage config.
R5. No unfinished markers of any kind. No editor annotation tokens, no announcement pages for
    unbuilt features, no filler text, no stub branches in shipped code paths.
R6. No any. TypeScript strict, noImplicitAny, strictNullChecks, exactOptionalPropertyTypes,
    noUncheckedIndexedAccess. Guard: scripts/check-no-any.mjs.
R7. No console.log. Structured logging through lib/core/logger.ts only.
R8. Every file starts with the path comment: // path: relative/path/to/file.ts
R9. Every table has id (UUID v7), company_id, created_at, updated_at, deleted_at.
    company_id is nullable only for platform scope tables, enforced by a check constraint.
R10. Every table has RLS enabled with a default-deny policy.
R11. Every mutation writes an audit log entry through the single audit writer.
R12. Every API and every form validates with Zod, client side and server side.
R13. Every input is sanitized. XSS, SQL injection, CSRF, SSRF, path traversal, header
     injection, unicode and zero width attacks are handled centrally.
R14. Every payment request carries an idempotency key, persisted with response replay.
R15. Every editable record has a version column and is updated with optimistic locking.
R16. Role hierarchy is fixed as defined in section 5.
R17. Actions on the approval matrix require the approval chain before effect.
R18. Local wallet brands and country wallet brands are never named. Use
     "Global Payout Partners" and "Local Payment Methods via Licensed Partners".
R19. Zero trust and zero tolerance. Nothing is trusted by default, every request is verified,
     every anomaly is logged and every escalation is automatic.
R20. VPN, proxy, Tor and datacenter IP detection and blocking on signup, payment, KYC upload
     and admin surfaces. Login flags the session and requires 2FA.

## 7. Money, Rounding and Settlement

- Storage type: numeric(18,4), NOT NULL, default 0.0000
- Runtime type: decimal.js Decimal, wrapped in lib/core/money.ts
- Currency: USD. ISO 4217 display codes may be shown read-only with frozen FX rate, and no
  FX gain or loss accounting is performed
- Rounding: half up at 4 decimal places per computed amount, line totals rounded before
  summation, document totals are the sum of rounded lines, tax rounded per line
- Settlement: owner balance is derived, never stored as mutable float. Balances are computed
  from the ledger and cached in a summary row guarded by optimistic locking
- Platform fee: percentage with 4 decimal precision, applied per captured payment, stored in
  platform_fee_ledger with the rate snapshot
- Settlement hold: integer days, default value seeded by platform settings, per tenant
  override allowed, snapshot stored on each payment

## 8. Storage Policy

- Text and structured data: Supabase PostgreSQL only
- Files: encrypted with AES-256-GCM before leaving the server, then written to the active
  provider for that company
- Provider resolution at runtime: company_storage_config, fallback to the platform default
- Every file row records storage_provider, provider file key, content hash, key version,
  byte size, MIME type, and uploaded_by
- Downgrade path: no provider available means the upload fails closed with a typed error,
  never a silent plaintext write
- A relocation tool moves a company between providers with dry-run, verification and audit

## 9. Payment Processors (five, pluggable)

| Key | Processor | Usage | Adapter file |
| --- | --- | --- | --- |
| nmi | NMI | Collection, refunds, void, dual front end mode | lib/payments/adapters/nmi/** |
| stripe_connect | Stripe Connect | Collection on connected account, application fee, refunds, disputes, payouts | lib/payments/adapters/stripe-connect.ts |
| payoneer | Payoneer | Payouts to beneficiaries, collection where supported | lib/payments/adapters/payoneer.ts |
| airwallex | Airwallex | Collection, 3DS, refunds, payouts | lib/payments/adapters/airwallex.ts |
| rainforest | Rainforest | Collection, refunds, payouts | lib/payments/adapters/rainforest.ts |

Registry: lib/payments/registry.ts is the single source of truth. A processor that is not
present in the registry cannot be added, enabled or routed to from the dashboard.

### 9.1 Documentation Freshness Rule
Before writing a single line of adapter code, the step chat must open the processor's own
official documentation on the internet, in that session, and follow the current version.
Coding from memory or assumption is prohibited. Each adapter step writes
docs/providers/<provider>.md recording: documentation URLs used, version or date observed,
authentication scheme, endpoint paths, request and response shapes actually implemented,
webhook signature algorithm, sandbox credential source, and known limitations.

### 9.2 NMI Dual Mode
- Mode A: Collect.js. Default. Single use token, 24 hour expiry.
- Mode B: Payment Component. Requires an npm package; the package addition request goes
  through the integrator because package.json is integrator owned.
- Both modes deliver the token to the same shared server module, which calls the NMI REST API.
- Exactly one mode is active per processor at any time.
- Mode change is super_admin only, requires a successful sandbox test in the target mode,
  is written to the audit log, and invalidates cached capability data.

### 9.3 Credential Vault
- Server-side page, super_admin only, 2FA plus fresh identity re-verification (step-up)
- Secrets encrypted with AES-256-GCM, key material in environment variables only
- After save, the interface shows only last four characters and last updated timestamp
- Test connection must pass before enable; disable is always allowed
- Every save, test, enable and disable is audited with actor, IP, device and correlation id
- Routing rules that resolve to no single processor cancel the payment. Failover to another
  processor mid-payment is prohibited.

## 10. Card and Authentication Rules

- Card number, expiry and CVV are entered only inside the processor's own secure fields or
  hosted page. Our code never reads, transmits, stores or logs them, and no keystroke
  tracking exists anywhere in the product
- One successful card payment per invoice per card, then that card is refused for that
  invoice for 24 hours. Other invoices are unaffected
- Card related or authentication failures (invalid number, invalid CVV, invalid OTP, 3DS
  failure) are counted per card token: two failures inside 24 hours block that card on the
  third attempt
- Insufficient funds failures are excluded from that counter
- Processor fraud signals or a blacklist match reject immediately
- Blacklist keys are card tokens and processor fingerprints, never PAN or BIN plus last four
- Velocity limits: per card, per card plus IP, per invoice and per IP, with defaults in
  config and overrides in the database
- Every money value in this flow is numeric(18,4) and decimal.js

## 11. Verification and Manual Review

- Request 3D Secure whenever the processor and region support it
- If 3DS is not completed, authorize only when the billing postal code matches the invoice
  billing data AND the payer IP country matches the invoice country
- If either check fails, the payment is not captured, the invoice stays unpaid and a
  manual_review_case is created with the reason codes
- VPN, proxy and Tor checks apply on the payment page exactly as on signup and admin
- Webhooks are accepted only after signature verification, inside a replay window, and are
  processed idempotently with a stored event id

## 12. Login Lock Policy

- Two consecutive failures on password or 2FA code lock the account for 2 hours
- While locked, a correct password does not sign the user in, and password reset is refused
- Changing the password does not clear the lock
- On lock start, the account owner receives an email alert
- Unlock requires a platform operator with the unlock permission, only after confirmed email
  contact with the owner, recorded as an unlock note; every unlock is audited
- Failure counters and lock state are rows in the database, surviving restarts
- IP based rate limiting runs in parallel
- All error messages are generic and never reveal whether an account exists

## 13. Approval Chain

- Every action in the approval matrix creates an approval request with ordered steps
- Standard chain: requester (admin_staff) > CEO > super_admin
- Dual approval is required for: processor enable, processor mode change, platform fee change,
  settlement hold change, refund above threshold, manual card blacklist entry above threshold,
  tenant suspension, KYC override, credential rotation
- SLA per step with automatic escalation, delegation when a role holder is unavailable
- The requester can never approve their own request
- Approval history is immutable and audited

## 14. Performance Doctrine (reduce Supabase and Vercel load)

- Every page has a query budget in docs/plan/05-performance-budget.md and CI review must
  confirm it
- Server Components by default, client components only for interaction
- Aggregates are computed in one PostgreSQL function per screen where multiple counts are
  needed, never as N round trips
- Denormalized counters with triggers for invoice totals, paid amounts, open balances
- Keyset pagination everywhere, no offset pagination on large tables
- Index coverage audit at Step 16, with a required index list per hot query
- Caching layers: request memoization, route segment cache, tag based revalidation, CDN
  headers, and a server side short lived cache for tenant settings and capabilities
- Realtime subscriptions limited to the approval inbox and payment status of the current
  invoice, no global presence
- Background work through the job queue with pg_cron, never inline in a page render
- Bundle budget: initial JS under 200 KB gzip per route group, no heavy chart or editor
  libraries on public pages

## 15. Security Baseline (zero trust, zero tolerance)

Network: Cloudflare WAF, DDoS protection, TLS 1.3, HSTS preload, DNSSEC, geo rules,
IP reputation, datacenter and proxy detection, IP rate limits.

Application: parameterized queries only, output encoding by React, server side sanitizing for
rich text, strict CSP with nonce and no unsafe-inline, CSRF double submit plus origin check,
SSRF allowlist, clickjacking denial, strict CORS, open redirect validation, mass assignment
stripping through strict Zod, prototype pollution guard, safe regex, header injection strip,
path traversal rejection, NFC normalization, zero width removal.

Data: AES-256-GCM envelope encryption with key version, per tenant key derivation through
HKDF, service role key server only, environment schema validation at boot, secret scanning in
CI, PII redaction in logs and Sentry, encrypted backups.

Tenancy: company_id on every table, RLS default deny, tenant guard in every server action,
cross tenant access returns 404, storage path isolation per company, IDOR checks on every id
parameter.

Abuse: Turnstile, honeypots, device fingerprint, login and payment velocity, disposable email
blocklist, headless browser signals, credential stuffing checks, mid session IP change
detection, automatic suspension on critical anomalies.

Audit: append only audit_logs with hash chain and sequence gap detection, Ed25519 signature
optional per environment, five year minimum retention, export and integrity verification tool.

## 16. Branch and Release Model

- integration: trunk branch, always deployable, all step work merges here
- step/NN-slug: one branch per step chat, merged into integration by pull request
- main: production mirror, fast forward from integration at release
- Tags: release/YYYY.MM.DD-N
- Every pull request: guard scripts, typecheck, lint, build, migration check, secret scan,
  and a handoff document reference

## 17. Definition of Done (per step, non negotiable)

A step is complete only when all of the following are true.

1. Every sub-step of that step is implemented, with no partial feature
2. Every file begins with its path comment
3. TypeScript strict passes with zero any
4. Every mutation writes audit, validates with Zod, sanitizes input, and locks optimistically
4b. Every new table has RLS, default deny policy, indexes, and trigger wiring
5. Guard scripts pass: english only, no tests, no unfinished markers, no any, no console
6. Environment variables added to .env.example with description and fail fast schema entry
7. Migrations include a down migration and are idempotent
8. Navigation entries appended through the module nav file, not by editing another module
9. docs/handoff/STEP-NN-HANDOFF.md written
10. docs/handoff/DECISIONS-LOG.md appended with every decision and deviation
11. Build succeeds and the affected routes render in all four states: loading, empty, error,
    success
12. Mobile at 320 px verified, dark mode verified, keyboard and screen reader labels present
13. Query budget for each new page recorded and met
14. No cross step file modified without an ownership transfer note in the decisions log

## 18. Environment Matrix

| Concern | Development | Staging | Production |
| --- | --- | --- | --- |
| Supabase project | dev | staging | prod |
| Encryption keys | dev set | staging set | prod set, never rotated in place |
| Processors | sandbox | sandbox | live, enabled only after test |
| Email | capture or sandbox SMTP | sandbox SMTP | verified domain SMTP or API |
| Turnstile | test keys | test keys | live keys |
| Sentry | dev project | staging project | prod project |
| IP reputation | vendor sandbox | vendor sandbox | live vendor key |

Required third party accounts at go-live: Supabase, Netlify, Cloudflare (DNS, Turnstile, WAF),
SMTP or email API, IP reputation and proxy detection vendor, sanctions screening vendor,
Sentry. Google Drive is required only if decision D1 resolves to optional Drive support.

## 19. Change Control

- This charter changes only through a pull request that also updates
  docs/handoff/DECISIONS-LOG.md
- A removed feature that reappears in a later step is a defect and is reverted
- A new feature is added to docs/plan/04-feature-catalog.md before implementation
