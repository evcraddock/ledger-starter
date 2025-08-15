# Processing Receipts with Ledger and Claude Code

Next to my desk I have a physical metal inbox. Everyone in our family has been trained/threatened/pestered into putting their receipts into this inbox. When you ask yourself, "who still gets receipts?", yes, that is us. Anyway, every morning, I gather all receipts, invoices, and checks and drop them into a `inbox` folder on my machine. I use a scanner to accomplish this. I have a ScanSnap iX 1300. It makes it very easy. You simply place the receipt in the scanner and press the button. I have the software set up to automatically save the file as a PDF and drop it in the `inbox` folder.

Next, I run Claude Code, `claude`, in the terminal from my ledger folder. Claude Code has this concept of a slash command. Essentially a slash command is a markdown file that gets stored in `.claude/commands/`. This markdown file is a reusable prompt which instructs Claude with what to do. If you look at the slash command [process-inbox.md](.claude/commands/process-inbox.md), you will see a bunch of instructions telling Claude exactly what to do with the receipts it finds in the `inbox` folder. To start it, run the slash command `/process-inbox` in the Claude Code terminal. Here is a overview of what it does. You should read it yourself for more detail.

- Instructs Claude to loop through each file in the folder.
- Identify the type of document it is working with (receipt, invoice, check)
- Extract the contents of the file into text using a command line pdf reader
- Rename the file to something like `MM-DD-YYYY-Merchant.pdf`
- Move the file to `{DocumentType}/MM-MonthName` folder e.g. `Receipts/01-January`
- Now it is time to update the ledger file.
- Check the `payees` file to verify if the merchant exists in the file, if not add it
- Look for the Last 4 digits of the payment card and then check the `account-cards` file to get the proper account.
- Look in the `item-categories.md` file to match the line item in the receipt to an expense category

The rest of the file gives formatting examples as well as instructions on when to mark a transaction as pending `!` or cleared `*`. The result is an entry for each receipt in your `inbox` folder to your monthly ledger file. Most of the time, it works flawlessly. On occasion it will mark a transaction cleared by mistake, or categorize an expense incorrectly. It is usually faster to just manually correct these kinds of mistakes. Sometimes it makes sense to update the prompt. Also I should point out that at the end of this process Claude will automatically run `ledger bal` against your file to make sure it doesn't have any errors.

This is a lot of explanation for a quick process. I should point out that normally it is as simple as doing the following:

1. Put the receipt in the scanner and press the button
2. Run `claude` in your terminal from your ledger folder
3. Run `/process-inbox` from the Claude application
4. Verify that everything is correct.
