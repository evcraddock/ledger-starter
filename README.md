# Ledger Double-Entry Accounting - Getting Started Guide

## What is Ledger?

Ledger is a powerful, command-line double-entry accounting system that uses plain text files. It's designed for those who want complete control over their finances without being locked into proprietary software.

I first began using ledger because it checked off several items I was concerned about when dealing with my finances.

- I wanted a hands on approach that would force me to keep track of every penny I spent.
- I wanted to keep a copy of my financial data locally
- I wanted a human readable record that didn't lock me in to any 3rd party software
- It didn't hurt that I prefer a keyboard to a mouse

As a software developer, I am very comfortable in the terminal. It was nice to have the ability to run reports and to be able to use the command line utility in my own scripts.

The biggest issue I ran into was how to organize my files and generally the best way to use them for daily processing and reporting. I found a lot of help at [Plain Text Accounting](https://plaintextaccounting.org).

Over the years, I have come up with a process for how I manage all of this. I am documenting this process here. I am currently using Claude Code to automate a lot of the process.

Anthropic's policy is to not train based on user input. They also do not persist this data unless specifically instructed to. Despite this, if you are uncomfortable submitting your data there are local models that should work as well. I have not yet evaluated any local models but plan to in the future.

Below are some LLM generated instructions on getting started with ledger. I intend to replace this with more specific instructions at some point.

## Installation

### macOS

```bash
brew install ledger
```

### Linux (Debian/Ubuntu)

```bash
sudo apt-get install ledger
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

## Creating Your First Ledger File

Create a file named `main.ledger`:

```ledger
; Opening balances
2025/01/01 Opening Balance
    Assets:Checking                 $1,000.00
    Equity:Opening Balances

; Simple transaction
2025/01/15 Grocery Store
    Expenses:Food:Groceries            $50.00
    Assets:Checking

; Paycheck with multiple splits
2025/01/31 Employer
    Assets:Checking                 $3,000.00
    Income:Salary                  -$3,500.00
    Expenses:Taxes:Federal            $400.00
    Expenses:Taxes:State              $100.00

; Credit card payment
2025/02/01 Credit Card Payment
    Liabilities:CreditCard            $500.00
    Assets:Checking
```

## Basic Commands

### View balance of all accounts

```bash
ledger -f main.ledger balance
```

### View checking account register

```bash
ledger -f main.ledger register Checking
```

### Monthly expenses report

```bash
ledger -f main.ledger -M register Expenses
```

### Balance sheet (assets vs liabilities)

```bash
ledger -f main.ledger balance Assets Liabilities
```

### Income statement

```bash
ledger -f main.ledger balance Income Expenses
```

### Search transactions

```bash
ledger -f main.ledger register payee "Grocery Store"
```

## File Organization Tips

### Recommended structure

```bash
finances/
├── main.ledger          # Main file that includes others
├── accounts.ledger      # Account declarations
├── 2025/
│   ├── 01-january.ledger
│   ├── 02-february.ledger
│   └── ...
└── prices.db           # Historical prices (optional)
```

### Main file example

```ledger
; main.ledger
include accounts.ledger
include 2025/*.ledger
```

### Account declarations (accounts.ledger)

```ledger
; Account declarations help with auto-completion and typo detection
account Assets:Checking
account Assets:Savings
account Expenses:Food:Groceries
account Expenses:Food:Restaurants
account Income:Salary
account Liabilities:CreditCard
```

## Advanced Features

### Automated transactions

```ledger
; Automatically allocate 20% of income to savings
= Income:Salary
    Assets:Savings                    0.20
    Assets:Checking                  -0.20
```

### Assertions (verify balances)

```ledger
2025/01/31 * Balance Assertion
    Assets:Checking                    $0 = $3,450.00
```

### Budgeting

```bash
ledger -f main.ledger --budget --monthly register Expenses
```

### Multi-currency

```ledger
2025/01/15 Currency Exchange
    Assets:Euro                     €100.00
    Assets:USD                     -$110.00
```

## Common Queries

### Current net worth

```bash
ledger -f main.ledger balance Assets Liabilities
```

### This month's expenses by category

```bash
ledger -f main.ledger -p "this month" balance Expenses
```

### Year-to-date income

```bash
ledger -f main.ledger -p "this year" balance Income
```

### Uncleared transactions

```bash
ledger -f main.ledger register --uncleared
```

## Best Practices

1. **Reconcile regularly**: Mark transactions as cleared (*) when they appear on statements
2. **Use meaningful payees**: Be consistent with naming (e.g., always "Walmart" not "WalMart")
3. **Comment liberally**: Use `;` for comments to add context
4. **Version control**: Use git to track changes to your ledger files
5. **Automate imports**: Consider using tools like `ledger-autosync` for bank imports

## Resources

- Official Documentation: <https://www.ledger-cli.org/docs.html>
- Manual: <https://www.ledger-cli.org/3.0/doc/ledger3.html>
- Community: <https://www.reddit.com/r/plaintextaccounting/>
- Example files: <https://github.com/ledger/ledger/tree/master/test/input>

## Quick Start Checklist

- [ ] Install ledger
- [ ] Create your first ledger file
- [ ] Set up opening balances
- [ ] Enter a few transactions
- [ ] Run `ledger balance` to see account totals
- [ ] Run `ledger register` to see transaction history
- [ ] Set up a file organization system
- [ ] Consider automation tools for importing bank data
