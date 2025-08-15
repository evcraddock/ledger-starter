# Receipt and Invoice Processing Instructions

Process all PDF files in the `inbox` folder following these steps:

## For each file

### 1. Identify Document Type

- First determine if the document is a **receipt** (proof of payment), an **invoice** (bill requesting payment), or a **check** (written payment)
- Receipts show completed transactions with payment confirmation
- Invoices show amounts due with payment terms and due dates
- Checks show handwritten or printed payment information with "Pay to the order of" field

### 2. Extract Document Content

- Use `pdftotext` or `pngtotext` to read the file
- If text extraction fails, examine the image visually to extract information

### 3. Parse Document Information

**For Receipts**, extract:

- **Date**: Transaction date in YYYY-MM-DD format
- **Merchant**: Store/vendor name
- **Total Amount**: Final amount paid
- **Payment Method**: Card type and last 4 digits (if available)
- **Items Purchased**: List of individual items/services from the receipt

**For Invoices**, extract:

- **Invoice Date**: Date invoice was issued
- **Due Date**: Payment due date
- **Vendor**: Company/service provider name
- **Total Amount**: Amount due
- **Services/Items**: Description of services or items being billed

**For Checks**, extract:

- **Date**: Check date (usually handwritten in top right)
- **Payee**: Name written after "Pay to the order of"
- **Amount**: Dollar amount of the check
- **Check Number**: Usually printed on check
- **Memo**: Description from memo line (if available)

### 4. Rename and Move File

**For Receipts:**

- Rename file to format: `MM-DD-YYYY-Merchant.pdf` (e.g., `06-21-2025-Walmart.pdf`)
- Move file to: `Receipts/MM-MonthName/` (e.g., `Receipts/06-June/`)

**For Invoices:**

- Rename file to format: `MM-DD-YYYY-Vendor.pdf` (using invoice date)
- Move file to: `Invoices/MM-MonthName/` (e.g., `Invoices/06-June/`)

**For Checks:**

- Rename file to format: `MM-DD-YYYY-Payee-Check.pdf` (e.g., `06-21-2025-JohnDoe-Check.pdf`)
- Move file to: `Receipts/MM-MonthName/` (e.g., `Receipts/06-June/`)

### 5. Update Ledger Transaction

**For Receipts:**

- Search for existing transaction in `YYYY-MM.ledger` file matching the date, merchant, and amount
- If found: Add receipt reference in the format:

  ```vim
  ; Receipts - (receipts/MM-Month/MM-DD-YYYY Merchant.pdf):
  ```

- If not found: Create a new pending transaction using "!"

**For Invoices:**

- Create a new pending transaction using "!" for the **due date** (not invoice date)
- Add invoice reference in the format:

  ```vim
  ; Invoices - (invoices/MM-Month/MM-DD-YYYY-Vendor.pdf):
  ```

- Use the due date for the transaction date to track when payment is expected

**For Checks:**

- Process similar to receipts - search for existing transaction matching date, payee, and amount
- If found: Add check reference in the format:

  ```vim
  ; Receipts - (receipts/MM-Month/MM-DD-YYYY Payee-Check.pdf):
  ```

- If not found: Create a new pending transaction using "!"
- The payment account for checks is usually `Assets:Checking:Billpay` or similar checking account

### 6. Determine Merchant and Account Categories

**Merchant Identification:**

- Extract merchant name from receipt
- **Check @payees** file to find the correct payee name and any aliases
- Use the standardized payee name from the payees file

**Payment Account Determination:**

- Extract card type and last 4 digits from receipt (if available)
- **Check @account-cards** file to map card number to correct account
- Look for entries like `card 1025` to find the corresponding account
- **IMPORTANT**: Never update @account-cards during processing
- If no card info available or not found in @account-cards, default to `Liabilities:Credit:Amex`

**Account Categorization:**

