# path: docs/plan/04-feature-catalog.md

# KD PAY LLC - VERIFIED FEATURE CATALOG (REVIEW LIST)

Purpose: a single reviewable list, after removing everything the owner excluded and after
merging the new requirements. Every feature below is in scope and mapped to the step that
builds it. Nothing in this file is optional unless it is marked PENDING with a decision id.

Legend:
- ID pattern: MODULE-NNN
- Step column: the chat that implements it
- PENDING = waiting for an owner decision, see docs/plan/06-open-decisions.md

---

## FND - Foundation and toolchain (Step 01)

| ID | Feature |
| --- | --- |
| FND-001 | Next.js App Router project with TypeScript strict |
| FND-002 | Tailwind design tokens and CSS variables |
| FND-003 | shadcn/ui primitive set with dark mode parity |
| FND-004 | Central money module using decimal.js, USD only |
| FND-005 | Structured logger with correlation id and PII redaction |
| FND-006 | Typed error classes and API response envelope |
| FND-007 | Zod validated environment with fail fast boot |
| FND-008 | UUID v7 identifier helper |
| FND-009 | Guard scripts: English only, no tests, no unfinished markers, no any, no console, path comment |
| FND-010 | Continuous integration: guards, typecheck, lint, build, secret scan |
| FND-011 | Navigation map with module level aggregation |
| FND-012 | Deploy manifests for Netlify, Vercel and Docker |

## SEC - Security core (Step 03)

| ID | Feature |
| --- | --- |
| SEC-001 | Input sanitizing with NFC normalization and zero width removal |
| SEC-002 | Server side HTML sanitizing for rich text |
| SEC-003 | SSRF guard with internal address block |
| SEC-004 | Header injection strip |
| SEC-005 | Nonce based content security policy |
| SEC-006 | Security header set with HSTS preload |
| SEC-007 | CSRF double submit plus origin validation |
| SEC-008 | Request body size and method allowlist |
| SEC-009 | Named rate limit policies per endpoint class |
| SEC-010 | Trusted proxy aware client IP resolution |
| SEC-011 | Cloudflare Turnstile verification |
| SEC-012 | VPN, proxy, Tor and datacenter classification |
| SEC-013 | IP reputation cache with TTL |
| SEC-014 | Disposable email blocklist |
| SEC-015 | Bot and headless browser signals |
| SEC-016 | Network policy map per surface (signup, payment, KYC, admin, login) |
| SEC-017 | AES-256-GCM envelope encryption with key id |
| SEC-018 | Per tenant key derivation through HKDF |
| SEC-019 | Blind index for encrypted field search |
| SEC-020 | Key rotation aware decryption |
| SEC-021 | PII redaction for logs and error reporting |
| SEC-022 | Single audit writer with correlation propagation |
| SEC-023 | Zero tolerance strike engine with automatic escalation |
| SEC-024 | Secret scanning and service role import guard |
| SEC-025 | Threat model document and header verification script |
| SEC-026 | Geo and country blocking configuration |
| SEC-027 | Impossible travel and mid session IP change detection |
| SEC-028 | Device fingerprint capture and trust score |
| SEC-029 | Credential stuffing detection through breach password check |
| SEC-030 | Automatic account suspension on critical anomaly with approval trail |

## AUTH - Authentication (Step 04)

