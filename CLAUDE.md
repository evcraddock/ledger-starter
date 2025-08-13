## General Information
- The .ledger files in this repo are segregated by month
- Each file YYYY-MM.ledger is for the year that the root folder is named (in this case 2025)
- There is a Receipts folder that contains a folder for each month (MM-MonthName) which stores purchase receipts for that month
- There is an Invoices folder that contains a folder for each month (MM-MonthName) which stores invoices for that month
- The file YYYY.ledger is the main file that includes each month file. for example 
``` ledger
include 2025-01.ledger
```
- The accounts file lists out each financial account that is being tracked
- The payees file lists the different vendors that have been paid and also lists the expense accounts associated with that vendor
- The expense accounts in the payees file should have a corresponding entry in the accounts file
- The item-categories.md file associates line items from a receipt to expense accounts
- The account-cards file associates the last 4 digits of a card with a banking or credit card account. It is used to determine which account a receipt should go against.

## Transaction Creation Rules
- Always use the format: `2025/MM/DD * Merchant` for transaction headers
- Use "!" for pending transactions
- Use "*" for cleared transactions
- Include receipt references in format: `; Receipts - (receipts/MM-Month/MM-DD-YYYY Merchant.pdf)`
- Align dollar amounts consistently in the rightmost column
- Use proper account hierarchy with colons (e.g., `Expenses:Food:Grocery`)

## Account Naming Conventions
- Check the accounts file and the payees file for everything except income
- If the Payee doesn't exist, it should be added to the payees file

## Transaction Categorization
- First check item-categories.md to determine the category based on specific items
- If that doesn't help, use the accounts file payee patterns to determine categorization
- Fallback to Expenses:Misc if no match is found
- Always include receipt references for physical purchases (not for digital subscriptions)

## Error Prevention 
### !! IMPORTANT
- Never modify existing transaction dates or amounts without explicit instruction
- Always maintain the double-entry bookkeeping principle
- Verify account names match existing conventions in the ledger
- When adding new transactions, place them in correct chronological order
- After every time a .ledger file is changed, run the `ledger bal` command to verify that the values still balance