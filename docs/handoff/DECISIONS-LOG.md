# path: docs/handoff/DECISIONS-LOG.md

# KD PAY LLC - DECISIONS LOG (APPEND ONLY)

Every decision, deviation and ownership transfer is appended here by the step chat that makes it.
Entries are never edited. A reversal is a new entry that references the old one.

Format:

```
## YYYY-MM-DD - STEP NN - <short title>
Decision: <what was decided>
Reason: <why, in one or two lines>
Impact: <files, tables, routes, contracts affected>
Refs: <decision id from 06-open-decisions.md when applicable>
```

---

## 2026-10-30 - STEP 00 - Planning pack adopted

Decision: docs/plan is the governing specification. MASTER_PROMPT.md and FEATURE_LIST.md are
retained as history and are superseded.
Reason: the earlier documents describe KD SOLUTION IT with removed modules and sixteen processors.
Impact: every step reads the plan folder first.

## 2026-10-30 - STEP 00 - Processor list reduced to five

Decision: NMI, Stripe Connect, Payoneer, Airwallex and Rainforest only. Custom adapter removed.
Reason: owner instruction. Five adapters keep the security surface reviewable.
Impact: lib/payments/adapters contains five adapters and one registry.
Refs: D17

## 2026-10-30 - STEP 00 - Modules removed

Decision: WhatsApp, SMS, payroll, HR, accounting, tax filing and tax reports, social automation,
owner staff seats and the mandatory Google Drive dependency are out of scope. Sales tax is still
collected on invoices.
Reason: owner instruction.
Impact: catalog and charter sections 3 and 4.
Refs: D18

## 2026-10-30 - STEP 00 - Role model fixed

Decision: super_admin, ceo, admin_staff with sub roles compliance_kyc, support and content, and
owner. Clients are tokenized with no login. Owners cannot invite staff.
Reason: owner instruction and least privilege.
Impact: config/permissions, charter section 5.
Refs: D2

## 2026-10-30 - STEP 00 - Sixteen step delivery model

Decision: the build is delivered in sixteen sequential chats, one step per chat, sub-steps in
order inside the chat, one handoff document per step.
Reason: each chat must fit a complete step without truncation, and the handoff keeps state.
Impact: docs/plan/01-step-map.md and docs/plan/02-chat-playbook.md.
