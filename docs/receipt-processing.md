---
author: ["Erik Craddock"]
description: "Automate your receipt processing and bookkeeping with a scanner and Claude Code to maintain accurate financial records with minimal effort."
hero_image: "receipts-claude.png"
images: ["receipts-claude.png"]
kind: "POST"
slug: "receipt-processing"
status: "DRAFT"
title: "Processing Receipts with Ledger and Claude Code"
---
<!-- markdownlint-disable-next-line MD025 -->
# Processing Receipts with Ledger and Claude Code

I keep a physical metal inbox next to my desk where our family deposits all receipts. Everyone has been trained/threatened/pestered into using this system. When you ask yourself, "who still gets receipts?", yes, that is us. Each morning, I scan these receipts, invoices, and checks using a ScanSnap iX 1300 scanner. I've configured the scanner software to automatically save files as PDFs directly into an `inbox` folder on my computer. You simply place the receipt in and press the button. For receipts that arrive via email, I print them and save as PDF directly to the same `inbox` folder instead of using a physical printer (File → Print in Mail.app, or Ctrl+P in Gmail, then choose "Save as PDF"). Sometimes when we can't get a receipt, we just write our own on a piece of paper.

Next, I run Claude Code (`claude`) in the terminal from my ledger folder. Claude Code uses slash commands - reusable prompts stored as markdown files in `.claude/commands/`. The [process-inbox.md](https://github.com/evcraddock/ledger-starter/blob/main/.claude/commands/process-inbox.md) slash command contains detailed instructions for processing receipts in the `inbox` folder. To start it, run `/process-inbox` in the Claude Code terminal. Here's an overview of what it does, though you should read the full command yourself for all the details:

Claude automatically handles the following steps:

- Loop through each file in the folder
- Identify document type (receipt, invoice, or check)
- Extract text content using PDF reader
- Rename and move files to appropriate folders (e.g., `Receipts/01-January`)
- Create or update ledger entries
- Use reference files (`payees`, `account-cards`, `item-categories.md`) to categorize transactions

The command file also includes formatting examples and instructions for marking transactions as pending (`!`) or cleared (`*`). The end result is a ledger entry for each document processed from your `inbox` folder. Most of the time it works flawlessly, though occasionally it might mark a transaction as cleared by mistake or categorize an expense incorrectly. It's usually faster to manually correct these minor issues than to perfect the automation. When errors are frequent, consider updating the command prompt. Claude automatically runs `ledger bal` at the end to verify the file balances correctly.

This is a lot of explanation for a quick process. In practice, it's as simple as:

1. Put the receipt in the scanner and press the button
2. Run `claude` in your terminal from your ledger folder
3. Run `/process-inbox` in Claude
4. Verify that everything is correct

## Summary

This receipt processing system transforms a manual bookkeeping chore into a quick daily routine. By combining a simple scanner setup with Claude Code's automation, you can maintain accurate financial records with minimal effort. The whole process typically takes just a few minutes each morning, and the result is a well-organized ledger with all receipts properly filed and categorized. While the automation isn't perfect, it handles the tedious parts so you can focus on reviewing and ensuring accuracy rather than data entry.