| ID | Feature |
| --- | --- |
| AUTH-001 | Email and password signup with Turnstile |
| AUTH-002 | Email verification flow |
| AUTH-003 | Generic error messaging with no account enumeration |
| AUTH-004 | Login with two failure lockout for two hours |
| AUTH-005 | Lock state persisted in the database |
| AUTH-006 | Correct password refused while locked |
| AUTH-007 | Password reset refused while locked |
| AUTH-008 | Password change does not clear the lock |
| AUTH-009 | Lock alert email to the account owner |
| AUTH-010 | Operator unlock with mandatory email contact confirmation |
| AUTH-011 | Audit entry for every unlock |
| AUTH-012 | IP rate limit alongside account lock |
| AUTH-013 | TOTP two factor enrolment with QR |
| AUTH-014 | Ten single use backup codes, hashed |
| AUTH-015 | Two factor reset through approval |
| AUTH-016 | Session list with device and IP |
| AUTH-017 | Session revoke one or all |
| AUTH-018 | Step-up token for critical actions |
| AUTH-019 | Password policy with breach check and history |
| AUTH-020 | Super admin impersonation with banner, time limit and audit |
| AUTH-021 | Login notification on new device |
| AUTH-022 | Idle and absolute session timeout configuration |

## ACL - Roles, permissions and approvals (Step 05)

| ID | Feature |
| --- | --- |
| ACL-001 | Permission catalog grouped by module |
| ACL-002 | Role to permission matrix |
| ACL-003 | Guard helpers for role, permission, company and step-up |
| ACL-004 | Admin staff sub roles: compliance_kyc, support, content |
| ACL-005 | Least privilege defaults with time bounded elevation |
| ACL-006 | Approval request with ordered steps |
| ACL-007 | Approval rules map per action |
| ACL-008 | Dual approval for critical actions |
| ACL-009 | SLA auto escalation |
| ACL-010 | Delegation when a role holder is unavailable |
| ACL-011 | Self approval block |
| ACL-012 | Approval diff view |
| ACL-013 | Bulk decision with count confirmation |
| ACL-014 | Approval inbox with realtime updates |
| ACL-015 | Immutable approval history |
| ACL-016 | Admin shell with permission filtered navigation |
| ACL-017 | Navigation integrity guard against dead links |
| ACL-018 | Command palette filtered by permission |

## STOR - Storage and files (Step 06)

| ID | Feature |
| --- | --- |
| STOR-001 | Provider abstraction with Supabase Storage adapter |
| STOR-002 | Optional Drive adapter, PENDING D1 |
| STOR-003 | Runtime provider resolution per company |
| STOR-004 | AES-256-GCM encryption before upload |
| STOR-005 | Chunked encryption for large files |
| STOR-006 | Magic byte and MIME verification |
| STOR-007 | Image re-encode and EXIF strip |
| STOR-008 | SVG sanitizing |
| STOR-009 | PDF JavaScript removal |
| STOR-010 | Archive bomb and zip slip guard |
| STOR-011 | Malware scan hook and quarantine |
| STOR-012 | Duplicate detection by content hash |
| STOR-013 | Signed short lived download with permission check |
| STOR-014 | Access log per file |
| STOR-015 | Provider relocation tool with dry run and hash parity |
| STOR-016 | Retention policy and purge job |

## TEN - Tenant onboarding and settings (Step 07)

| ID | Feature |
| --- | --- |
| TEN-001 | Company creation transaction with slug lock |
| TEN-002 | Resumable onboarding wizard |
| TEN-003 | Company profile and legal identity fields |
| TEN-004 | Address and contact management |
| TEN-005 | Tax identifier stored for display only |
| TEN-006 | Timezone and business day configuration |
| TEN-007 | Branding: logo, colors, typography with contrast validation |
| TEN-008 | Invoice prefix and numbering pattern |
| TEN-009 | Email sender identity per tenant |
| TEN-010 | Portal vanity path |
| TEN-011 | Display currency with frozen FX snapshot |
| TEN-012 | Default payment terms and invoice footer |
| TEN-013 | Cached tenant configuration loader |
| TEN-014 | Settings audit and version conflict interface |
| TEN-015 | Owner payout profile |
| TEN-016 | Owner cannot invite staff, single seat enforced |
| TEN-017 | Onboarding checklist with completion state |
| TEN-018 | Account closure request through approval |

## KYC - Compliance (Step 08)

