# Bank Management Application (C++)

A console-based banking system demonstrating object-oriented design and
persistent file storage.

## Features
- Create new accounts (auto-generated account number starting at 1001)
- Deposit money
- Withdraw money (blocks overdrafts)
- Check balance (PIN-protected)
- Transfer funds between accounts
- View a single account's summary
- Admin view listing all accounts
- All data persists to `accounts.txt` between runs

## Design / OOP concepts used
| Concept | Where |
|---|---|
| Encapsulation | `Account` keeps `balance`, `pin`, etc. `private`; only exposed via getters and `deposit()`/`withdraw()` which validate input |
| Single Responsibility | `Account` = one record's data + rules. `Bank` = collection management + file I/O. `main.cpp` = user interface only |
| Abstraction | Callers use `bank.deposit(acc, pin, amount, msg)` without knowing how accounts are stored or files are written |
| Serialization | `Account::toFileString()` / `Account::fromFileString()` isolate the on-disk format in one place |

## File layout
```
Account.h / Account.cpp   -> Account class (single bank account)
Bank.h    / Bank.cpp       -> Bank class (manages all accounts + file I/O)
main.cpp                   -> Console menu / user interaction
accounts.txt                -> Data file (created automatically on first run)
```

## Data format
Each line in `accounts.txt` is one account:
```
accountNumber|holderName|pin|balance
```
Example:
```
1001|Alice Smith|1234|5000
```

## Security notes
- Every balance-affecting or balance-revealing operation (deposit, withdraw,
  check balance, transfer) requires the correct PIN before it proceeds.
- PINs and balances are never printed back except to the authenticated owner.
- This is a teaching/demo project: PINs are stored in plain text in the data
  file, which is fine for learning but **not** how a real bank would do it.
  For production use you would hash the PIN (e.g. bcrypt) and encrypt the
  data file or use a proper database with access controls.

## Build

Using g++ directly:
```bash
g++ -std=c++17 -Wall -o bank_app main.cpp Account.cpp Bank.cpp
```

## Run
```bash
./bank_app
```

On first run there's no `accounts.txt` yet, so start by choosing option 1
to create an account. The account number and PIN you set are needed for
every later operation.

## Example session
```
1. Create New Account   -> creates account #1001
2. Deposit               -> add funds
3. Withdraw              -> remove funds (fails if insufficient)
4. Check Balance          -> PIN required
5. Transfer Funds          -> move money to another account #
6. View Account Summary    -> single account details
7. List All Accounts       -> admin overview of every account
8. Exit
```

## Possible extensions
- Password/PIN hashing (e.g. with a small SHA-256 implementation)
- Transaction history log per account (append-only ledger file)
- Interest calculation for savings accounts (via an `Account` subclass)
- Multi-currency support
- Migrate storage from a flat file to SQLite for concurrent access
