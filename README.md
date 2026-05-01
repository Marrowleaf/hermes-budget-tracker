# Budget Tracker

> Track personal finances, expenses, and savings goals with Obsidian integration and automated weekly reports.

[![License: MIT](https://img.shields.io/badge/License-MIT-blue.svg)](https://github.com/Marrowleaf/hermes-budget-tracker)

## Features

- Natural language expense logging ("spent £12 on lunch", "rent of £850")
- Category-based expense tracking with custom categories
- Monthly budget monitoring with overspend alerts
- Savings goals with progress tracking
- Weekly and monthly financial summaries as Obsidian notes
- Recurring expense support (subscriptions, bills)
- Currency handling with multi-currency support
- Charts and trend analysis via Obsidian Dataview

## Installation

```bash
hermes skills install productivity/budget-tracker
```

Or manually clone into `~/.hermes/skills/productivity/budget-tracker/`.

## Usage

```
budget log "spent £12.50 on lunch"
budget log "rent payment of £850"
budget report week
budget report month
budget set savings-goal "holiday" --target 2000
budget categories
budget summary
```

## Configuration

Store financial data in your Obsidian vault at `~/obsidian-vault/3-Resources/budget/`. Set your default currency and monthly budget in `config.md`.

## Requirements

- Hermes Agent v0.12+
- Obsidian vault (for data storage)

## License

MIT