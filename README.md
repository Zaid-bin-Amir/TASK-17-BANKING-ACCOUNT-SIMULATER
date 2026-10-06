from datetime import datetime

def create_account(owner, opening_balance=0.0):
    return {
        "owner": owner,
        "balance": opening_balance,
        "transactions": [],
    }


def record_transaction(account, kind, amount):
    entry = {
        "time": datetime.now().strftime("%Y-%m-%d %H:%M:%S"),
        "type": kind,
        "amount": amount,
        "balance_after": account["balance"],
    }
    account["transactions"].append(entry)


def read_amount(prompt):
    while True:
        raw = input(prompt).strip()
        try:
            value = float(raw)
        except ValueError:
            print("Invalid input. Please enter a number.")
            continue
        if value <= 0:
            print("Amount must be greater than zero.")
            continue
        return round(value, 2)


def check_balance(account):
    print(f"\nCurrent balance: {account['balance']:.2f}")


def deposit(account):
    amount = read_amount("Enter amount to deposit: ")
    account["balance"] += amount
    record_transaction(account, "Deposit", amount)
    print(f"Deposited {amount:.2f}. New balance: {account['balance']:.2f}")


def withdraw(account):
    amount = read_amount("Enter amount to withdraw: ")
    if amount > account["balance"]:
        print(
            f"Insufficient funds. Available balance: {account['balance']:.2f}"
        )
        return
    account["balance"] -= amount
    record_transaction(account, "Withdrawal", amount)
    print(f"Withdrew {amount:.2f}. New balance: {account['balance']:.2f}")


def show_history(account):
    history = account["transactions"]
    if not history:
        print("\nNo transactions yet.")
        return
    print("\n" + "=" * 70)
    print(f"{'Time':<22}{'Type':<14}{'Amount':>14}{'Balance':>16}")
    print("-" * 70)
    for item in history:
        print(
            f"{item['time']:<22}{item['type']:<14}"
            f"{item['amount']:>14.2f}{item['balance_after']:>16.2f}"
        )
    print("=" * 70)


def show_menu():
    print("\n===== BANK ACCOUNT SIMULATOR =====")
    print("1. Check Balance")
    print("2. Deposit")
    print("3. Withdraw")
    print("4. Transaction History")
    print("5. Exit")


def read_opening_balance():
    while True:
        raw = input("Enter opening balance (0 for none): ").strip()
        try:
            value = float(raw)
        except ValueError:
            print("Invalid input. Please enter a number.")
            continue
        if value < 0:
            print("Opening balance cannot be negative.")
            continue
        return round(value, 2)


def read_name():
    while True:
        name = input("Enter account holder name: ").strip()
        if name:
            return name
        print("Name cannot be empty.")


def main():
    owner = read_name()
    opening = read_opening_balance()
    account = create_account(owner, opening)
    if opening > 0:
        record_transaction(account, "Opening", opening)

    print(f"\nWelcome, {account['owner']}!")

    actions = {
        "1": check_balance,
        "2": deposit,
        "3": withdraw,
        "4": show_history,
    }

    while True:
        show_menu()
        choice = input("Choose an option (1-5): ").strip()
        if choice == "5":
            print(f"\nThank you for banking with us, {account['owner']}. Goodbye!")
            break
        action = actions.get(choice)
        if action is None:
            print("Invalid choice. Please select a number from 1 to 5.")
            continue
        action(account)


if __name__ == "__main__":
    main()