| ID | Feature |
| --- | --- |
| KYC-001 | Case model with one active case per company |
| KYC-002 | Country and business type requirement matrix |
| KYC-003 | Encrypted document upload with category |
| KYC-004 | Draft save and submit for review |
| KYC-005 | Request for information and resubmission |
| KYC-006 | Reviewer queue with SLA timer |
| KYC-007 | Checklist based decision with reason codes |
| KYC-008 | Approval chain for override and tier elevation |
| KYC-009 | Sanctions, PEP and adverse media screening |
| KYC-010 | Screening result persistence with raw response hash |
| KYC-011 | Periodic re-screening job |
| KYC-012 | Risk score and tier assignment |
| KYC-013 | Tenant limits per transaction, day, month and payout |
| KYC-014 | Automatic compliance hold on breach |
| KYC-015 | Hold release through approval |
| KYC-016 | Privacy request handling for export and erasure |

## CRM - Clients (Step 09)

| ID | Feature |
| --- | --- |
| CRM-001 | Client records with contacts and addresses |
| CRM-002 | Duplicate detection on email plus fuzzy name |
| CRM-003 | Client merge with audit |
| CRM-004 | Tags, archive and soft delete |
| CRM-005 | Tax profile per client |
| CRM-006 | Internal notes |
| CRM-007 | Open balance cached per client |
| CRM-008 | Client timeline of invoices, payments and views |
| CRM-009 | CSV import with duplicate pre-flight |
| CRM-010 | CSV export through the job queue |
| CRM-011 | Server side search and keyset pagination |
| CRM-012 | Client level portal link history |
| CRM-013 | Statement of account PDF for a client |
| CRM-014 | Data minimization on client deletion request |

## PORT - Tokenized client portal (Step 09 and Step 10)

| ID | Feature |
| --- | --- |
| PORT-001 | HMAC signed token with 128 bit randomness |
| PORT-002 | Per document scoped token |
| PORT-003 | Expiry per plan with revoke and reissue |
| PORT-004 | Token hash storage, raw token never persisted |
| PORT-005 | Optional email OTP gate |
| PORT-006 | Login free invoice view |
| PORT-007 | Estimate accept or decline |
| PORT-008 | Receipt view and download |
| PORT-009 | Client hub with all documents |
| PORT-010 | Expired link recovery page |
| PORT-011 | Tenant branding on every portal page |
| PORT-012 | noindex and no PII in URL |
| PORT-013 | Token brute force guard and rate limit |
| PORT-014 | Client event capture for evidence |
| PORT-015 | Consent capture with timestamp, IP and text version |
| PORT-016 | Network trust check before payment entry |

## INV - Invoicing and documents (Step 10)

| ID | Feature |
| --- | --- |
| INV-001 | Invoice with lines, quantity, unit price and discount |
| INV-002 | Line level tax rate with inclusive or exclusive mode |
| INV-003 | Tax exempt client flag |
| INV-004 | Rounding policy half up at four decimals per line |
| INV-005 | Gapless numbering with advisory lock |
| INV-006 | Fiscal year scoped sequence |
| INV-007 | Status lifecycle with events |
| INV-008 | Optimistic locking with conflict interface |
| INV-009 | Duplicate detection by hash with override confirmation |
| INV-010 | Invoice send with idempotent job |
| INV-011 | Invoice PDF with tax column and payment instructions |
| INV-012 | Immutable snapshot hash on send |
| INV-013 | Estimate to invoice conversion |
| INV-014 | Credit note against invoice |
| INV-015 | Receipt generated on payment |
| INV-016 | Recurring invoice schedule |
| INV-017 | Payment reminder ladder |
| INV-018 | Dunning step templates |
| INV-019 | Late fee rules with cap and waiver |
| INV-020 | Promise to pay |
| INV-021 | Write off with approval |
| INV-022 | Batch send with per item result |
| INV-023 | Bulk status change and archive |
| INV-024 | CSV export of invoices and lines |
| INV-025 | Server rendered list with filters and keyset pagination |
| INV-026 | Dashboard summary through one aggregate query |
| INV-027 | Client statement PDF |
| INV-028 | Public note and internal note separation |
| INV-029 | Attachments per invoice |
| INV-030 | Void invoice with audit |
| INV-031 | Partial payment allocation |
| INV-032 | Overpayment handling as client credit |
| INV-033 | Multi document search |
| INV-034 | Sales tax collected shown per invoice only, no tax module |

