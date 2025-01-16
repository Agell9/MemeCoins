# MemeCoins
### Objective
The objective of this analysis was to examine the price movements of popular meme coins, identify relationships and correlations between them, and develop a trading strategy that reduces risk while capitalizing on the volatile nature of meme coins.


# Data Source 

The data for this analysis was pulled using the CoinGecko API, which provides reliable cryptocurrency price and market data.

API Endpoint: https://api.coingecko.com/api/v3/coins/{id}/market_chart?vs_currency=usd&days=7
The API delivers 7-day historical price data for selected meme coins.

### Data Collection 

The following Python script was used to fetch 7-day historical price data for the selected meme coins using the CoinGecko API. 2 separate sets had to be created because of the API limitations. 

The first set fetched the data for DOGE, SHIB, PEPE, FLOKI, and BONK. 

The second set fetched the data for BRETT,MOG and POPCAT.   

```ruby
         import requests
         import pandas as pd

# Updated meme coins list with correct CoinGecko IDs
MEME_COINS = [
    {"id": "dogecoin", "symbol": "DOGE"},
    {"id": "shiba-inu", "symbol": "SHIB"},
    {"id": "pepe", "symbol": "PEPE"},
    {"id": "floki", "symbol": "FLOKI"},
    {"id": "bonk", "symbol": "BONK"},]
   


# Function to fetch 7-day historical price data for a coin
def fetch_7day_price(coin_id):
    url = f"https://api.coingecko.com/api/v3/coins/{coin_id}/market_chart?vs_currency=usd&days=7"
    try:
        response = requests.get(url)
        if response.status_code == 200:
            prices = response.json().get("prices", [])  # List of [timestamp, price]
            return prices
        else:
            print(f"Failed to fetch data for {coin_id}. HTTP {response.status_code}")
            return []
    except Exception as e:
        print(f"An error occurred while fetching {coin_id}: {e}")
        return []

# Main execution
if __name__ == "__main__":
    print("Fetching 7-day historical price data for DOGE, SHIB, PEPE, FLOKI, and BONK...\n")

    all_data = []
    for coin in MEME_COINS:
        coin_id = coin["id"]
        coin_symbol = coin["symbol"]
        print(f"Fetching data for {coin_symbol}...")
        price_data = fetch_7day_price(coin_id)

        if price_data:
            # Convert price data to DataFrame
            df = pd.DataFrame(price_data, columns=["Timestamp", "Price"])
            df["Timestamp"] = pd.to_datetime(df["Timestamp"], unit="ms")  # Keep full timestamp
            df.set_index("Timestamp", inplace=True)
            df.rename(columns={"Price": coin_symbol}, inplace=True)
            all_data.append(df)

            # Display the first few rows of price data for the current coin
            print(f"Price Data for {coin_symbol} (Last 7 Days):")
            print(df.head(), "\n")
        else:
            print(f"No data found for {coin_symbol}.\n")

import requests
import pandas as pd

# Updated meme coins list with correct CoinGecko IDs
MEME_COINS = [
   
    {"id": "brett", "symbol": "BRETT"},
    {"id": "mog-coin", "symbol": "MOG"},
    {"id": "popcat", "symbol": "POPCAT"}]


# Function to fetch 7-day historical price data for a coin
def fetch_7day_price(coin_id):
    url = f"https://api.coingecko.com/api/v3/coins/{coin_id}/market_chart?vs_currency=usd&days=7"
    try:
        response = requests.get(url)
        if response.status_code == 200:
            prices = response.json().get("prices", [])  # List of [timestamp, price]
            return prices
        else:
            print(f"Failed to fetch data for {coin_id}. HTTP {response.status_code}")
            return []
    except Exception as e:
        print(f"An error occurred while fetching {coin_id}: {e}")
        return []

# Main execution
if __name__ == "__main__":
    print("Fetching 7-day historical price data for DOGE, SHIB, PEPE, FLOKI, and BONK...\n")

    all_data = []
    for coin in MEME_COINS:
        coin_id = coin["id"]
        coin_symbol = coin["symbol"]
        print(f"Fetching data for {coin_symbol}...")
        price_data = fetch_7day_price(coin_id)

        if price_data:
            # Convert price data to DataFrame
            df = pd.DataFrame(price_data, columns=["Timestamp", "Price"])
            df["Timestamp"] = pd.to_datetime(df["Timestamp"], unit="ms")  # Keep full timestamp
            df.set_index("Timestamp", inplace=True)
            df.rename(columns={"Price": coin_symbol}, inplace=True)
            all_data.append(df)

            # Display the first few rows of price data for the current coin
            print(f"Price Data for {coin_symbol} (Last 7 Days):")
            print(df.head(), "\n")
        else:
            print(f"No data found for {coin_symbol}.\n")
 ```

