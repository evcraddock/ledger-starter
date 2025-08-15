# Ledger Double-Entry Accounting - Getting Started Guide

## What is Ledger?

Ledger is a powerful, command-line double-entry accounting system that uses plain text files. It's designed for those who want complete control over their finances without being locked into proprietary software.

I first began using ledger because it checked off several items I was concerned about when dealing with my finances.

- I wanted a hands on approach that would force me to keep track of every penny I spent.
- I wanted to keep a copy of my financial data locally
- I wanted a human readable record that didn't lock me in to any 3rd party software
- It didn't hurt that I prefer a keyboard to a mouse

As a software developer, I am very comfortable in the terminal. It was nice to have the ability to run reports and to be able to use the ledger command line utility in my own scripts.

The biggest issue I ran into was how to organize my files and generally the best way to use them for daily processing and reporting. I found a lot of help at [Plain Text Accounting](https://plaintextaccounting.org).

Over the years, I have come up with a process for how I manage all of this. I am documenting this process here. I am currently using Claude Code to automate a lot of this process.

## Installation

### Ledger

#### macOS

```bash
brew install ledger
```

#### Linux (Debian/Ubuntu)

```bash
sudo apt-get install ledger
```

For more information, visit: <https://www.ledger-cli.org>

### Claude Code

```bash
npm install -g @anthropic/claude-code
```

For more information, visit: <https://docs.anthropic.com/en/docs/claude-code>

## File Organization

```bash
ledger-starter/
├── .claude/
│   └── commands/
│       ├── process-inbox.md    # Command to process receipts/invoices
│       └── format.md           # Command to format ledger files
├── 2025/                       # Year folder for transactions
│   ├── opening.ledger          # Opening balances
│   ├── 2025-01.ledger          # monthly ledger file
│   ├── Receipts/               # Receipt PDFs by month
│   │   └── 01-January/
│   └── Invoices/               # Invoice PDFs by month
│       └── 01-January/
├── docs/
│   ├── daily-process.md        # Daily workflow documentation
│   └── receipt-processing.md   # Receipt processing guide
├── inbox/                      # Staging area for unprocessed documents
├── accounts                    # Chart of accounts
├── account-cards              # Card last-4 to account mappings
├── payees                     # Vendor list with default categories
├── item-categories.md         # Line-item categorization rules
├── CLAUDE.md                  # Claude Code instructions
└── README.md                  # This file
```

## Basic Concepts

### Double-Entry Accounting

Every transaction has at least two entries:

- A debit (increase in assets or expenses)
- A credit (increase in liabilities, equity, or income)

The sum of all entries must equal zero.

### Account Categories

- **Assets**: What you own (bank accounts, cash, investments)
- **Liabilities**: What you owe (credit cards, loans)
- **Income**: Money coming in (salary, interest)
- **Expenses**: Money going out (rent, food, utilities)
- **Equity**: Net worth (opening balances)

## Your Ledger Files

By default the `ledger` command line application will look for
default values at `~/.ledgerrc`. Here you can set your default
sort date, currency and file.

```bash
--exchange $
--sort date
--file main.ledger
```

I have named mine `main.ledger`. In it I include my [accounts](./accounts) and [payees](./payees) lists. I also include a ledger file per year. I keep this file in dedicated folder for that year. So for example [2025/2025.ledger](./2025/2025.ledger).

Now I can create monthly files like `2025/2025-01.ledger` and include them in the yearly file. These files are nice and small perfect for claude's context window.

```ledger
; Transaction with receipt reference
2025/01/15 * Walmart
    ; Receipts - (receipts/01-January/01-15-2025-Walmart.pdf):
    Expenses:Food:Grocery            $45.23
    Expenses:Home:Household          $12.45
    Liabilities:Credit:Amex         $-57.68

; Pending invoice (due date, not invoice date)
2025/01/25 ! Comcast
    ; Invoices - (invoices/01-January/01-05-2025-Comcast.pdf):
    Expenses:Utilities:Internet      $79.99
    Liabilities:Credit:Amex         $-79.99

; Cleared transaction from bank reconciliation
2025/01/31 * Employer
    Assets:Checking:Main          $3,000.00
    Income:Salary                -$3,500.00
    Expenses:Taxes:Federal          $400.00
    Expenses:Taxes:State            $100.00
```

### Accounts file

The accounts file defines your chart of accounts - the complete hierarchy of all financial accounts you track. This establishes the valid account names that can be used in transactions and ensures consistency across your ledger files.

```ledger
; accounts file - defines account hierarchy
account Assets:Checking:Main
account Assets:Checking:Billpay
account Liabilities:Credit:Amex
account Liabilities:Credit:Main
account Expenses:Food:Grocery
account Expenses:Home:Household
account Expenses:Utilities:Internet
account Income:Salary
```

### Payees file

The payees file maintains a standardized list of vendors and merchants you transact with. This ensures consistent naming across all transactions and can include default expense account mappings for each payee to streamline transaction entry.

```ledger
; payees file - standardized vendor names
payee Walmart
payee Target
payee Comcast
payee CVS
```

### Account-cards mapping

The account-cards file maps the last 4 digits of your payment cards to their corresponding ledger accounts. This enables automatic account assignment when processing receipts, ensuring transactions are charged to the correct credit card or bank account.

```ledger
; account-cards file - maps card last-4 to accounts
card 1025 Liabilities:Credit:Amex
card 8027 Liabilities:Credit:Main
```

## Best Practices

1. **Reconcile regularly**: Mark transactions as cleared (*) when they appear on statements
2. **Use meaningful payees**: Be consistent with naming (e.g., always "Walmart" not "WalMart")
3. **Comment liberally**: Use `;` for comments to add context
4. **Version control**: Use git to track changes to your ledger files
5. **Automate imports**: Consider using tools like `ledger-autosync` for bank imports

## Resources

- Plain Text Accounting: <https://plaintextaccounting.org>
- Official Documentation: <https://www.ledger-cli.org/docs.html>
- Manual: <https://www.ledger-cli.org/3.0/doc/ledger3.html>
- Community: <https://www.reddit.com/r/plaintextaccounting/>
- Example files: <https://github.com/ledger/ledger/tree/master/test/input>

## Notes

### Why No Auto-Import

I deliberately don't auto-import transactions from banks or credit cards. Manual entry forces me to review every transaction, catch errors, and stay aware of my spending patterns. Similarly, using LLMs for transaction processing requires me to verify their output since I don't 100% trust their accuracy. Both approaches keep me engaged with my finances rather than letting them run on autopilot.

### Budgets

I've omitted budgets from this starter setup because I haven't focused on implementing them yet. This is something I plan to add in the future as ledger has powerful budget tracking capabilities.

### LLMs and Plain Text Accounting

This repository primarily demonstrates how to leverage LLMs (like Claude Code) with plain text accounting. The combination of structured text files and AI assistance creates a powerful workflow - you get the control and transparency of plain text with the automation capabilities of modern AI tools.