## PAY - Payment engine (Step 11)

| ID | Feature |
| --- | --- |
| PAY-001 | Payment intent with explicit state machine |
| PAY-002 | Attempt tracking per intent |
| PAY-003 | Status history with reason codes |
| PAY-004 | Idempotency key with response replay |
| PAY-005 | Concurrency guard on confirmation |
| PAY-006 | Provider routing rules with persisted decision |
| PAY-007 | No provider match cancels the payment, no failover |
| PAY-008 | KYC tier gate before payment |
| PAY-009 | Card token fingerprint registry |
| PAY-010 | One card once per invoice per 24 hours |
| PAY-011 | Authentication failure counter with third attempt block |
| PAY-012 | Insufficient funds excluded from the counter |
| PAY-013 | Blacklist by fingerprint, token, IP, email and device |
| PAY-014 | Velocity limits per card, card plus IP, invoice and IP |
| PAY-015 | 3D Secure request when supported |
| PAY-016 | Postal code and IP country fallback verification |
| PAY-017 | Manual review case on verification failure |
| PAY-018 | Platform fee percent with tenant override and snapshot |
| PAY-019 | Settlement hold days with snapshot and release date |
| PAY-020 | Ledger based owner balance, available, pending and reserve |
| PAY-021 | Refund full and partial with ledger entries |
| PAY-022 | Void and cancel flows |
| PAY-023 | Payout state machine with failure reversal |
| PAY-024 | Webhook event store with signature verification |
| PAY-025 | Replay window and idempotent processing |
| PAY-026 | Webhook retry with backoff and dead letter |
| PAY-027 | Manual webhook replay with permission and audit |
| PAY-028 | Routing decision audit |
| PAY-029 | Payment consent record before charge |
| PAY-030 | Failure reason normalization across providers |
| PAY-031 | Payment status realtime for the current invoice only |
| PAY-032 | Double capture prevention |
| PAY-033 | Dispute status intake from provider |
| PAY-034 | Reconciliation safe settlement import |
| PAY-035 | Currency guard rejecting anything but USD |
| PAY-036 | Amount guard rejecting negative and zero values |

## PRV - Processors (Steps 12 and 13)

| ID | Feature |
| --- | --- |
| PRV-001 | Adapter contract with capability descriptor |
| PRV-002 | Registry guard rejecting unknown providers |
| PRV-003 | NMI Collect.js mode, default |
| PRV-004 | NMI Payment Component mode |
| PRV-005 | NMI shared server module for both modes |
| PRV-006 | NMI sale, authorize, capture, void, refund |
| PRV-007 | Stripe Connect onboarding and account links |
| PRV-008 | Stripe Connect payment with application fee |
| PRV-009 | Stripe Connect refund and dispute mapping |
| PRV-010 | Payoneer collection and mass payout |
| PRV-011 | Airwallex payment intent, 3DS, refund, payout |
| PRV-012 | Rainforest charge and refund |
| PRV-013 | Webhook endpoints per provider with signature verification |
| PRV-014 | Connection test per provider with stored result |
| PRV-015 | One active mode per provider enforced in the database |
| PRV-016 | Mode switch gated by passing test and audit |
| PRV-017 | Provider health snapshot and circuit breaker |
| PRV-018 | Beneficiary management with generic payout labels |

## CHK - Checkout and payment links (Step 14)

