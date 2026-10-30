# path: docs/plan/02-chat-playbook.md

# KD PAY LLC - CHAT PLAYBOOK (IDENTICAL INSTRUCTION FOR EVERY STEP CHAT)

This file exists so every new chat receives the same instruction set and produces code that
fits the previous step without conflicts, duplicates or rework.

You do not rewrite the instruction for each step. You paste the same block and change only the
step number.

---

## 1. How the 16 chats work

- Chat 01 implements STEP 01, chat 02 implements STEP 02, and so on up to chat 16.
- One chat equals one step. A step is never split across two chats, and a chat never starts a
  second step.
- Inside a chat, the step is delivered sub-step by sub-step in the order defined in
  docs/plan/01-step-map.md.
- If a step is too large for one response, the chat continues with the continuation line shown
  in section 5. The rule set does not change.
- Every chat starts from the repository, never from memory of the previous chat.
- Every chat ends with a commit per sub-step and a handoff document.

## 2. What to paste at the start of every chat

Replace only <NN> with the two digit step number. Everything else stays identical.

```
KD PAY LLC - STEP <NN> OF 16 - IMPLEMENT NOW

You are the senior engineer for KD PAY LLC, an enterprise multi-tenant SaaS invoicing and
payment platform, Asia first launch, USD only, English only.

Read these files from the repository in this exact order, from the integration branch:
1. docs/plan/00-charter.md
2. docs/plan/01-step-map.md (only the STEP <NN> section and the Overview table)
3. docs/plan/02-chat-playbook.md (this file)
4. docs/plan/03-data-model.md (tables owned by STEP <NN>)
5. docs/plan/04-feature-catalog.md (features in scope for STEP <NN>)
6. docs/plan/05-performance-budget.md
7. docs/plan/07-file-ownership-map.md (paths owned by STEP <NN>)
8. docs/handoff/DECISIONS-LOG.md
9. the handoff document of STEP <NN-1>, that is docs/handoff/STEP-<NN-1>-HANDOFF.md

Then implement ONLY STEP <NN>, in sub-steps <NN>.1 up to the last sub-step listed for that step,
in order, in this chat.

Non negotiable rules, all 20 of them, restated:
1. English only, ASCII only. No other script anywhere. No emoji in code or content.
2. USD only. Every money column numeric(18,4). decimal.js only. Never JavaScript Number.
3. Text and structured data in Supabase PostgreSQL. Files in encrypted storage with a
   storage_provider column.
4. No test files of any kind.
5. No unfinished markers of any kind, no announcement pages for unbuilt features, no filler text.
6. No `any`. TypeScript strict. Explicit return types.
7. No console output. Structured logging through lib/core/logger.ts.
8. Every file starts with the path comment: // path: relative/path.
9. Every table has id UUID v7, company_id, created_at, updated_at, deleted_at.
10. Every table has RLS enabled with a default deny policy.
11. Every mutation writes an audit log entry.
12. Every API and every form validates with Zod on client and server.
13. Every input is sanitized (XSS, SQL injection, CSRF, SSRF, path traversal, header injection).
14. Every payment carries an idempotency key.
15. Every editable record has a version column and optimistic locking.
16. Roles: super_admin > ceo > admin_staff (compliance_kyc, support, content) > owner.
    Clients are tokenized with no login. Owners cannot invite staff.
17. Approval chain required for every sensitive action per config/approval-rules.ts.
18. Never name local wallet brands. Use "Global Payout Partners" and
    "Local Payment Methods via Licensed Partners".
19. Zero trust and zero tolerance. Nothing is trusted by default.
20. VPN, proxy, Tor and datacenter detection on signup, payment, KYC upload and admin.

Additional constraints for this project:
- Do not create, modify or delete files owned by another step. Extend behaviour only through
  the module files you own, plus documented registry and navigation entries.
- Do not rewrite migrations from earlier steps and do not edit earlier migrations.
- Do not add dependencies outside the approved list for this step.
- Every new page must implement all four states: loading, empty, error, success.
- Every list uses keyset pagination and server side filtering.
- Every page must respect the query budget in docs/plan/05-performance-budget.md.
- Card data never touches our code or DOM. Only provider hosted fields or redirects.
- For every payment processor in this step: open the provider's own official documentation on
  the internet in this session and follow the current version. Coding from memory is not
  allowed. Write docs/providers/<provider>.md with the URLs used, the date observed,
  the endpoints and payloads implemented, the signature algorithm and the sandbox source.

Definition of done for this chat:
- All sub-steps of STEP <NN> implemented, nothing partial
- npm run verify passes (guards, typecheck, lint, build)
- Migrations include down migrations and are idempotent
- .env.example updated with every new variable
- docs/handoff/STEP-<NN>-HANDOFF.md written with the nine required sections
- docs/handoff/DECISIONS-LOG.md appended with every decision and deviation
- One commit per sub-step: feat(step-<NN>.<k>): summary
- Final message lists: sub-steps done, files added, tables added, routes added, env vars added,
  contracts exposed to later steps, and the exact starting point of the next step

Start now with sub-step <NN>.1. Do not summarise the plan back to me. Do not ask questions that
the repository already answers. Implement.
```

