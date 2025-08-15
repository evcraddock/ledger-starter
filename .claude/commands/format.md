# Formatting Requirements

- get todays date `date "+%m-%d-%Y"`
- format the ledger file for the current month using: `ledger print --sort 'date, amount' -f {YY}-{MM}.ledger > {YY}.ledger && mv {YY}.ledger {YY}-{MM}.ledger`
- Maintain chronological order by date
- Ensure proper indentation for account lines