| ID | Feature |
| --- | --- |
| CHK-001 | Hosted secure checkout, no card input in our DOM |
| CHK-002 | 3DS challenge handling |
| CHK-003 | Success, failure and retry states with policy aware messaging |
| CHK-004 | Mobile first checkout layout |
| CHK-005 | Payment link creation from invoice |
| CHK-006 | Link expiry and usage limit |
| CHK-007 | Link revoke |
| CHK-008 | Consent text versioning at checkout |
| CHK-009 | Receipt download immediately after payment |
| CHK-010 | Duplicate submit protection in the interface |
| CHK-011 | Accessible checkout with keyboard and screen reader support |
| CHK-012 | Reduced motion support |
| CHK-013 | Generic partner naming in every payment label |
| CHK-014 | Failed payment guidance without exposing risk logic |

## EML - Email and notifications (Step 14)

| ID | Feature |
| --- | --- |
| EML-001 | SMTP provider adapter |
| EML-002 | API provider adapters: Resend, SendGrid, SES, Mailgun, Postmark |
| EML-003 | Runtime provider configuration by super admin |
| EML-004 | Encrypted provider secrets |
| EML-005 | Connection test and from address verification |
| EML-006 | Queue with retry and backoff |
| EML-007 | Bounce and complaint handling |
| EML-008 | Template registry with versioning |
| EML-009 | Template variables validated with Zod |
| EML-010 | Template editor with sanitized preview |
| EML-011 | Test send |
| EML-012 | Tenant branding in templates |
| EML-013 | Activity emails: verification, lock alert, security alert |
| EML-014 | Activity emails: invoice sent, reminders, payment success, receipt, failure |
| EML-015 | Activity emails: KYC status, payout sent, weekly summary |
| EML-016 | Quiet hours and timezone aware scheduling |
| EML-017 | In app notifications with preferences |
| EML-018 | Duplicate send guard and per recipient rate limit |

## ADM - Operator console (Step 15)

| ID | Feature |
| --- | --- |
| ADM-001 | Processor credential vault, super admin only |
| ADM-002 | Step-up plus fresh identity verification for the vault |
| ADM-003 | Secret write with AES-256-GCM and last four display only |
| ADM-004 | Connection test required before enable |
| ADM-005 | Enable and disable with audit |
| ADM-006 | Registry guard on the provider picker |
| ADM-007 | NMI mode switch console |
| ADM-008 | Tenant list with server side search |
| ADM-009 | Tenant detail, limits and plan assignment |
| ADM-010 | Tenant suspension through approval |
| ADM-011 | Impersonation with banner and audit |
| ADM-012 | Platform fee percent global and per tenant |
| ADM-013 | Settlement hold days global and per tenant |
| ADM-014 | Fee waiver with approval and effective dating |
| ADM-015 | Manual review queue with evidence |
| ADM-016 | Blacklist management by fingerprint and identifiers |
| ADM-017 | Velocity configuration editor with validation |
| ADM-018 | Dispute case management with deadline tracker |
| ADM-019 | Evidence pack generation as a single PDF |
| ADM-020 | Dispute submission log and outcome analytics |
| ADM-021 | Audit explorer with hash chain verification |
| ADM-022 | Audit export through the job queue |
| ADM-023 | Feature flag editor |
| ADM-024 | Health dashboard: providers, queue depth, cron status |
| ADM-025 | Revenue surfaces: fees, payouts, settlement aging |
| ADM-026 | KYC review console integration |

## PUB - Public site, content and support (Step 16)

