# Spliit Contribution Ideas

Reference document for potential contributions to the [spliit-app/spliit](https://github.com/spliit-app/spliit) open-source project.

---

## Strategy

1. Start with a small, self-contained PR to build trust with the maintainer.
2. Follow up with the flagship auth contribution once established.
3. Coordinate with existing PR authors where applicable.

---

## Tier 1: Most Requested — Code Already Exists in Settlr

### 1. Analytics / Charts by Category

- **Issue**: [#82 — Chart of total amount by category](https://github.com/spliit-app/spliit/issues/82)
- **Opened by**: The maintainer (scastiel) himself
- **Status**: Open, no PR
- **Effort**: Low (1–2 days)
- **Priority**: Start here — perfect first PR

Spliit has a Stats tab but no visual charts. Settlr already has:
- Pie chart by spending category (`src/components/analytics/spending-pie-chart.tsx`)
- Monthly bar chart trends (`src/components/analytics/monthly-bar-chart.tsx`)
- Summary cards (total spent, average per expense, top category)

**Approach**: Port the Recharts-based charts into Spliit's Stats tab. Adapt data fetching from Prisma instead of Supabase. Keep it self-contained so the PR stays focused.

---

### 2. Authentication / User Accounts

- **Issues**: [#257 — Authentication (Optional)](https://github.com/spliit-app/spliit/issues/257) (22 votes), [#76 — User Accounts](https://github.com/spliit-app/spliit/issues/76) (18 votes), [#336 — Auth before group creation](https://github.com/spliit-app/spliit/issues/336)
- **Existing PR**: [#495 — Group Cloud Sync](https://github.com/spliit-app/spliit/pull/495) by @antonio-ivanovski (pending review since Jan 2026)
- **Effort**: High (1–2 weeks)
- **Priority**: Flagship contribution — do this after charts PR is merged

This is the single most requested feature. The community wants:
- Optional login (email + OAuth) — not mandatory, backward-compatible
- Groups linked to accounts (no more URL-only sharing)
- "Who are you?" replaced with actual authenticated identity
- Protected groups that only members can access
- Cross-device sync (login on any device, see all your groups)

**What Settlr already has**: Full Supabase Auth (email, Google OAuth, password reset), session middleware, RLS, account deletion.

**Approach**: Since Spliit uses Prisma (not Supabase), use **better-auth** or **next-auth** instead. Key decisions:
- Auth should be optional (env var toggle) so self-hosters can keep the no-login experience
- Add a `User` model linked one-to-one with `Participant`
- Protected groups: only authenticated participants can view/edit
- Coordinate with PR #495 author first — collaborate or build on their work

**Unlocks other issues**: Cross-group balances (#19, #170), user permissions (#152), encryption at rest (#34).

---

### 3. Group Deletion

- **Issue**: [#9 — Group deletion](https://github.com/spliit-app/spliit/issues/9)
- **Status**: Open, no PR
- **Effort**: Low (few hours)

Spliit currently has no way to delete a group. Settlr has this (creator can delete). Implementation: cascade delete group → participants → expenses → expense_splits → activity. Add a confirmation dialog with the group name typed to confirm.

---

## Tier 2: Most Requested — Build from Scratch

### 4. Import from Splitwise

- **Issue**: [#22 — Import from Splitwise](https://github.com/spliit-app/spliit/issues/22)
- **Status**: Open, listed as "Possible incoming feature" in README
- **Effort**: Medium (3–5 days)

Many users want to migrate from Splitwise. Approach:
- Accept Splitwise CSV export (Settings → Export → CSV)
- Parse and map fields: description, amount, currency, date, category, payer, split participants
- Create group + participants + expenses in one Prisma transaction
- Handle edge cases: deleted participants, multiple currencies, reimbursements

---

### 5. Multi-Payer Support

- **Issue**: [#14 — Multi-payer support](https://github.com/spliit-app/spliit/issues/14)
- **Status**: Open
- **Effort**: Medium (3–5 days)

Currently only one person can pay per expense. Some expenses are jointly paid (e.g., two people splitting a hotel bill at checkout). Requires:
- Schema change: junction table or JSON array for payer-amount pairs
- Expense form UI: multi-select payer with amount per payer
- Balance calculation update in `getBalances()`

---

### 6. Recurring Expenses

- **Issue**: [#5 — Recurring expenses](https://github.com/spliit-app/spliit/issues/5)
- **Status**: Open, listed as "Possible incoming feature" in README
- **Effort**: Medium
- **Note**: Schema already has `recurrenceRule` and `RecurringExpenseLink` in Prisma — the backend groundwork exists, but the UI and automation logic are missing.

---

## Tier 3: Quick Wins

### 7. Improved PWA Support

- **Issue**: [#79 — Improve PWA Support](https://github.com/spliit-app/spliit/issues/79)
- Better offline caching with service worker
- Install prompts
- Push notifications for new expenses

### 8. Custom AI API Endpoint

- **Issue**: [#309 — Allow custom API for AI features](https://github.com/spliit-app/spliit/issues/309)
- Let users configure Ollama, local LLMs, or other OpenAI-compatible APIs for receipt scanning and category extraction instead of requiring an OpenAI key.
- Mostly configuration/env var changes + a flexible API client.

---

## Recommended Order

| # | Contribution | Effort | Why this order |
|---|---|---|---|
| 1 | Charts by category (#82) | Low | Build trust, maintainer-requested, self-contained |
| 2 | Group deletion (#9) | Low | Quick win, long-standing request |
| 3 | Authentication (#257/#76) | High | Flagship, highest community demand |
| 4 | Import from Splitwise (#22) | Medium | Key migration path, listed in README roadmap |
| 5 | Recurring expenses (#5) | Medium | Schema groundwork exists, README roadmap item |
| 6 | Multi-payer (#14) | Medium | Niche but valuable |

---

## References

- Upstream repo: https://github.com/spliit-app/spliit
- Discord: https://discord.gg/YSyVXbwvSY
- Your fork: https://github.com/vmnit/spliit
- Settlr (your codebase to port from): `/home/vivek/WSLProjects/MobileApps/settlr`
- Auth PR to coordinate with: https://github.com/spliit-app/spliit/pull/495
