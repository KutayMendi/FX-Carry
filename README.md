Fx-Carry Project
----
This repo contains my attempt at coding up a simple FX carry strategy with volatility targeting. The idea is pretty classic: rank currencies by interest rate differentials, go long the high yielders, short the low yielders, and size the portfolio so that overall risk stays stable.

Data
----
- data/fred/ : the daily FX close levels from Federal Reserve Economic Data from Federal Reserve Bank of St Louis
- data/bis/ : monthly policy/short rates from Bank of International Settlements

Method
----
1) First I gather the FX levels and policy rates, and align them onto a table with a daily index
2) Then I calculate carry, which is simply base-quote differential for any currency pair XXXYYY
3) At the start of any given month (I use start of month only because the rate data is monthly, so no need to calculate differential for every day) I rank pairs by their differential: top_n = longs, bottom_n = short
4) I forward fill these weights through the month, then apply simple vol targetting:
   - Estimate realised vol over a rolling window,
   - then scale portfolio leverage up/down to hit the targel annual vol
6) Here I account for transcation costs, then calculate metrics such as pnl, equity curve, sharpe, mdd etc

Results
----
- The code works well (weights, vol targetting, turnover, etc) but the strategies performance since 2022 was poor
- This more so reflects the market reality: carry became unviable when USD rates inceased, while safer currencies rallied in a risk-off regime.
- I found that adjusting to a longer vol window makes the leveraging smoother and makes the results look better in the backtest, but long vol windows could backfire in a market crash event like 2008
- By cumulating transcation costs, you can see how even small ones can degrade sharpe
- I also found that top_n = bottom_n = 2 is a sweet spot no matter what the other parameters are.
- Increasing target vol boosts our leverage, so both returns and drawdown gets magnified. However, sharpe doesnt chane much as this is a classic risk/return trade off
- I attached a couple graphics and metrics associated with different combinations of parameter values in this repo

Bloopers
----
In the data/results/folder, along with a couple graphics for various parameter values, is two files called mistake.png and mistake2.png. 
- For mistake.png, it is quite obvious an error in my logic caused the equity to constant remain at 1.0, only dropping discrete chunks becuase of lossed due to tiny transcation costs.
- For mistake2.png, this graph looks suspiciously like the EURUSD fx rate for the 5 year period! I made a mistake in a loop which led to all the currencies in the tables taking the EURUSD fx rate values.





