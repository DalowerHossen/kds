# path: docs/plan/06-open-decisions.md

# KD PAY LLC - OPEN DECISIONS

Each decision is answered once, recorded here, and then appended to
docs/handoff/DECISIONS-LOG.md. Until a decision is answered, the recommended default applies so
that no step is blocked. A later change is a new decision entry, never a silent edit.

Status legend: OPEN, ANSWERED, SUPERSEDED.

---

## D1 - Google Drive storage

Status: OPEN, blocks Step 06

Two instructions were given at different times: files may live on Google Drive when a company
connects it, and Google Drive appears in the removal list.

Options:
- A) Supabase Storage only. No Drive code. The `storage_provider` column still exists so a
  provider can be added later without a migration.
- B) Optional per company Drive adapter with Supabase fallback, as in the first instruction.

Recommendation: A for launch. It removes a service account, per tenant OAuth, quota management
and a whole class of compliance risk, and keeps the migration path open. Option B is added
later as Step 06 extension if the owner asks.

Impact if A: the storage chapter of the charter stays valid, only the Drive adapter is dropped.
Impact if B: Step 06 grows by roughly a third and requires a Google Cloud project and service
account, plus encrypted refresh token storage.

## D2 - Owner staff seats

Status: OPEN, blocks Step 07

Instruction: owners and sub merchants must not be able to add staff.

Options:
- A) Owners are single seat. The `owner` role has no staff management surface.
- B) Owner can create read only collaborators without login.

Recommendation: A. It also removes the need for the `staff` role in the hierarchy, which keeps
permission review small and closes an entire abuse path.

## D3 - Public REST API and API keys for tenants

Status: OPEN, blocks Step 16

Options:
- A) No public API at launch. Internal server actions only.
- B) Read only API with tenant API keys, scoped tokens and rate limits.
- C) Full API with webhooks to tenants.

Recommendation: A at launch, B in the first post launch iteration, because every API key is a
new credential to rotate, monitor and revoke, and launch security is the priority.

## D4 - Recurring invoices

Status: OPEN, blocks Step 10

Options:
- A) Include recurring invoices with a schedule and a cron driven generator.
- B) Include invoice templates only, no automatic generation.

Recommendation: A. It is core invoicing, it costs one table set and one cron kind, and it
materially improves retention for Asian service businesses.

## D5 - Portal email OTP gate

Status: OPEN, blocks Step 09

Options:
- A) Optional per tenant, default off, on for invoices above a configured amount.
- B) Always on.
- C) Never on.

Recommendation: A. It keeps the login free promise while adding a second factor for large
amounts.

## D6 - Display currency surfaces

Status: OPEN, blocks Step 07

Options:
- A) USD only in the product, no conversion display.
- B) Optional read only conversion with frozen FX for the client portal only.

Recommendation: B limited to the portal, because Asian clients frequently ask for a local
figure while settlement remains USD. No FX gain or loss accounting in either case.

## D7 - Payoneer scope

Status: OPEN, blocks Step 13

Options:
- A) Payoneer for payouts only.
- B) Payoneer for collection where the account supports it, plus payouts.

Recommendation: B, with capability detection so the adapter advertises only what the account can
actually do.

## D8 - Contracts and e-signature

Status: OPEN, no step assigned

Options:
- A) Excluded from launch.
- B) Simple acceptance record on estimates only, which is already in scope.

Recommendation: A, with B already covered by estimate approval and consent capture.

## D9 - Support tickets inside the product

Status: OPEN, blocks Step 16

Options:
- A) Internal ticket table with email notifications and an admin queue.
- B) Contact form only, routing to a shared mailbox.

Recommendation: A. Support is a named office role, so the queue must exist.

## D10 - Analytics and consent banner

Status: OPEN, blocks Step 16

Options:
- A) Privacy first aggregate analytics only, no third party pixels.
- B) GA4 plus a consent banner.

Recommendation: A at launch. Payment pages never load third party scripts.

## D11 - Vendor selection for risk data

Status: OPEN, blocks Steps 03 and 08

- IP reputation and proxy detection vendor account and key
- Sanctions, PEP and adverse media screening vendor account and key
- Both are behind adapters, so the adapter contract is built first and the live key is plugged
  in when available

Recommendation: build both adapters with a vendor neutral interface, ship with the vendor that
provides a sandbox immediately.

## D12 - Plan tiers and quota enforcement

Status: OPEN, blocks Step 15

Options:
- A) Three plans with invoice quotas, no payment gating.
- B) Single plan at launch, platform fee only.

Recommendation: B at launch. Platform fee plus settlement hold already produces revenue, and
quota logic is a large surface for marginal launch value.

## D13 - Public status page

Status: OPEN, blocks Step 16

Options:
- A) Internal health dashboard only.
- B) Public status page fed by the health snapshots.

Recommendation: B, static rendered, cheap, and expected by enterprise buyers.

## D14 - Docker and VPS parity

Status: OPEN, blocks Step 16

Options:
- A) Netlify plus Vercel only at launch, Docker manifests kept in the repository.
- B) Full Docker deployment exercised at launch.

Recommendation: A, with the manifests and runbook shipped so the alternative is one command away.

## D15 - Sales tax on revenue surfaces

Status: OPEN, blocks Step 15

Options:
- A) Show tax collected per invoice only.
- B) Add one read only tax collected total to the revenue summary.

Recommendation: B, explicitly labelled as collected and not filed, since tax filing is out of
scope.

## D16 - Second language for the interface

Status: CLOSED by charter

Decision: English only, single locale, no translation layer. Confirmed by the charter.

## D17 - Processor list

Status: CLOSED by owner instruction

Decision: five processors only, NMI, Stripe Connect, Payoneer, Airwallex, Rainforest. The custom
gateway adapter is removed with the other eleven processors.

## D18 - Removed modules

Status: CLOSED by owner instruction

Decision: WhatsApp, SMS, payroll, HR, accounting, tax filing and reports, social automation,
owner staff seats, Google Drive as a mandatory dependency, and every other removed item listed
in docs/plan/04-feature-catalog.md stay removed.

---

## How to answer

Reply with the decision id and the chosen option, for example: "D1 = A, D3 = A, D4 = A, D9 = A".
The answer is written here with status ANSWERED, the date, and a one line rationale, then
mirrored into docs/handoff/DECISIONS-LOG.md before Step 01 starts.
