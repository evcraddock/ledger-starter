---
author: ["Erik Craddock"]
description: "A simple 5-10 minute daily routine for managing personal finances using Ledger CLI and Claude Code automation."
hero_image: "finances-claude.png"
images: ["finances-claude.png"]
kind: "POST"
slug: "daily-process"
status: "DRAFT"
title: "Managing Finances with Ledger and Claude Code"
---
<!-- markdownlint-disable-next-line MD025 -->
# Managing Finances with Ledger and Claude Code

Every morning, I spend 5-10 minutes processing my financial transactions from the previous day. When I started this process several years ago, I worried it would be too time-consuming. For a while it was, especially when I skipped days. But I believed that spending more time with my financial data would lead to better understanding and management of my assets. This turned out to be true, I paid off all debt and became more comfortable with investments. Over time, I've streamlined the process significantly.

Adding Claude Code transformed this routine completely. It handles the tedious work while maintaining accuracy, automatically categorizing transactions and verifying balances using the ledger CLI tool. The precision consistently surprises me.

I initially had concerns about using an LLM for financial data. According to [Anthropic's policies](https://privacy.anthropic.com/en/articles/10023580-is-my-data-used-for-model-training), user input isn't used for training and data remains private. While I remain somewhat cautious and limit exposure to expense accounts, I'm probably at greater risk from my credit card processor.

Accuracy was another concern given AI limitations. However, I realized accuracy issues exist whether humans or AI handle the data. Git version control and the ledger CLI provide safeguards. I still manually validate amounts against bank statements daily, this manual check is why the process takes even 5 minutes. While I could automate this completely, I value staying engaged with my finances, and the few daily mistakes make those 5 minutes worthwhile to avoid larger debugging sessions later.

I've documented this process with step-by-step instructions, prompts, and tools for anyone interested.

## Setup

I use the [ledger-starter](https://github.com/evcraddock/ledger-starter) GitHub repository as a base for my ledger setup. To use it for your personal finances:

```bash
git clone https://github.com/evcraddock/ledger-starter.git
cd ledger-starter
rm -rf .git  # Remove git history for personal use
git init     # Start fresh git history for your private financial data
```

The [README](https://github.com/evcraddock/ledger-starter/blob/main/README.md) contains detailed installation instructions for both Ledger and Claude Code, along with explanations of the file organization and configuration files.

## Daily Process

1. **Set up your workspace** - Open your text editor (Vim with ledger plugin or VS Code with ledger extension work well) and navigate to your ledger-starter root directory. From there, open the current year's folder (e.g., `2025/`) to view your transaction files. In your editor's built-in terminal, make sure you're in the ledger-starter root directory and start Claude Code by running `claude`. Note: VS Code users can install the Claude Code extension for tighter integration. Keep this entire repository in a private git repo to track your financial data securely.

2. **Process yesterday's receipts** - Scan and process any receipts, invoices, or checks from the previous day using the `/process-inbox` command in Claude Code. Physical receipts provide detailed line-item data for accurate categorization, serve as warranty/return proof, and create tax audit trails. See [Receipt Processing](/receipt-processing) for step-by-step instructions on automating this with Claude Code.

3. **Reconcile with bank websites** - Log into each bank and credit card website to review posted transactions. Verify all transactions appear in your ledger file and mark any previously pending transactions that have cleared by changing "!" to "*".

4. **Add missing transactions** - Copy any missing transaction details from bank websites and paste them into Claude Code with simple instructions like "Add this pending transaction to the Visa". Claude will parse the details, apply the correct account mapping, and mark new transactions as pending with "!".

5. **Verify balances** - Run `ledger cleared bal Assets Liabilities --no-pager` to check that accounts balance correctly. This shows both current balances and projected balances when pending transactions clear. Use the [cbal](https://github.com/evcraddock/ledger-starter/blob/main/cbal) shortcut script to save typing this command.

6. **Format and commit** - Run `/format` in Claude Code to sort transactions and fix indentation, then commit your changes to git:

   ```bash
   git add .
   git commit -m "MM-DD Daily Update"
   git push
   ```

## Summary

That's it! Six simple steps that take around 5-10 minutes each morning. The combination of ledger's precision and Claude Code's automation means you stay engaged with your finances without drowning in tedious data entry. You get complete visibility into your spending, automatic categorization, and the confidence that comes from knowing exactly where every dollar goes. There's something deeply satisfying about watching those accounts balance perfectly each day.
