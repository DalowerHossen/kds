# path: docs/plan/05-performance-budget.md

# KD PAY LLC - PERFORMANCE BUDGET (SUPABASE AND HOSTING LOAD CONTROL)

Two hard constraints drive this document:

1. The site must load fast on mobile networks in Asia.
2. Database and edge function load must stay low on Supabase and on the hosting platform.

Every step implements against this budget. A page that exceeds its query budget is a defect,
even when it renders correctly.

---

## 1. Page query budgets (round trips to PostgreSQL per render)

| Route group | Page | Max queries | Strategy |
| --- | --- | --- | --- |
| Public | home, features, pricing, legal | 0 to 1 | Static render, content from cache tag, one query only for dynamic pricing |
| Public | blog index | 1 | Cursor query with cover metadata joined |
| Public | blog post | 1 | Single row plus author, no per tag query |
| Portal | invoice view /i/[token] | 2 | Token plus invoice with lines in one RPC, branding from cached tenant config |
| Portal | hub /hub/[token] | 1 | Client documents list in one query with computed balances |
| Auth | login, signup | 2 | Rate limit consume plus user lookup |
| Dashboard | home | 1 | `fn_report_dashboard()` returns all cards in one row |
| Dashboard | invoice list | 2 | List query plus one count through window function |
| Dashboard | invoice detail | 2 | Invoice with lines, plus allowed action set |
| Dashboard | client list and detail | 2 | List plus aggregate balance |
| Dashboard | payments list | 2 | List plus one aggregate |
| Dashboard | settings | 1 | Cached tenant config |
| Dashboard | KYC | 2 | Case plus documents |
| Admin | approvals inbox | 2 | Page of requests plus badge count |
| Admin | tenant list | 2 | Keyset page plus total from same query |
| Admin | processors | 1 | Registry from code, credentials metadata only |
| Admin | fraud / review queue | 2 | Page plus count |
| Admin | audit explorer | 2 | Page plus chain head |
| Admin | revenue | 1 | `fn_report_revenue(range)` single row |

Rules:
- A second query is allowed only when it cannot be joined, and must be justified in the handoff
- No page loops over rows to fetch children (no N+1, measured by counting queries in the log)
- Client components never fetch data that the server already rendered

## 2. Query design rules

- Explicit column lists, never select star
- Keyset pagination on (created_at desc, id desc), page size 25 default, 100 max
- Filters and sorting resolved on the server; the browser never sorts a large set
- Aggregates through PostgreSQL functions, not application side summation
- Denormalized counters maintained by triggers: invoice totals, paid amounts, balances,
  client open balance, unread notification count, approval badge count
- Existence checks with `exists` instead of count where the interface only needs a boolean
- Batch writes through a single RPC when more than ten rows change together
- All reads within one request go through a request scoped loader with memoization, so two
  components asking for the same tenant config cause one query
- Read only transactions for list screens
- Statement timeout 5 seconds on web requests, 60 seconds on jobs

## 3. Index requirements per hot path

| Query | Required index |
| --- | --- |
| Invoices by company and date | idx_invoices_company_created on (company_id, created_at desc) where deleted_at is null |
| Invoice by number | unique (company_id, invoice_number) |
| Invoice by client | (company_id, client_id, created_at desc) |
| Payments by invoice | (company_id, invoice_id, created_at desc) |
| Payment by provider id | unique (provider_key, provider_payment_id) |
| Webhook dedupe | unique (provider_key, provider_event_id) |
| Clients search | (company_id, lower(email)) unique, trgm on name |
| Portal token | unique (token_hash) |
| Audit range | (company_id, created_at desc, id) |
| Jobs claim | partial index on (run_at) where status = 'queued' |
| Rate limit bucket | primary key (bucket_key, window_start) |
| Ledger by account | (company_id, account, created_at desc) |
| Approvals inbox | partial index where status = 'pending' |
| KYC queue | (status, submitted_at) partial index where status in ('submitted','in_review') |

Index policy: an index is added with the migration that introduces the query, and reviewed at
Step 16 with `EXPLAIN (ANALYZE, BUFFERS)` output recorded in docs/PERFORMANCE-REPORT.md.

## 4. Caching layers

1. Request memoization through React `cache()` for tenant config, permissions and registry
2. Route segment cache with tag based revalidation: tags `tenant:{id}:settings`,
   `tenant:{id}:invoices`, `post:{slug}`, `pricing`, `nav`
3. Data cache for reference data: countries, payout method catalog, platform settings, TTL 300
   seconds with write invalidation
4. CDN headers: immutable for hashed assets, `s-maxage=60, stale-while-revalidate=300` for
   public HTML, `no-store` for authenticated and portal pages
5. Realtime only for the approval inbox and the current invoice payment status
6. No cache for anything carrying a token or a secret

Invalidation map lives in `lib/cache/tags.ts`; every write states the tags it invalidates.

## 5. Rendering and bundle budget

- Server Components by default; `use client` only for interactive islands
- Initial JS per route group under 200 KB gzip; public marketing routes under 120 KB
- No chart library, PDF library or rich text editor on public routes
- Dynamic import for the PDF preview, editor and chart widgets
- Fonts: two families maximum, subset, `display: swap`, preloaded
- Images: next/image with AVIF then WebP, explicit dimensions, lazy below the fold
- Icons: tree shaken lucide imports only
- Prefetch limited to the primary navigation, not list rows

## 6. Edge and server workload budget

- Middleware performs local JWT verification and header work only; no database query in
  middleware. Rate limiting that needs storage runs in route handlers, not in middleware
- Server actions: 1 transaction where possible, no cross service chatty calls
- PDF generation and exports always run in the job queue, never inside a request
- Webhook handlers: verify signature, insert into webhook_events, respond 200, process in the
  queue. No provider call inside the webhook request
- Provider API calls: 8 second timeout, one retry with jitter, circuit breaker per provider
- Cron jobs: leader lock, bounded batches, off peak window, and a run record

## 7. Measurement gates

| Gate | Target | Enforced at |
| --- | --- | --- |
| Lighthouse performance on public pages | 90 or above | Step 16 |
| LCP on 4G mobile | under 2.5 seconds | Step 16 |
| INP | under 200 milliseconds | Step 16 |
| CLS | under 0.1 | Step 16 |
| Dashboard server render | under 400 milliseconds p50 | Step 16 |
| Portal invoice render | under 300 milliseconds p50 | Step 16 |
| Queries per page | equals budget table | every step |
| Slow query count above 1 second | zero in normal traffic | every step |
| Payment intent creation | under 800 milliseconds excluding provider latency | Step 11 |

Evidence is recorded in docs/PERFORMANCE-REPORT.md with the command, the route and the raw
numbers.

## 8. Database load control

- Connection pooling through the Supabase transaction pooler for server code
- No long lived transactions in request paths
- Batch inserts for webhook and audit bursts
- Audit writes through one insert per mutation with a single statement
- Retention jobs delete in bounded batches to avoid bloat
- `pg_stat_statements` reviewed at Step 16 for the top twenty statements
- Vacuum and analyze tuned per table with heavy churn (jobs, rate limits, audit)

## 9. Anti patterns rejected in review

1. Fetching the whole list then filtering in the browser
2. Five separate count queries for five dashboard cards
3. Realtime subscription per list row
4. Awaiting a provider API inside a server action that renders a page
5. Loading a PDF library on a public page
6. Serial awaits that could be one joined query
7. Recomputing invoice totals on every read instead of reading the maintained counter
8. Client side money arithmetic
9. Unbounded `select *` exports in a request
10. Cache with a key that omits the tenant id
