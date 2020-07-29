# Trading-Technicals-Basic
All sorts of technical indicators that can be implemented in to any systematic trading strategy

#MACD - Trend (lagging) Following Momentum Indicator.  The difference of 2 moving averages (often the 12 and the 26)
#AS ALWAYS, IMPORT OUR LIBRARIES

import yfinance as yf
import datetime as dt
import matplotlib.pyplot as plt

#RECORD HISTOICAL DATA OVER OUR CHOSEN TIME PERIOD

ticker = "TSLA"
ohlcv = yf.download(ticker,dt.date.today()-dt.timedelta(1825),dt.datetime.today())

def MACD(DF,a,b,c):
    """function to calculate MACD
       typical values a = 12; b =26, c =9"""
    df = DF.copy()
    df["MA_Fast"]=df["Adj Close"].ewm(span=a,min_periods=a).mean()
    df["MA_Slow"]=df["Adj Close"].ewm(span=b,min_periods=b).mean()
    df["MACD"]=df["MA_Fast"]-df["MA_Slow"]
    df["Signal"]=df["MACD"].ewm(span=c,min_periods=c).mean()
    df.dropna(inplace=True)
    return df

#HERE WE WILL VISUALIZE OUR DATA

df = MACD(ohlcv, 12, 26, 9)

plt.subplot(311)
plt.plot(df.iloc[-100:,4])
plt.title('MSFT Stock Price')
plt.xticks([])

plt.subplot(312)
plt.bar(df.iloc[-100:,5].index, df.iloc[-100:,5].values)
plt.title('Volume')
plt.xticks([])

plt.subplot(313)
plt.plot(df.iloc[-100:,[-2,-1]])
plt.title('MACD')
plt.legend(('MACD','Signal'),loc='lower right')

plt.show()


#MORE VISUALIZATION HERE

fig, (ax0, ax1) = plt.subplots(nrows=2,ncols=1, sharex=True, sharey=False, figsize=(10, 6), gridspec_kw = {'height_ratios':[2.5, 1]})
df.iloc[-100:,4].plot(ax=ax0)
ax0.set(ylabel='Adj Close')

df.iloc[-100:,[-2,-1]].plot(ax=ax1)
ax1.set(xlabel='Date', ylabel='MACD/Signal')

#CHART TITLE
fig.suptitle('Stock Price with MACD', fontsize=14, fontweight='bold')
