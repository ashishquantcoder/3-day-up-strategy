#3 Day Up strategy
'''
Strategy: 
* SPY must be up three days in a row  and each day open should increase (from close to close).
* Entry on close on the 3rd up day.
* Exit the next day open.
*  Its an overnight trading strategy, the lowest-hanging fruit in the stock market
'''
import yfinance as yf
import numpy as np
import pandas as pd
import matplotlib.pyplot as plt
from scipy.stats import skew, kurtosis

# Load data from Yahoo Finance for SPY
spy_data = yf.download('SPY', start='1999-01-01', end='2024-03-15')


def implement_strategy(data):
    signals = []
    for i in range(4, len(data)):
        # Check conditions for the strategy
        if (data['Close'].iloc[i-3] < data['Open'].iloc[i-3] and                # Closing price lower than open on first day
            data['Open'].iloc[i-2] < data['Close'].iloc[i-3] and                # Second day's open below previous day's close
            data['Open'].iloc[i-1] > data['Open'].iloc[i-2] and                # Third day's open above second day's open
            data['Open'].iloc[i] > data['Open'].iloc[i-1] and                # Fourth day's open above third day's open
            data['Close'].iloc[i] > data['Open'].iloc[i] and               # Fourth day's close above open
            data['Close'].iloc[i-1] > data['Open'].iloc[i-1] and               # Third day's close above open
            data['Close'].iloc[i-2] > data['Open'].iloc[i-2]):                 # Second day's close above open
            signals.append(1)  # Buy signal
        else:
            signals.append(0)  # No signal
    return signals

# Backtest the strategy
def backtest_strategy(data, signals):
    returns = []
    dates = []
    for i in range(4, len(data)):
        if i < len(signals):  # Ensure signals list is long enough
            if signals[i] == 1:  # Entry on close on the fourth day
                entry_price = data['Close'].iloc[i]
                exit_price = data['Open'].iloc[i+1]  # Exit the next day open
                returns.append((exit_price - entry_price) / entry_price)
                dates.append(data.index[i])
    return dates, returns

# Implement strategy on SPY data
signals = implement_strategy(spy_data)
dates, returns = backtest_strategy(spy_data, signals)

# Convert backtest results to DataFrame
backtest_results = pd.DataFrame({'Date': dates, 'Returns': returns})
backtest_results.set_index('Date', inplace=True)

# Risk-free rate assumption
risk_free_rate = 0.0

# Benchmark returns (S&P 500 index)
benchmark_data = yf.download('^GSPC', start='2023-01-01', end='2024-01-01')
benchmark_data['Benchmark Returns'] = benchmark_data['Adj Close'].pct_change()

# Calculate additional parameters
annual_return = backtest_results['Returns'].mean() * 252
annual_volatility = backtest_results['Returns'].std() * np.sqrt(252)
beta = backtest_results['Returns'].cov(benchmark_data['Benchmark Returns']) / benchmark_data['Benchmark Returns'].var()
cumulative_returns = backtest_results['Returns'].cumsum()
max_drawdown = (1 - (1 + cumulative_returns) / (1 + cumulative_returns.cummax())).max()
calmar_ratio = annual_return / max_drawdown
stability = annual_return / annual_volatility
omega_ratio = (annual_return - risk_free_rate) / np.abs(max_drawdown)
sortino_ratio = annual_return / (backtest_results['Returns'][backtest_results['Returns'] < 0].std() * np.sqrt(252))
skewness = skew(backtest_results['Returns'])
kurt = kurtosis(backtest_results['Returns'])
tail_ratio = backtest_results['Returns'][backtest_results['Returns'] < 0].mean() / backtest_results['Returns'][backtest_results['Returns'] > 0].mean()
daily_var = backtest_results['Returns'].quantile(0.05)
alpha = annual_return - risk_free_rate - beta * (benchmark_data['Benchmark Returns'].mean() - risk_free_rate)

# Total number of winning and losing trades
winning_trades = len(backtest_results[backtest_results['Returns'] > 0])
losing_trades = len(backtest_results[backtest_results['Returns'] < 0])
total_trades = winning_trades + losing_trades

# Total return over the entire period (cumulative returns)
total_return = backtest_results['Returns'].sum()

# Average return per trade
average_return_per_trade = total_return / total_trades

# Sharpe Ratio (assuming risk-free rate of 0)
sharpe_ratio = (average_return_per_trade * 252) / (backtest_results['Returns'].std() * np.sqrt(252))

# Total trades win rate
win_rate = winning_trades / total_trades

# Print the results
print("Calmar Ratio:", calmar_ratio)
print("Stability:", stability)
print("Omega Ratio:", omega_ratio)
print("Sortino Ratio:", sortino_ratio)
print("Skewness:", skewness)
print("Kurtosis:", kurt)
print("Tail Ratio:", tail_ratio)
print("Daily Value at Risk:", daily_var)
print("Alpha:", alpha)
print("Beta:", beta)
print("Total Trades:", total_trades)
print("Winning Trades:", winning_trades)
print("Losing Trades:", losing_trades)
print(f"Total Return: {total_return:.2%}")
print(f"Average Return per Trade: {average_return_per_trade:.2%}")
print("Sharpe Ratio:", sharpe_ratio)
print(f"Win Rate: {win_rate:.2%}")


# Plot strategy returns
plt.figure(figsize=(10, 6))
backtest_results['Returns'].cumsum().plot()
plt.title('Strategy Returns')
plt.xlabel('Date')
plt.ylabel('Cumulative Returns')
plt.grid(True)
plt.show()

# Plot drawdown
plt.figure(figsize=(10, 6))
drawdown = cumulative_returns - cumulative_returns.cummax()
drawdown.plot(color='red')
plt.title('Drawdown Plot')
plt.xlabel('Date')
plt.ylabel('Drawdown')
plt.grid(True)
plt.show()

# Filter dates for buy signals
buy_dates = backtest_results.index[backtest_results['Returns'] > 0]

# Plot strategy signal
plt.figure(figsize=(10, 6))
plt.plot(spy_data.index, spy_data['Close'], label='SPY Close Price')

# Overlay buy signals
buy_dates = backtest_results.index[backtest_results['Returns'] > 0]
plt.scatter(buy_dates, spy_data.loc[buy_dates, 'Close'], marker='o', color='green', label='Buy Signal')

plt.title('SPY Price Chart with Buy Signals')
plt.xlabel('Date')
plt.ylabel('Price')
plt.legend()
plt.grid(True)
plt.show()
