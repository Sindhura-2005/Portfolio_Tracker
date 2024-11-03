import requests

# Replace 'YOUR_API_KEY' with your actual Alpha Vantage API key
API_KEY = 'YOUR_API_KEY'

def get_stock_data(symbol):
    """Fetch real-time stock data from Alpha Vantage."""
    url = f'https://www.alphavantage.co/query?function=GLOBAL_QUOTE&symbol={symbol}&apikey={API_KEY}'
    response = requests.get(url)
    data = response.json()
    if 'Global Quote' in data and '05. price' in data['Global Quote']:
        return data['Global Quote']
    else:
        print(f"Error fetching data for symbol: {symbol}. Please check if the symbol is correct.")
        return None


class Portfolio:
    def __init__(self):
        """Initialize an empty portfolio."""
        self.stocks = {}

    def add_stock(self, symbol, quantity):
        """Add stock to the portfolio."""
        if symbol in self.stocks:
            self.stocks[symbol] += quantity
        else:
            self.stocks[symbol] = quantity

    def remove_stock(self, symbol, quantity):
        """Remove stock from the portfolio."""
        if symbol in self.stocks:
            if self.stocks[symbol] >= quantity:
                self.stocks[symbol] -= quantity
                if self.stocks[symbol] == 0:
                    del self.stocks[symbol]
                print(f"Removed {quantity} shares of {symbol} from the portfolio.")
            else:
                print(f"Cannot remove {quantity} shares of {symbol}. You only own {self.stocks[symbol]} shares.")
        else:
            print(f"{symbol} is not in your portfolio.")

    def get_portfolio_value(self):
        """Calculate the total value of the portfolio."""
        total_value = 0
        for symbol, quantity in self.stocks.items():
            stock_data = get_stock_data(symbol)
            if stock_data:
                price = float(stock_data['05. price'])
                total_value += price * quantity
        return total_value


def calculate_gain_loss(initial_value, current_value):
    """Calculate gains/losses."""
    return current_value - initial_value


def main():
    portfolio = Portfolio()
    initial_value = 0

    while True:
        print("\n1. Add stock to portfolio")
        print("2. Remove stock from portfolio")
        print("3. View portfolio value")
        print("4. Exit")

        choice = input("Enter your choice: ")

        if choice == '1':
            symbol = input("Enter stock symbol: ").upper()
            quantity = int(input("Enter quantity: "))
            portfolio.add_stock(symbol, quantity)
            print(f"Added {quantity} shares of {symbol} to the portfolio.")

        elif choice == '2':
            symbol = input("Enter stock symbol: ").upper()
            quantity = int(input("Enter quantity: "))
            portfolio.remove_stock(symbol, quantity)

        elif choice == '3':
            current_value = portfolio.get_portfolio_value()
            print(f"Current Portfolio Value: ${current_value:.2f}")
            if initial_value == 0:
                initial_value = current_value
            print(f"Gains/Losses: ${calculate_gain_loss(initial_value, current_value):.2f}")

        elif choice == '4':
            print("Exiting the program.")
            break

        else:
            print("Invalid choice. Please try again.")


if __name__ == "__main__":
    main()
