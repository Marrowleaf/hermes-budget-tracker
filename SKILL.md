---
name: budget-tracker
version: 1.0.0
description: Log expenses via natural language, categorize transactions smartly, generate monthly budget reports in Obsidian, and track spending targets.
author: hermes
tags:
  - finance
  - budget
  - obsidian
  - productivity
  - expenses
commands:
  - name: log
    description: "Log an expense from natural language (e.g., 'spent £12 at Tesco', 'paid £49.99 for Netflix')"
    usage: "budget-tracker log <natural language expense>"
    examples:
      - "budget-tracker log spent £12 at Tesco"
      - "budget-tracker log paid $49.99 for Netflix"
      - "budget-tracker log €8.50 coffee at Starbucks"
      - "budget-tracker log spent £35 on petrol"
  - name: report
    description: "Generate a monthly budget report in Obsidian"
    usage: "budget-tracker report [YYYY-MM]"
    examples:
      - "budget-tracker report"
      - "budget-tracker report 2026-04"
  - name: edit
    description: "Edit a previously logged transaction"
    usage: "budget-tracker edit <transaction-id> <field>=<value>"
    examples:
      - "budget-tracker edit txn-20260501-1 amount=15"
      - "budget-tracker edit txn-20260501-1 category=food"
      - "budget-tracker edit txn-20260501-1 description=Weekly groceries"
  - name: delete
    description: "Delete a previously logged transaction"
    usage: "budget-tracker delete <transaction-id>"
    examples:
      - "budget-tracker delete txn-20260501-1"
  - name: targets
    description: "Configure monthly spending targets per category"
    usage: "budget-tracker targets [set <category> <amount>] [list] [delete <category>]"
    examples:
      - "budget-tracker targets set food 300"
      - "budget-tracker targets set subscriptions 50"
      - "budget-tracker targets list"
      - "budget-tracker targets delete entertainment"
  - name: list
    description: "List recent transactions, optionally filtered by category or date"
    usage: "budget-tracker list [category=<cat>] [from=YYYY-MM-DD] [to=YYYY-MM-DD] [limit=N]"
    examples:
      - "budget-tracker list"
      - "budget-tracker list category=food"
      - "budget-tracker list from=2026-04-01 to=2026-04-30"
  - name: summary
    description: "Show a quick spending summary for a period"
    usage: "budget-tracker summary [YYYY-MM]"
    examples:
      - "budget-tracker summary"
      - "budget-tracker summary 2026-04"
---

# Budget Tracker

Track personal expenses in Obsidian using natural language input. Expenses are stored as Markdown tables, organized by month, with smart auto-categorization and configurable spending targets.

## Configuration

All configuration and data live inside the Obsidian vault. The skill uses these paths:

| Path | Purpose |
|------|---------|
| `~/obsidian-vault/3-Resources/budget-tracker/config.md` | Settings, targets, defaults |
| `~/obsidian-vault/3-Resources/budget-tracker/YYYY-MM.md` | Monthly expense files |
| `~/obsidian-vault/2-Areas/Finance/Budget Reports/YYYY-MM.md` | Monthly budget reports |

Adjust `vault_path` in the config if your vault lives elsewhere.

## How It Works

### 1. Logging Expenses

When the user says something like **"spent £12 at Tesco"** or **"paid $49.99 for Netflix"**, the skill:

1. **Parses** the natural language input to extract:
   - **Amount**: the monetary value (12, 49.99)
   - **Currency**: derived from the symbol (£ → GBP, $ → USD, € → EUR)
   - **Description**: the vendor or item ("Tesco", "Netflix subscription")

2. **Categorizes** the transaction using keyword matching and context rules (see Categories below).

3. **Generates** a transaction ID: `txn-YYYYMMDD-N`

4. **Appends** a row to the current month's expense table in `~/obsidian-vault/3-Resources/budget-tracker/YYYY-MM.md`.

### 2. Monthly Expense File Format

Each month gets its own Markdown file. Example (`2026-05.md`):

```markdown
---
type: budget-month
month: "2026-05"
currency: GBP
created: 2026-05-01T07:20:00
---

## Expenses

| ID | Date | Amount | Currency | Category | Description |
|----|------|--------|----------|----------|-------------|
| txn-20260501-1 | 2026-05-01 | 12.00 | GBP | food | Tesco groceries |
| txn-20260501-2 | 2026-05-01 | 49.99 | GBP | subscriptions | Netflix subscription |
| txn-20260503-1 | 2026-05-03 | 35.00 | GBP | transport | Petrol fill-up |
| txn-20260505-1 | 2026-05-05 | 8.50 | GBP | food | Starbucks coffee |
```

### 3. Smart Categories

The skill auto-categorizes transactions using these keyword rules:

| Category | Keywords / Patterns |
|----------|-------------------|
| `food` | tesco, sainsbury, asda, morrisons, aldi, lidl, waitrose, m&s, marks & spencer, grocery, groceries, food, takeaway, restaurant, cafe, coffee, starbucks, costa, pizza, mcdonalds, kfc, subway, deliveroo, just eat, uber eats, meal, lunch, dinner, breakfast, bakery, butcher |
| `bills` | rent, mortgage, council tax, water, gas, electricity, electric, energy, bt, virgin media, broadband, internet, phone bill, mobile bill, insurance, tax, hmrc, TV licence |
| `subscriptions` | netflix, spotify, amazon prime, disney, apple, icloud, youtube premium, audible, subscription, membership, patreon, github, medium, times, guardian |
| `transport` | petrol, diesel, fuel, bus, train, tube, metro, uber, lyft, taxi, oyster, travelcard, parking, toll, car wash, mot, servicing, bus pass, railcard |
| `entertainment` | cinema, movie, film, concert, gig, theatre, theater, game, steam, xbox, playstation, nintendo, ticket, bowling, escape room, night out, pub, bar, drink |
| `shopping` | amazon, argos, john lewis, next, primark, h&m, zara, ebay, clothes, clothing, shoes, electronics, gadget, diy, b&q, homebase, ikea, furnitur |
| `health` | pharmacy, boots, gp, dental, dentist, optician, gym, fitness, prescription, medicine, health, medical, therapy, counselling |
| `education` | book, books, course, udemy, coursera, skillshare, tuition, class, workshop, seminar, training |
| `gifts` | gift, birthday, christmas, wedding, present, card, flowers |
| `savings` | savings, deposit, isa, pension, investment, vanguard, s&p, stocks, shares |
| `other` | (anything that doesn't match the above) |

### 4. Currency Support

The skill recognises these currency symbols and maps them:

| Symbol | Code | Name |
|--------|------|------|
| £ | GBP | British Pound |
| $ | USD | US Dollar |
| € | EUR | Euro |

If no symbol is provided, the default currency from `config.md` is used (defaults to GBP).

### 5. Spending Targets

Targets are stored in the config file and referenced in monthly reports. Example config:

```markdown
---
type: budget-config
default_currency: GBP
vault_path: ~/obsidian-vault
---

## Spending Targets

| Category | Monthly Target | Currency |
|----------|--------------|----------|
| food | 300 | GBP |
| bills | 500 | GBP |
| subscriptions | 50 | GBP |
| transport | 150 | GBP |
| entertainment | 100 | GBP |
| shopping | 200 | GBP |
| health | 80 | GBP |
| education | 50 | GBP |
| gifts | 50 | GBP |
| savings | 200 | GBP |
```

### 6. Monthly Budget Reports

Using `budget-tracker report` (or `budget-tracker report YYYY-MM`), the skill generates a full Obsidian report at `~/obsidian-vault/2-Areas/Finance/Budget Reports/YYYY-MM.md`.

**Report template:**

```markdown
---
type: budget-report
month: "YYYY-MM"
currency: GBP
generated: YYYY-MM-DDTHH:MM:SS
---

# Budget Report — Month Year

## Summary

- **Total spent**: £XXX.XX
- **Total budget**: £XXX.XX
- **Remaining budget**: £XXX.XX
- **Budget utilisation**: XX%

## Category Breakdown

| Category | Spent | Target | Remaining | Status |
|----------|-------|--------|-----------|--------|
| food | £XXX.XX | £300 | £XXX.XX | ✅ / ⚠️ / 🔴 |
| bills | £XXX.XX | £500 | £XXX.XX | ✅ / ⚠️ / 🔴 |
| subscriptions | £XXX.XX | £50 | £XXX.XX | ✅ / ⚠️ / 🔴 |
| transport | £XXX.XX | £150 | £XXX.XX | ✅ / ⚠️ / 🔴 |
| entertainment | £XXX.XX | £100 | £XXX.XX | ✅ / ⚠️ / 🔴 |
| shopping | £XXX.XX | £200 | £XXX.XX | ✅ / ⚠️ / 🔴 |
| health | £XXX.XX | £80 | £XXX.XX | ✅ / ⚠️ / 🔴 |
| education | £XXX.XX | £50 | £XXX.XX | ✅ / ⚠️ / 🔴 |
| gifts | £XXX.XX | £50 | £XXX.XX | ✅ / ⚠️ / 🔴 |
| savings | £XXX.XX | £200 | £XXX.XX | ✅ / ⚠️ / 🔴 |

> **Status legend**: ✅ Under budget · ⚠️ Within 10% of limit · 🔴 Over budget

## Top Expenses

| # | Date | Amount | Category | Description |
|---|------|--------|----------|-------------|
| 1 | YYYY-MM-DD | £XXX.XX | category | description |
| 2 | YYYY-MM-DD | £XXX.XX | category | description |
| 3 | YYYY-MM-DD | £XXX.XX | category | description |
| 4 | YYYY-MM-DD | £XXX.XX | category | description |
| 5 | YYYY-MM-DD | £XXX.XX | category | description |

## Daily Spending Trend

(Bar chart placeholder — visualise spend per day across the month)

## Notes

- (Auto-generated insights, e.g., "You spent 20% more on food this month compared to last month.")
- (Highlight any category that exceeded its target.)
```

**Status logic:**
- ✅ — Spent ≤ target
- ⚠️ — Spent > 90% of target but ≤ target
- 🔴 — Spent > target

### 7. Retroactive Edits

Use `budget-tracker edit` to modify a previously logged transaction. The skill:

1. Locates the transaction by ID across all monthly files.
2. Updates the specified field(s).
3. Re-writes the row in the Markdown table.

Editable fields:
- `amount` — new numeric amount
- `currency` — GBP, USD, or EUR
- `category` — override the auto-categorised category
- `description` — change the description text
- `date` — move the transaction to a different date (may move it to a different monthly file)

Use `budget-tracker delete <id>` to remove a transaction entirely.

## Natural Language Parsing Rules

The skill understands many phrasings:

| Input Pattern | Extracted |
|---------------|-----------|
| "spent £12 at Tesco" | amount=12, currency=GBP, description="Tesco" |
| "paid $49.99 for Netflix" | amount=49.99, currency=USD, description="Netflix" |
| "€8.50 coffee at Starbucks" | amount=8.50, currency=EUR, description="Starbucks coffee" |
| "rent £950" | amount=950, currency=GBP, description="Rent" |
| "spent 25 on petrol" | amount=25, currency=<default>, description="Petrol" |
| "paid the electric bill, £67.20" | amount=67.20, currency=GBP, description="Electric bill" |
| "£3.50 bus fare" | amount=3.50, currency=GBP, description="Bus fare" |
| "bought groceries for £42" | amount=42, currency=GBP, description="Groceries" |
| "Netflix subscription £10.99" | amount=10.99, currency=GBP, description="Netflix subscription" |

### Parsing Algorithm

1. **Extract currency symbol**: scan for £, $, or € adjacent to a number.
2. **Extract amount**: the number closest to the currency symbol. Handle decimals.
3. **Extract description**: remaining text after removing the amount/currency fragment. Apply these transforms:
   - Remove filler words: "spent", "paid", "for", "at", "on", "the", "bought", "cost"
   - Trim whitespace, capitalise first letter.
4. **If no date mentioned**, default to today's date.
5. **If no currency symbol found**, use default from config.
6. **Categorise** by matching description against category keywords.

## Implementation Checklist

When executing a budget-tracker command, follow these steps:

### `log` command
1. Parse the natural language input to extract amount, currency, description, and date.
2. Auto-categorize using keyword matching.
3. Determine the target month file path: `~/obsidian-vault/3-Resources/budget-tracker/YYYY-MM.md`
4. If the file doesn't exist, create it with the frontmatter and table header.
5. Append the transaction row to the Markdown table.
6. Confirm to the user with a summary: category, amount, and description.

### `report` command
1. Read the specified month's expense file (default: current month).
2. Read the config file for targets.
3. Calculate totals per category, compare to targets.
4. Determine status (✅/⚠️/🔴) for each category.
5. Generate the report file at `~/obsidian-vault/2-Areas/Finance/Budget Reports/YYYY-MM.md`.
6. Include top 5 expenses sorted by amount.
7. Compare to previous month if data exists (add to Notes).

### `edit` command
1. Find the transaction ID in the monthly files.
2. Parse the field=value pairs.
3. If `date` is changed, move the row to the correct monthly file.
4. Otherwise, update the row in place.
5. Confirm the change to the user.

### `delete` command
1. Find and remove the transaction row.
2. Confirm deletion.

### `targets` command
1. `set`: update or add a row in the config targets table.
2. `list`: display the current targets table.
3. `delete`: remove a target row.

### `list` command
1. Read the relevant monthly file(s).
2. Filter by category and/or date range if specified.
3. Display matching transactions.

### `summary` command
1. Aggregate spending by category for the given month.
2. Display a compact summary with totals.

## File Templates

### Config file (`~/obsidian-vault/3-Resources/budget-tracker/config.md`)

```markdown
---
type: budget-config
default_currency: GBP
vault_path: ~/obsidian-vault
---

## Spending Targets

| Category | Monthly Target | Currency |
|----------|--------------|----------|
| food | 300 | GBP |
| bills | 500 | GBP |
| subscriptions | 50 | GBP |
| transport | 150 | GBP |
| entertainment | 100 | GBP |
| shopping | 200 | GBP |
| health | 80 | GBP |
| education | 50 | GBP |
| gifts | 50 | GBP |
| savings | 200 | GBP |
```

### Monthly expense file (`~/obsidian-vault/3-Resources/budget-tracker/YYYY-MM.md`)

```markdown
---
type: budget-month
month: "YYYY-MM"
currency: GBP
created: YYYY-MM-DDTHH:MM:SS
---

## Expenses

| ID | Date | Amount | Currency | Category | Description |
|----|------|--------|----------|----------|-------------|
```

### Monthly report (`~/obsidian-vault/2-Areas/Finance/Budget Reports/YYYY-MM.md`)

See the full report template in section 6 above.

## Notes

- All data is stored as pure Markdown — fully portable and Obsidian-native.
- The `ID` column uses the format `txn-YYYYMMDD-N` where N is a daily sequence number.
- Files follow the PARA structure: Resources for raw data, Areas for reports.
- Reports are regenerated fresh each time; edits to expenses are reflected in subsequent reports.
- Currency mixing is supported within a single month (reports show per-currency breakdowns).