- **Check @accounts** file for payee-specific account mappings (regex patterns under account definitions)
- **Check @item-categories.md** for item-specific categorization guidelines  
- **Check @accounts** file for available account categories
- Prioritize account mappings from @accounts file over general categorization rules
- Use multiple expense categories if receipt contains different types of items

**Example Process:**

1. Receipt shows "Walmart" → Check @payees → Use "Walmart"
2. Receipt shows "Visa ending in 8027" → Check @account-cards → Maps to `Liabilities:Credit:Main`
3. Check @accounts → "Walmart" matches `Expenses:Food:Groceries` pattern
4. Check items → Mixed grocery/household items → Split accordingly:

   ```vim
   Expenses:Food:Grocery         $45.23
   Expenses:Home:Household       $12.45  
   ```

### 7. Transaction Format

Follow CLAUDE.md formatting rules:

- Header: 2025/MM/DD * Merchant (or ! for pending) using payee name from @payees
- Include receipt reference comment
- Use account names exactly as defined in @accounts file
- Align amounts in rightmost column
- Maintain double-entry bookkeeping
- **For multi-line transactions**: Include the negative total amount on the payment account line
- Use payment account determined from @account-cards or appropriate default

## Example Processing

Receipt shows CVS purchase with cold medicine, shampoo, and candy totaling $24.77, paid with Amex ending in 1025

**Process:**

1. Extract merchant: "CVS" → Check @payees → Use "CVS"
2. Extract payment: "Amex ending in 1025" → Check @account-cards → Maps to `Liabilities:Credit:Amex`
3. Check @accounts → No specific CVS mapping, categorize by items
4. Check @item-categories.md → Medicine=Medical, Shampoo=Personal, Candy=Food:Junkfood
5. Check @accounts → Use exact account names: `Expenses:Medical`, `Expenses:Personal`, `Expenses:Food:Junkfood`

**Result in ledger:**

```vim
2025/06/21 * CVS
    ; Receipts - (receipts/06-June/06-21-2025 CVS.pdf):
    Expenses:Medical              $12.99
    Expenses:Personal              $8.49
    Expenses:Food:Junkfood         $3.29
    Liabilities:Credit:Amex      $-24.77
```

## Example Invoice Processing

Invoice from utility company dated 06/05/2025 with due date 06/25/2025 for $150.00

**Process:**

1. Identify as invoice (not receipt) based on "Due Date" and "Amount Due" fields
2. Extract vendor: "ConEd" → Check @payees → Use "ConEd"
3. Rename file: `06-05-2025-ConEd.pdf` (using invoice date)
4. Move to: `Invoices/06-June/`
5. Create pending transaction for due date (06/25/2025)

**Result in ledger:**

```vim
2025/06/25 ! ConEd
    ; Invoices - (invoices/06-June/06-05-2025 ConEd.pdf):
    Expenses:Utilities:Electric   $150.00
    Liabilities:Credit:Amex      $-150.00
```

## Example Check Processing

Check dated 07/24/2025 with "Pay to the order of" showing "Jane Smith" for $140.00

**Process:**

1. Identify as check based on "Pay to the order of" field
2. Extract payee: "Jane Smith" → Check @payees → If not found, use as-is or add to payees
3. Rename file: `07-24-2025-Jane-Smith-Check.pdf`
4. Move to: `Receipts/07-July/`
5. Create transaction for check date (07/24/2025)

**Result in ledger:**

```vim
2025/07/24 ! Jane Smith
    ; Receipts - (receipts/07-July/07-24-2025-Jane-Smith-Check.pdf):
    Expenses:Misc                   $140.00
    Assets:Checking:Billpay        $-140.00
```

**Note**: Always reference @accounts and @payees files to ensure consistent merchant names and account categories. For multi-line transactions, include the negative total amount on the Liabilities line.

Process each file in inbox systematically, identifying whether it's a receipt, invoice, or check and handling accordingly.
