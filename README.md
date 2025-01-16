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

# Data Preparation 

I used this python script to take the first and second set of outputs and exported to an excel spreadsheet where I combined both sets.

```ruby
# Meme coins list with correct CoinGecko IDs
MEME_COINS = [
    {"id": "dogecoin", "symbol": "DOGE"},
    {"id": "shiba-inu", "symbol": "SHIB"},
    {"id": "pepe", "symbol": "PEPE"},
    {"id": "floki", "symbol": "FLOKI"},
    {"id": "bonk", "symbol": "BONK"},
]

# Path to save the CSV file
SAVE_PATH = 

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
    print("Fetching 7-day historical price data for selected meme coins...\n")

    all_data = {}
    for coin in MEME_COINS:
        coin_id = coin["id"]
        coin_symbol = coin["symbol"]
        print(f"Fetching data for {coin_symbol}...")
        price_data = fetch_7day_price(coin_id)

        if price_data:
            # Convert price data to DataFrame
            df = pd.DataFrame(price_data, columns=["Timestamp", "Price"])
            df["Timestamp"] = pd.to_datetime(df["Timestamp"], unit="ms")  # Convert to readable datetime
            df.set_index("Timestamp", inplace=True)  # Set timestamp as the index
            df = df.resample('1D').mean()  # Resample to daily average prices
            
            # Format prices to 8 decimal places
            df["Price"] = df["Price"].apply(lambda x: round(x, 8) if pd.notna(x) else x)
            all_data[coin_symbol] = df.rename(columns={"Price": coin_symbol})  # Store cleaned data
        else:
            print(f"No data found for {coin_symbol}.\n")

    # Combine all data into a single DataFrame
    if all_data:
        combined_df = pd.concat(all_data.values(), axis=1)  # Merge all coin DataFrames on the index
        combined_df = combined_df.sort_index()  # Sort the data by timestamp

        # Display the cleaned combined data
        print("Cleaned Combined Price Data (Daily Average):")
        print(combined_df.head())

        # Save the cleaned data to a CSV file
        file_name = "meme_coins_daily_cleaned.csv"
        save_file_path = os.path.join(SAVE_PATH, file_name)
        combined_df.to_csv(save_file_path, index=True, float_format="%.8f")
        print(f"\nData successfully saved to: {save_file_path}")
    else:
        print("No data fetched for any meme coins.")

# Updated meme coins list with correct CoinGecko IDs
MEME_COINS = [
    {"id": "brett", "symbol": "BRETT"},
    {"id": "mog-coin", "symbol": "MOG"},
    {"id": "popcat", "symbol": "POPCAT"},
]

# Path to save the CSV file
SAVE_PATH = 

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
    print("Fetching 7-day historical price data for BRETT, MOG, and POPCAT...\n")

    all_data = {}
    for coin in MEME_COINS:
        coin_id = coin["id"]
        coin_symbol = coin["symbol"]
        print(f"Fetching data for {coin_symbol}...")
        price_data = fetch_7day_price(coin_id)

        if price_data:
            # Convert price data to DataFrame
            df = pd.DataFrame(price_data, columns=["Timestamp", "Price"])
            df["Timestamp"] = pd.to_datetime(df["Timestamp"], unit="ms")  # Convert to readable datetime
            df.set_index("Timestamp", inplace=True)  # Set timestamp as the index
            df = df.resample('1D').mean()  # Resample to daily average prices
            
            # Format prices to 8 decimal places
            df["Price"] = df["Price"].apply(lambda x: round(x, 8) if pd.notna(x) else x)
            all_data[coin_symbol] = df.rename(columns={"Price": coin_symbol})  # Store cleaned data
        else:
            print(f"No data found for {coin_symbol}.\n")

    # Combine all data into a single DataFrame
    if all_data:
        combined_df = pd.concat(all_data.values(), axis=1)  # Merge all coin DataFrames on the index
        combined_df = combined_df.sort_index()  # Sort the data by timestamp

        # Display the cleaned combined data
        print("Cleaned Combined Price Data (Daily Average):")
        print(combined_df.head())

        # Save the cleaned data to a CSV file
        file_name = "meme_coins_daily_cleaned_set2.csv"
        save_file_path = os.path.join(SAVE_PATH, file_name)
        combined_df.to_csv(save_file_path, index=True, float_format="%.8f")
        print(f"\nData successfully saved to: {save_file_path}")
    else:
        print("No data fetched for any meme coins.")

# Correlation Analysis 
The analysis began by calculating the correlation matrix for the selected meme coins. A high correlation (greater than 0.7) indicates that the prices of two coins tend to move together, either positively or negatively. Below is the python script for all  the correlated coins and a normalized line graph. 

```ruby
# Read the uploaded CSV directly
combined_df = pd.read_csv("meme_coins_daily_cleaned_set2.csv", index_col="Timestamp", parse_dates=True)
correlation_matrix = combined_df.corr()

# Display the correlation matrix in a clear format
print("Correlation Table Between Meme Coins:")
print(correlation_matrix)

# Identify the top correlated pairs (correlation > 0.7 for example)
threshold = 0.7
high_correlation_pairs = []

for coin1 in correlation_matrix.columns:
    for coin2 in correlation_matrix.columns:
        if coin1 != coin2 and correlation_matrix.loc[coin1, coin2] > threshold:
            high_correlation_pairs.append((coin1, coin2, correlation_matrix.loc[coin1, coin2]))

# Display the most correlated pairs
print("\nHighly Correlated Pairs (Correlation > 0.7):")
for pair in high_correlation_pairs:
    print(f"{pair[0]} and {pair[1]}: Correlation = {pair[2]:.2f}")