## 3. The sixteen paste values

| Chat | Paste the block with | Step title |
| --- | --- | --- |
| 01 | STEP 01 | Foundation, toolchain, design system base, guards |
| 02 | STEP 02 | Database core: tenancy, RLS, audit, idempotency, queue, settings |
| 03 | STEP 03 | Security core: sanitizing, headers, CSP, rate limit, network trust, encryption |
| 04 | STEP 04 | Authentication, lockout, 2FA, sessions, step-up |
| 05 | STEP 05 | RBAC, permissions, approval chain engine, admin shell |
| 06 | STEP 06 | Storage and files with AES-256-GCM |
| 07 | STEP 07 | Tenant onboarding, company profile, branding, settings |
| 08 | STEP 08 | KYC and KYB, screening, risk tiers, limits |
| 09 | STEP 09 | Clients CRM and tokenized client portal |
| 10 | STEP 10 | Invoicing core, numbering, PDF engine, sales tax on invoice |
| 11 | STEP 11 | Payments core engine |
| 12 | STEP 12 | Processors A: NMI dual mode, Stripe Connect |
| 13 | STEP 13 | Processors B: Payoneer, Airwallex, Rainforest, payouts, reconciliation |
| 14 | STEP 14 | Checkout, payment links, email system, templates, activity emails |
| 15 | STEP 15 | Super admin console |
| 16 | STEP 16 | Public site, blog, support, SEO, performance, deploy, go-live |

## 4. Expected response shape (so you can verify quality immediately)

Every step response must contain, in this order:

1. Sub-step heading and a one line objective
2. Complete files, each starting with its path comment
3. Migration files with matching down migration
4. Any environment variable added
5. Verification block: the commands run and their result
6. Handoff document content

A response missing any of these is incomplete and the chat continues with the continuation
line.

## 5. Continuation line (when a step does not finish in one response)

```
Continue STEP <NN> from sub-step <NN>.<k>. Same rules, same files, same chat. Finish the step and
produce the handoff document.
```

## 6. Closing line for the chat (optional, when you want the PR)

```
Commit the work of this step per sub-step, open a pull request from this branch into
integration with the title "STEP <NN>: <title>", and paste the pull request link.
```

## 7. Conflict and duplicate prevention (why this order works)

1. Vertical ownership. Every directory and every table has exactly one owning step, listed in
   docs/plan/07-file-ownership-map.md. Later steps import, they do not edit.
2. Append only registries. Navigation, permissions, adapters, approval rules and capabilit
   entries are added through per module files that a single aggregation file imports. No step
   rewrites the aggregator except its owner (Step 01 for nav, Step 05 for permissions,
   Step 11 for the processor registry).
3. Migration ordering. Timestamps are generated at creation, which is always after the previous
   step, so numbering collisions cannot happen. Later steps never edit earlier migrations; they
   add columns with new migrations.
4. Contract first. When a step needs something a later step provides (for example the invoice
   email job), the contract is defined by the earlier step (job kind, payload schema) and the
   consumer is implemented in the owning step. The earlier step enqueues real rows, it never
   writes a stub function.
5. Single source of truth per concern:
   nav map for routes, permissions for access, approval rules for chains, money module for
   arithmetic, sanitizer for input, audit writer for history, storage service for files,
   payments registry for processors, email service for messages, settings service for config.
6. No parallel code paths. If a later step needs a variation, it adds a capability flag to the
   existing service instead of a second service.
7. Handoff before start. No step starts without the previous handoff document and the decisions
   log, so build time is never spent rediscovering decisions.

## 8. Review gate before merging each step

Run and confirm, then merge the pull request into integration:

```
git switch integration
git pull
git switch step/<NN>-<slug>
git rebase integration
npm ci
npm run guard
npm run typecheck
npm run lint
npm run build
```

Manual checks: RLS enabled on every new table, down migration present, no non-ASCII characters,
no unfinished markers, no test files, no any, no console output, secrets not printed in logs,
and the handoff document is present.

## 9. What never changes between chats

- The rules block in section 2
- The definition of done
- The handoff structure
- The ownership map
- The money, sanitize, audit, RLS and idempotency patterns

What changes between chats is only the step number, which selects the scope from the step map,
the tables from the data model, the features from the catalog and the paths from the ownership
map.

## 10. Quality traps to avoid inside any chat

- Do not invent a table that the data model assigns to a different step
- Do not put business logic in a route handler or a component; services own logic
- Do not compute money in the browser
- Do not fetch the same row twice in one request; use one typed loader per request
- Do not add a client component where a server component is enough
- Do not add a realtime subscription that is not on the approved list
- Do not print sensitive values in an error message or a log line
- Do not allow a payment to continue when routing resolves to no provider
- Do not allow processor enablement without a passing connection test
- Do not let an owner see another company row, ever, including by id guessing
