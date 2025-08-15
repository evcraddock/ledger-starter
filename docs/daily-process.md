# Managing Finances with Ledger and Claude Code

Every morning, I spend anywhere between 5 and 10 minutes processing my financial transactions from the previous day. When I first started this process, several years ago, I was concerned that it would take too much time. For awhile it did, especially if I didn't handle things every day. I decided that it was worth it. My thoughts were that the more time I spent looking at my financial data the better I would understand what I had. I expected this would naturally lead to better management of my assets. This turned out to be true. Soon, I was completely out of debt and felt more comfortable with my investments. Also, as time has gone by, I've stream lined the process. Now I can process my transactions much faster.

Recently, I started using Claude Code to do most of the tedious work for me. It has been amazing. As I mentioned earlier, it now takes about 5 to 10 minutes and it is more accurate. I am routinely shocked that it knows exactly which account, how to break up transactions by category and how to verify that everything is balanced by using the ledger cli tool.

I did have a few concerns. The biggest was just using an LLM for this kind of process in the first place. I had fears of my transaction being used as training data or worse made public on the internet. According to [Anthropic's policies](https://privacy.anthropic.com/en/articles/10023580-is-my-data-used-for-model-training), user input isn't used for training. They also claim that your data is private unless you specifically say otherwise. To be honest, I'm still skeptical so I've limited what accounts I expose to expense accounts. To be honest, I am probably more at risk from my Credit Card processor.

Another concern was accuracy. There has been a lot of press about AI halucinations. I quickly realized that accuracy was an issue whether it was a human or an AI updating files. The fix is source control and the ledger cli tool. This is also a good reason to continue validating the amounts myself by checking the ledger register with my credit card and bank account statement. I still do this manually everyday, this is the only reason it takes even 5 minutes of my time. I could easily automate this completly and have it email me if there were issues. The primary reason I don't automate is, as I mentioned earlier, I find value in closely tracking what I have. Also there are enough little mistakes that spending 5 minutes a day is worth it to prevent potential larger debugging sessions weekly or monthly.

Mostly for documentation, but also for anyone who is interested, I have decided to write down the steps I take along with any prompts, custom scripts and command line tools that I use.

## Setup

I use the [ledger-starter](https://github.com/your-username/ledger-starter) GitHub repository as a base for my ledger setup. To use it for your personal finances:

```bash
git clone https://github.com/evcraddock/ledger-starter.git
cd ledger-starter
rm -rf .git  # Remove git history for personal use
git init     # Start fresh git history for your private financial data
```

The [README](../README.md) contains detailed installation instructions for both Ledger and Claude Code, along with explanations of the file organization and configuration files.

## Daily Process

1. **Open the current year folder and start Claude Code** - Open the folder for the current year (e.g., `2025/`) in a text editor with ledger syntax support. Good options include Vim with ledger plugin or VS Code with ledger extension. Open a terminal in this folder and start Claude Code by running `claude`. Most editors have built-in terminal applications, and some like VS Code have Claude Code extensions that can be installed for seamless integration. This folder should be stored in its own private git repository for version control of your financial data.

2. **Add transactions from receipts** - Process any receipts, invoices, or checks from the previous day. Keeping physical receipts provides detailed line-item information for accurate expense categorization, serves as proof for warranties and returns, and creates an audit trail for tax purposes. See [Receipt Processing](receipt-processing.md) for detailed instructions on how I use Claude Code to automatically enter transactions from scanned receipts.

3. **Verify and reconcile account transactions** - Open the website for each bank and credit card account. Review all posted transactions to ensure they are represented in your ledger file. While reviewing, update any previously pending transactions that have now cleared by changing "!" to "*" to indicate the transaction has cleared the bank.

4. **Add missing transactions** - For any transactions not yet in your ledger, use Claude Code to add them. Simply copy the transaction information from the website and paste it into Claude Code with instructions like "Add this pending transaction to the Visa". Claude will automatically parse the transaction details, mark it with "!" for pending status, and add it to the appropriate ledger file with the correct account mapping.

5. **Check that everything balances** - Verify your accounts are balanced by running `ledger cleared bal Assets Liabilities --no-pager`. This command shows current account balances alongside what they will be when pending transactions clear. I run this command frequently, so I created a [cbal](../cbal) script to save typing.

6. **Commit your changes** - If everything looks good, run the Claude Code slash command `/format` to properly format the current month's ledger file. This command sorts transactions by date and amount while ensuring proper indentation. Then commit the changes to git for version control:

   ```bash
   git add .
   git commit -m "MM-DD Daily Update"
   git push
   ```

## Summary

That's it! What once seemed like a daunting daily task now takes just 5-10 minutes each morning. The combination of ledger's power and Claude Code's assistance makes tracking finances surprisingly painless. Sure, there's a learning curve at first, the clarity and control you gain over your finances is worth those few minutes each day. Plus, there's something oddly satisfying about watching everything balance perfectly at the end.
