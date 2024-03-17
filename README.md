# 3-day-up-strategy

This strategy, often referred to as the "3 Day Up" strategy, is based on identifying a specific pattern in the price movement of a security over a four-day period. Here's a detailed explanation of the strategy:

**Strategy Conditions:**
First Day (Day 1):
The closing price of the security is lower than the opening price.

Second Day (Day 2):
The opening price is below the closing price of the previous day (Day 1).

Third Day (Day 3):
The opening price is higher than the opening price of the previous day (Day 2).

Fourth Day (Day 4):
The opening price is higher than the opening price of the previous day (Day 3).
The closing price is higher than the opening price.
The closing price of the previous day (Day 3) is higher than its opening price.
The closing price of two days ago (Day 2) is higher than its opening price.

**Strategy Implementation:**
First Day (Day 1) to Fourth Day (Day 4):
The strategy iterates through the data, starting from the fourth day onwards, as it requires at least four days of data.
It checks each day against the specified conditions outlined above.
If all conditions are met for a particular day, a buy signal (1) is generated; otherwise, no signal (0) is generated.

**Backtesting the Strategy:
Entry and Exit Points:**
If a buy signal is generated on the fourth day, the strategy enters a long position at the close of that day.
It then exits the position at the opening price of the next day (fifth day).

**Returns Calculation:**
The strategy calculates the returns based on the difference between the exit price and the entry price, normalized by the entry price.