| ID | Feature |
| --- | --- |
| PUB-001 | Home, features, pricing, coverage, security, how it works |
| PUB-002 | About and contact pages with validated forms |
| PUB-003 | Blog index and post pages |
| PUB-004 | Content editor with sanitized HTML |
| PUB-005 | Draft, review and publish through approval |
| PUB-006 | Categories and media library through storage service |
| PUB-007 | Help center with searchable articles |
| PUB-008 | Support tickets with replies and attachments, PENDING D13 |
| PUB-009 | Legal pages: terms, privacy, refund, acceptable use, DPA |
| PUB-010 | Metadata, canonical and Open Graph per route |
| PUB-011 | Structured data for organization, product, FAQ and breadcrumbs |
| PUB-012 | Sitemap and robots |
| PUB-013 | 404 and 500 pages with real navigation |
| PUB-014 | Cookie and analytics consent notice, PENDING D14 |
| PUB-015 | Status and uptime page |
| PUB-016 | Performance report and Lighthouse evidence |
| PUB-017 | Accessibility statement and WCAG 2.1 AA evidence |
| PUB-018 | Developer documentation page for the platform API, PENDING D3 |
| PUB-019 | Changelog page fed from release notes |
| PUB-020 | Contact and sales inquiry routing to support queue |

## OPS - Operate and launch (Steps 01, 16)

| ID | Feature |
| --- | --- |
| OPS-001 | Netlify production deploy with preview deploys |
| OPS-002 | Vercel parity deploy |
| OPS-003 | Docker and VPS alternative |
| OPS-004 | Migration apply and rollback procedure |
| OPS-005 | Scheduled jobs with leader lock |
| OPS-006 | Backup schedule and restore drill |
| OPS-007 | Error monitoring with PII scrubbing |
| OPS-008 | Uptime monitoring and alert routing |
| OPS-009 | Cron dead man switch |
| OPS-010 | Queue depth and payment failure alerts |
| OPS-011 | Release tagging and changelog |
| OPS-012 | Runbooks for payment, webhook, payout and lockout incidents |
| OPS-013 | Go live checklist executed and signed |
| OPS-014 | Live smoke payment with minimum amount and immediate refund |

---

## Removed features (not built, not referenced)

1. WhatsApp automation, WhatsApp templates, WhatsApp webhook
2. SMS channel and SMS providers
3. Payroll, payslips, salary structures, reimbursement runs
4. HR module: employees, attendance, leave, performance
5. Double entry accounting: chart of accounts, journals, trial balance, P&L, balance sheet,
   cash flow statement, fixed assets, depreciation, budgets
6. Tax filing, tax returns, tax remittance, tax reports, jurisdiction tax engines,
   VAT validation services
7. Social media automation and publishing for any platform
8. Google Drive as mandatory storage, and the Drive service account flow, PENDING D1
9. Owner or sub merchant staff invitation, staff seats, staff permissions editor
10. Eleven processors removed: Adyen, Braintree, BlueSnap, Square, FastSpring, Paddle,
    PayPro Global, PayPal standalone, Payarc, Rapyd, Verifone
11. Generic custom HTTPS gateway adapter
12. QR business card module
13. AI provider layer and prompt tooling
14. Affiliate, reseller, white label partner and accountant roles
15. Automated test suites of any kind
16. Bank feed integrations (Plaid, Salt Edge)
17. Receipt OCR
18. Marketplaces and e-commerce connectors (Shopify, WooCommerce)
19. Zapier and Make connectors
20. Multi currency ledger and FX gain or loss accounting

## Pending decisions (must be resolved before the related step starts)

| Decision | Related feature | Step it blocks |
| --- | --- | --- |
| D1 | Google Drive optional adapter | 06 |
| D2 | Owner staff seats permanently removed | 07 |
| D3 | Public REST API and API keys for tenants | 16 |
| D4 | Recurring invoices and schedules | 10 |
| D5 | Portal email OTP gate default | 09 |
| D6 | Display currency conversion surfaces | 07 |
| D7 | Payoneer used for collection as well as payout | 13 |
| D8 | Contracts and e-signature | none yet, excluded until decided |
| D9 | Support tickets inside the product | 16 |
| D10 | Analytics pixels and consent banner | 16 |
| D11 | IP reputation vendor and sanctions screening vendor | 03 and 08 |
| D12 | Plan tiers and quota enforcement | 15 |
| D13 | Public status page hosting | 16 |
| D14 | Docker and VPS parity at launch | 16 |
| D15 | Sales tax display on the revenue summary | 15 |