# Analyze how much one coin's price increase affects the other (percentage change impact)
print("\nImpact Analysis: How Much One Coin's Price Increase Affects Others")

# Calculate percentage change for all coins
percentage_changes = combined_df.pct_change().dropna()

# Compute the average impact of each coin on others
for coin1, coin2, _ in high_correlation_pairs:
    # Calculate the average ratio of price changes between the two coins
    avg_impact = (percentage_changes[coin2] / percentage_changes[coin1]).mean()
    print(f"When {coin1} increases, {coin2} changes on average by {avg_impact:.2f} times.")
```
<img width="354" alt="Image" src="https://github.com/user-attachments/assets/6f53a6af-f338-4abc-bf13-153dfdabeeac" />
<img width="396" alt="Image" src="https://github.com/user-attachments/assets/c51cd96c-129b-4ae3-bdce-99b3ae7d53be" />

### Visual 

```ruby
normalized_df = combined_df / combined_df.iloc[0] * 100  # Start at 100%

# Plot the normalized line graph
plt.figure(figsize=(12, 8))
for coin in normalized_df.columns:
    plt.plot(normalized_df.index, normalized_df[coin], label=coin)

# Add labels, title, and legend
plt.xlabel("Date")
plt.ylabel("Normalized Price (%)")
plt.title("Normalized Price Movements of Meme Coins Over Time")
plt.legend()
plt.grid(True)

# Show the graph
plt.show()

```

<img width="678" alt="Image" src="https://github.com/user-attachments/assets/19e0e576-052c-4927-b39d-2e68807f3724" />

# Selected Coins For Strategy 

## DOGE: The First and Most Well-Known Meme Coin
- Dogecoin (DOGE) was the first meme coin ever created, launched in 2013 as a parody of Bitcoin.
- Its position as the original meme coin makes it the most recognizable and well-known in the market.
- Due to its long-standing history and strong community backing, DOGE often acts as a leading indicator for other meme coins.

## SHIB and BONK: High Liquidity and Market Influence
- Shiba Inu (SHIB): Often referred to as the "DOGE killer," SHIB has grown into one of the most traded meme coins, with a massive community and significant liquidity.
- BONK: A newer meme coin that has gained rapid popularity, showing high volatility and correlation with established coins like DOGE and SHIB.

### Strong Correlations
The analysis revealed that DOGE, SHIB, and BONK exhibit exceptionally high correlations:

- DOGE and SHIB: Correlation = 0.86
- DOGE and BONK: Correlation = 0.83
- SHIB and BONK: Correlation = 0.83

### Volatility for Profit Potential
Meme coins are inherently volatile, but this volatility presents an opportunity:

- Coins like DOGE, SHIB, and BONK exhibit sharp price swings, allowing traders to capitalize on short-term upward movements.
- Their strong correlation amplifies gains when multiple coins move together.
- When DOGE increases, SHIB tends to increase by an average of 2.16 timesThis amplification effect allows traders to maximize profits while managing risk.

# Impact Analysis 
The impact analysis quantifies how price movements of one meme coin influence others based on historical price changes. The analysis focuses on DOGE, SHIB, and BONK . 

```ruby
# Load the dataset
combined_df = pd.read_csv("meme_coins_daily_cleaned_set2.csv", index_col="Timestamp", parse_dates=True)



# Filter only the selected coins: DOGE, SHIB, BONK
selected_coins = ["DOGE", "SHIB", "BONK"]
combined_df = combined_df[selected_coins]

# Calculate the correlation matrix
correlation_matrix = combined_df.corr()

# Display the correlation matrix in a clear format
print("Correlation Table Between Selected Meme Coins (DOGE, SHIB, BONK):")
print(correlation_matrix)

# Identify the top correlated pairs (correlation > 0.7)
threshold = 0.7
high_correlation_pairs = []

for coin1 in correlation_matrix.columns:
    for coin2 in correlation_matrix.columns:
        if coin1 != coin2 and correlation_matrix.loc[coin1, coin2] > threshold:
            high_correlation_pairs.append((coin1, coin2, correlation_matrix.loc[coin1, coin2]))

# Display the most correlated pairs
print("\nHighly Correlated Pairs (Correlation > 0.7):")
for pair in high_correlation_pairs:
    print(f"{pair[0]} and {pair[1]}: Correlation = {pair[2]:.2f}")

# Analyze how much one coin's price increase affects the other (percentage change impact)
print("\nImpact Analysis: How Much One Coin's Price Increase Affects Others")

# Calculate percentage change for all selected coins
percentage_changes = combined_df.pct_change().dropna()

# Compute the average impact of each coin on others
for coin1, coin2, _ in high_correlation_pairs:
    # Calculate the average ratio of price changes between the two coins
    avg_impact = (percentage_changes[coin2] / percentage_changes[coin1]).mean()
    print(f"When {coin1} increases by 1 unit, {coin2} changes on average by {avg_impact:.6f} units.")
 


```
<img width="353" alt="Image" src="https://github.com/user-attachments/assets/9daac040-00a6-4055-b613-6d883f5b3bbd" />

## DOGE 
- When DOGE increases by 1 unit,
- SHIB increases on average by 2.16 units.
- BONK increases on average by 2.29 units.
DOGE has a strong positive influence on both SHIB and BONK,

## SHIB 
- When SHIB increases by 1 unit,
- DOGE increases on average by 0.24 units.
- BONK increases on average by 1.22 units.
SHIB has a more pronounced effect on BONK compared to DOGE, suggesting a closer relationship between SHIB and BONK.

## BONK 
- When BONK increases by 1 unit,
- DOGE increases on average by 0.64 units.
- SHIB increases on average by 0.13 units.
BONK's influence on the other two coins is weaker but still noticeable, with DOGE responding more strongly than SHIB.
