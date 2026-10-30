# path: docs/plan/README.md

# KD PAY LLC - PLANNING PACK (ENTRY POINT)

This folder is the operating plan for building KD PAY LLC in sixteen sequential chats.
It contains no application code. It is the contract every step chat follows.

## Read in this order

| # | File | What it decides |
| --- | --- | --- |
| 1 | 00-charter.md | Identity, scope in and out, roles, the twenty rules, payment policy, lock policy, security and performance doctrine, branch model, definition of done |
| 2 | 01-step-map.md | The sixteen steps, their sub-steps, tables, routes and exit criteria |
| 3 | 02-chat-playbook.md | The exact block pasted into every new chat, the expected response shape, and the conflict prevention rules |
| 4 | 03-data-model.md | Column contract, naming, RLS, audit, idempotency, every table by owning step, enums, migration rules |
| 5 | 04-feature-catalog.md | The verified feature list with ids, the removed list, and the pending decisions |
| 6 | 05-performance-budget.md | Query budgets per page, caching, indexes, bundle budget, measurement gates |
| 7 | 06-open-decisions.md | Decisions that must be answered, with recommendations and impact |
| 8 | 07-file-ownership-map.md | Which step owns which path, and the extension points that prevent collisions |

## Working method

1. Answer the open decisions, at minimum D1, D2, D3 and D4.
2. Open a new chat for Step 01, paste the block from 02-chat-playbook.md with the number 01.
3. When Step 01 merges into the integration branch, open the next chat for Step 02.
4. Never start a step twice and never merge two steps in one chat.
5. Each chat ends with a handoff document in docs/handoff/ and a merged pull request.

## What makes this plan conflict free

- One owner per path and per table
- Append only registries for navigation, permissions, processors, job kinds and email templates
- Contracts defined by the step that enqueues work, consumers implemented by the step that owns
  the channel
- Handoff documents carry the exact state so the next chat never guesses

## Legacy documents

MASTER_PROMPT.md and FEATURE_LIST.md describe the earlier KD SOLUTION IT scope, which included
payroll, HR, accounting, tax filing, WhatsApp and social automation, and sixteen processors.
They are retained as history only. Where they disagree with this folder, this folder wins.
