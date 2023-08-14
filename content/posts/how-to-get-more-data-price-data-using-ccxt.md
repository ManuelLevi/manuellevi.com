+++
author = "Manuel Levi"
categories = ["Crypto", "python"]
date = 2022-07-26T09:55:00Z
description = ""
draft = false
slug = "how-to-get-more-data-price-data-using-ccxt"
tags = ["Crypto", "python"]
title = "How to get historical price data using CCXT (over 500 and 1000 rows)"

+++


If you want to download crypto price data using Binance or other exchanges' API, you'll soon find it's not possible to download everything at once.

For example, doing this, using CCXT library with Binance:

<pre class="python-editor">
import ccxt
ex = ccxt.binance()
ohlcv = ex.fetch_ohlcv('BTC/USDT', '5m')</pre>

Would return you the last 500 five-minute candles with the Open, High, Low, Close, and Volume - hence "ohlcv".

## Increasing the limit

Depending on which API you are using, you should be able to increase the number of price information rows. For example, with Binance, that limit is 1000.

You can increase the limit with CCXT with the `limit` argument, like this:

<pre class="python-editor">
import ccxt
ex = ccxt.binance()
ohlcv = ex.fetch_ohlcv('BTC/USDT', '5m', limit=1000)</pre>

This will allow you to increase the 500 rows limit to 1000.

## Getting historical data

Obviously, this is not enough if you want to do any kind of relevant analysis. For that, you need to be able to fetch historical data.

You can do this with CCXT together with the Binance API using the `since` argument, like this:

<pre class="python-editor">
import ccxt
ex = ccxt.binance()
from_ts = ex.parse8601('2022-07-21 00:00:00')
ohlcv = ex.fetch_ohlcv('BTC/USDT', '5m', since=from_ts, limit=1000)
</pre>

As long as there are at least 1000 candles to be returned, you should get exactly 1000 data points.

## Getting data for a longer time period

You can also get data for a longer time period, the trick here is to do multiple API requests.

<pre class="python-editor">
from_ts = ex.parse8601('2022-07-21 00:00:00')
ohlcv_list = []
ohlcv = ex.fetch_ohlcv('BTC/USDT', '5m', since=from_ts, limit=1000)
ohlcv_list.append(ohlcv)
while True:
    from_ts = ohlcv[-1][0]
    new_ohlcv = ex.fetch_ohlcv('BTC/USDT', '5m', since=from_ts, limit=1000)
    ohlcv.extend(new_ohlcv)
    if len(new_ohlcv)!=1000:
    	break
</pre>

This code will get the first 1000 rows, download them, get their last date and request the API for the data created since it. You will get this data repeated in the dataset, I like that because it allows me to know clearly where the data was stitched, but this is a personal preference.

Once the request returns a number different from 1000, then we know we have the last data available

Note: APIs are typically throttled. This means that if you make enough requests in a too short period of time, your request might get refused. Simply add some sleep statements to the code to make sure you're not going above your limits.

## Converting it to a pandas dataframe

If you're doing this, there's a high probability you'll want to convert this data into a dataframe, so this is how to do it:

```python
df = pd.DataFrame(ohlcv, columns=['date', 'open', 'high', 'low', 'close', 'volume'])
df['date'] = pd.to_datetime(df['date'], unit='ms')
df.set_index('date', inplace=True)
df = df.sort_index(ascending=True)
df.head()
```

This code will not only create a `dataframe` with all the data, it will also convert dates into `datetimes` that panda can process and set them as index inplace. Just to make sure, the index is sorted too.

## Good luck!

Hope this was helpful for you. If it was, feel free to share what you're building [with me](https://www.linkedin.com/in/manuellevi)!

