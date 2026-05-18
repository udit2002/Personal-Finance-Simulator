# Personal Finance Simulator

**Author:** Udit Bansal | **Student ID:** 560290619 | **Tutorial:** 26 | **Tutor:** Niousha Nazemi

A Command-Line Interface (CLI) based Personal Finance Simulator built with Python and PostgreSQL. Manage accounts, track transactions, set budgets, generate financial reports, export statements, and visualise spending — all from the terminal.

---

## Table of Contents

- [Overview](#overview)
- [Technologies Used](#technologies-used)
- [Project Structure](#project-structure)
- [Database Schema](#database-schema)
- [Setup & Installation](#setup--installation)
- [Running the Application](#running-the-application)
- [Features & Screenshots](#features--screenshots)
  - [Main Menu](#main-menu)
  - [Account Management](#account-management)
  - [Transactions](#transactions)
  - [Budget Management](#budget-management)
  - [Financial Reports](#financial-reports)
  - [Export Statements](#export-statements)
  - [Visual Reports (Charts)](#visual-reports-charts)
- [Module Descriptions](#module-descriptions)

---

## Overview

This project simulates a personal finance management system, allowing users to:

- Create and manage multiple bank accounts (Savings, Checking)
- Perform deposits, withdrawals, and transfers
- Categorise expenses and track budgets
- Generate income/expense reports with filtering
- Export transaction data as CSV files
- Visualise financial data using bar, pie, and line charts

---

## Technologies Used

| Technology | Purpose |
|---|---|
| Python 3 | Core application language |
| PostgreSQL | Relational database for persistent storage |
| psycopg2 | PostgreSQL database adapter for Python |
| matplotlib | Chart and graph generation |
| csv (stdlib) | CSV export functionality |
| CLI (Terminal) | User interface |

---

## Project Structure

```
personal-finance-simulator/
│
├── main.py            # Entry point; all menus and navigation
├── database.py        # Database connection setup (psycopg2)
├── accounts.py        # Account creation, update, delete, view
├── transactions.py    # Deposit, withdraw, transfer, view history
├── budgets.py         # Set, track, reset category budgets
├── reports.py         # Income/expense summaries and filters
├── exports.py         # CSV export by account, date, category
├── charts.py          # Matplotlib chart generation
└── SQL_Stmts.sql      # Table creation scripts
```

---

## Database Schema

```sql
CREATE TABLE accounts (
    acc_id   SERIAL PRIMARY KEY,
    name     VARCHAR(100),
    type     VARCHAR(50),
    balance  NUMERIC(10,2)
);

CREATE TABLE budgets (
    budget_id     SERIAL PRIMARY KEY,
    category      VARCHAR(50),
    limit_amount  NUMERIC(10,2),
    spent         NUMERIC(10,2)
);

CREATE TABLE transactions (
    trans_id  SERIAL PRIMARY KEY,
    acc_id    INT REFERENCES accounts(acc_id) ON DELETE CASCADE,
    type      VARCHAR(50),
    amount    NUMERIC(10,2),
    category  VARCHAR(50),
    date      TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);
```

**Relationships:** Each transaction links to an account via `acc_id`. Deleting an account cascades and removes all its associated transactions.

---

## Setup & Installation

### Prerequisites

- Python 3.8+
- PostgreSQL database access
- pip

### Install Dependencies

```bash
pip install psycopg2-binary matplotlib
```

### Configure Database

Edit `database.py` to point to your PostgreSQL instance:

```python
def connect_db():
    conn = psycopg2.connect(
        host="your-db-host",
        database="your-database-name",
        user="your-username",
        password="your-password"
    )
    return conn
```

### Initialise Tables

Run the SQL script against your database:

```bash
psql -h <host> -U <user> -d <database> -f SQL_Stmts.sql
```

---

## Running the Application

```bash
python main.py
```

---

## Features & Screenshots

### Main Menu

The application launches with a top-level menu. All navigation uses numbered options.

```
====== Personal Finance Simulator ======
1. Account Management
2. Transactions
3. Budget Management
4. Reports
5. Export Statements (CSV)
6. Visual Reports (Charts)
7. Exit

Enter choice:
```

---

### Account Management

**Submenu:**

```
--- Account Management ---
1. Create Account
2. View Accounts
3. Update Account
4. Delete Account
5. Back

Enter choice:
```

**Create Account:**

```
Enter choice: 1
Enter name: John Smith
Enter type (Savings/Checking): Savings
Enter opening balance: 5000
Account created successfully. Account ID: 1
```

**View Accounts:**

```
Enter choice: 2

Accounts:
--------------------------------------------------
ID: 1 | Name: John Smith  | Type: Savings   | Balance: $5000.00
ID: 2 | Name: Jane Doe    | Type: Checking  | Balance: $2500.00
ID: 3 | Name: Joint Acc   | Type: Savings   | Balance: $10000.00
```

**Update Account:**

```
Enter choice: 3
Enter account ID: 2
Enter new name: Jane Doe (Updated)
Enter new type: Checking
Account updated successfully.
```

**Delete Account:**

```
Enter choice: 4
Enter account ID: 3
Account deleted successfully.
```

---

### Transactions

**Submenu:**

```
--- Transactions ---
1. Deposit
2. Withdraw
3. Transfer
4. View Transactions
5. Back

Enter choice:
```

**Deposit:**

```
Enter choice: 1
Enter account ID: 1
Enter amount: 1500
$1500.0 deposited successfully.
```

**Withdraw:**

```
Enter choice: 2
Enter account ID: 1
Enter amount: 200
Enter category: Food
Food budget remaining: $300.0
$200.0 withdrawn successfully.
```

**Insufficient Balance Handling:**

```
Enter choice: 2
Enter account ID: 2
Enter amount: 99999
Enter category: Travel
Insufficient balance.
```

**Transfer Between Accounts:**

```
Enter choice: 3
From account ID: 1
To account ID: 2
Enter amount: 500
$500.0 transferred successfully.
```

**View Transactions:**

```
Enter choice: 4

Transaction History:
--------------------------------------------------------------------------------
ID: 5 | Account: 1 | Deposit       | $1500.00 | Deposit  | 2026-04-24 10:21:03
ID: 4 | Account: 1 | Withdraw      | $200.00  | Food     | 2026-04-24 10:19:45
ID: 3 | Account: 2 | Transfer In   | $500.00  | Transfer | 2026-04-24 10:18:30
ID: 2 | Account: 1 | Transfer Out  | $500.00  | Transfer | 2026-04-24 10:18:30
ID: 1 | Account: 1 | Deposit       | $5000.00 | Deposit  | 2026-04-24 10:15:00
```

---

### Budget Management

**Submenu:**

```
--- Budget Management ---
1. Set Budget
2. View Budgets
3. Reset Budget
4. Back

Enter choice:
```

**Set Budget:**

```
Enter choice: 1
Enter category: Food
Enter budget amount: 500
Food budget created.
```

**View Budgets:**

```
Enter choice: 2

Budgets:
------------------------------------------------------------
ID: 1 | Category: Food    | Limit: $500.00 | Spent: $200.00 | Remaining: $300.00
ID: 2 | Category: Travel  | Limit: $300.00 | Spent: $0.00   | Remaining: $300.00
ID: 3 | Category: Rent    | Limit: $1500.00| Spent: $1500.00| Remaining: $0.00
```

**Budget Warning (Exceeded):**

```
Enter choice: 1
Enter account ID: 1
Enter amount: 450
Enter category: Food
WARNING: Food budget exceeded!
$450.0 withdrawn successfully.
```

**Reset Budget:**

```
Enter choice: 3
Enter category: Food
Food budget reset.
```

---

### Financial Reports

**Submenu:**

```
--- Reports ---
1. Income Summary
2. Expense Summary
3. Spending By Category
4. Filter By Category
5. Filter By Type
6. Filter By Date
7. Back

Enter choice:
```

**Income Summary:**

```
Enter choice: 1

Total Income: $8500.00
```

**Expense Summary:**

```
Enter choice: 2

Total Expenses: $2350.00
```

**Spending By Category:**

```
Enter choice: 3

Spending By Category
--------------------------------------------------
Rent:    $1500.00
Food:    $450.00
Travel:  $300.00
General: $100.00
```

**Filter By Category:**

```
Enter choice: 4
Enter category: Food

Transactions for category: Food
--------------------------------------------------------------------------------
ID:4 | Acc:1 | Withdraw | $200.00 | 2026-04-24 10:19:45
ID:7 | Acc:1 | Withdraw | $250.00 | 2026-04-22 14:05:12
```

**Filter By Type:**

```
Enter choice: 5
Enter type: Deposit

Transactions of type: Deposit
--------------------------------------------------------------------------------
ID:5 | Acc:1 | $1500.00 | Deposit | 2026-04-24 10:21:03
ID:1 | Acc:1 | $5000.00 | Deposit | 2026-04-24 10:15:00
```

**Filter By Date:**

```
Enter choice: 6
Start date (YYYY-MM-DD): 2026-04-20
End date (YYYY-MM-DD): 2026-04-24

Transactions from 2026-04-20 to 2026-04-24
------------------------------------------------------------------------------------------
ID:5 | Acc:1 | Deposit     | $1500.00 | Deposit  | 2026-04-24 10:21:03
ID:4 | Acc:1 | Withdraw    | $200.00  | Food     | 2026-04-24 10:19:45
ID:3 | Acc:2 | Transfer In | $500.00  | Transfer | 2026-04-24 10:18:30
```

---

### Export Statements

**Submenu:**

```
--- Export Statements (CSV) ---
1. Export All Transactions
2. Export by Account
3. Export by Date Range
4. Export by Category
5. Back

Enter choice:
```

**Export All Transactions:**

```
Enter choice: 1
Enter filename (press Enter for default):
Transactions exported to 'transactions_export.csv' (12 records).
```

**Export by Account:**

```
Enter choice: 2
Enter account ID: 1
Enter filename (press Enter for default):
Account 1 statement exported to 'account_1_statement.csv' (8 records).
```

**Export by Date Range:**

```
Enter choice: 3
Start date (YYYY-MM-DD): 2026-04-01
End date (YYYY-MM-DD): 2026-04-30
Enter filename (press Enter for default):
Statement from 2026-04-01 to 2026-04-30 exported to 'statement_2026-04-01_to_2026-04-30.csv' (12 records).
```

**Export by Category:**

```
Enter choice: 4
Enter category: Food
Enter filename (press Enter for default):
Category 'Food' transactions exported to 'category_food_export.csv' (3 records).
```

The exported CSV files contain the following columns:

| Transaction ID | Account ID | Type | Amount | Category | Date |
|---|---|---|---|---|---|

---

### Visual Reports (Charts)

**Submenu:**

```
--- Visual Reports (Charts) ---
1. Spending by Category (Bar Chart)
2. Income vs Expenses (Bar Chart)
3. Spending Distribution (Pie Chart)
4. Daily Spending Trend (Line Chart)
5. Budget vs Spent (Grouped Bar Chart)
6. Back

Enter choice:
```

**Generate Chart:**

```
Enter choice: 1
Save as (press Enter for default):
Chart saved to 'spending_by_category.png'.
```

Charts generated by the system:

| Chart | Filename | Description |
|---|---|---|
| Spending by Category | `spending_by_category.png` | Bar chart of total withdrawals per category |
| Income vs Expenses | `income_vs_expenses.png` | Side-by-side bar comparison |
| Spending Distribution | `spending_pie.png` | Pie chart with category percentages |
| Daily Spending Trend | `transaction_trend.png` | Line chart of daily spend over time |
| Budget vs Spent | `budget_vs_spent.png` | Grouped bars showing limit vs actual spend |

All charts are saved as `.png` files at 150 DPI using matplotlib.

---

## Module Descriptions

| Module | Key Functions | Description |
|---|---|---|
| `main.py` | `main()`, `account_menu()`, `transaction_menu()`, etc. | Entry point; menu navigation and user input |
| `database.py` | `connect_db()` | Returns a psycopg2 database connection |
| `accounts.py` | `create_account()`, `view_accounts()`, `update_account()`, `delete_account()` | Full account lifecycle management |
| `transactions.py` | `deposit()`, `withdraw()`, `transfer()`, `view_transactions()` | Transaction processing with balance validation |
| `budgets.py` | `set_budget()`, `add_expense()`, `reset_budget()`, `view_budgets()` | Budget tracking with overspend warnings |
| `reports.py` | `income_summary()`, `expense_summary()`, `spending_by_category()`, `filter_by_*()` | Reporting and transaction filtering |
| `exports.py` | `export_all_transactions()`, `export_by_account()`, `export_by_date_range()`, `export_by_category()` | CSV export in multiple formats |
| `charts.py` | `chart_spending_by_category()`, `chart_income_vs_expenses()`, `chart_spending_pie()`, `chart_transaction_trend()`, `chart_budget_vs_spent()` | Matplotlib chart generation |

---

*Personal Finance Simulator — COMP9120 Project | University of Sydney, 2026*
