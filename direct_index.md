1) Download all symbols and their weights in the S&P 500.


```python
import pandas as pd
import requests
import cloudscraper
from io import StringIO
from bs4 import BeautifulSoup

pd.set_option('display.max_rows', 50)

url = "https://www.slickcharts.com/sp500"
scraper = cloudscraper.create_scraper()
soup = BeautifulSoup(scraper.get(url).text, 'html.parser')
sap_table = soup.find('table', {'class':"table"})
sap_list = pd.read_html(StringIO(str(sap_table)), flavor='html5lib')[0]
sap = pd.DataFrame(sap_list)

sap = sap[["Company", "Symbol", "Portfolio%"]]
sap
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Company</th>
      <th>Symbol</th>
      <th>Portfolio%</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>Microsoft Corp</td>
      <td>MSFT</td>
      <td>7.14%</td>
    </tr>
    <tr>
      <th>1</th>
      <td>Apple Inc.</td>
      <td>AAPL</td>
      <td>6.08%</td>
    </tr>
    <tr>
      <th>2</th>
      <td>Nvidia Corp</td>
      <td>NVDA</td>
      <td>4.70%</td>
    </tr>
    <tr>
      <th>3</th>
      <td>Amazon.com Inc</td>
      <td>AMZN</td>
      <td>3.75%</td>
    </tr>
    <tr>
      <th>4</th>
      <td>Meta Platforms, Inc. Class A</td>
      <td>META</td>
      <td>2.58%</td>
    </tr>
    <tr>
      <th>...</th>
      <td>...</td>
      <td>...</td>
      <td>...</td>
    </tr>
    <tr>
      <th>498</th>
      <td>Zions Bancorporation N.a.</td>
      <td>ZION</td>
      <td>0.01%</td>
    </tr>
    <tr>
      <th>499</th>
      <td>V.F. Corporation</td>
      <td>VFC</td>
      <td>0.01%</td>
    </tr>
    <tr>
      <th>500</th>
      <td>Paramount Global Class B</td>
      <td>PARA</td>
      <td>0.01%</td>
    </tr>
    <tr>
      <th>501</th>
      <td>Fox Corporation Class B</td>
      <td>FOX</td>
      <td>0.01%</td>
    </tr>
    <tr>
      <th>502</th>
      <td>News Corporation Class B</td>
      <td>NWS</td>
      <td>0.01%</td>
    </tr>
  </tbody>
</table>
<p>503 rows × 3 columns</p>
</div>



2) Remove any equities you don't want to hold


```python
banned_tickers = ("TSLA", "FOX", "NWS")
sap = sap[~sap["Symbol"].isin(banned_tickers)].copy()
sap
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Company</th>
      <th>Symbol</th>
      <th>Portfolio%</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>Microsoft Corp</td>
      <td>MSFT</td>
      <td>7.14%</td>
    </tr>
    <tr>
      <th>1</th>
      <td>Apple Inc.</td>
      <td>AAPL</td>
      <td>6.08%</td>
    </tr>
    <tr>
      <th>2</th>
      <td>Nvidia Corp</td>
      <td>NVDA</td>
      <td>4.70%</td>
    </tr>
    <tr>
      <th>3</th>
      <td>Amazon.com Inc</td>
      <td>AMZN</td>
      <td>3.75%</td>
    </tr>
    <tr>
      <th>4</th>
      <td>Meta Platforms, Inc. Class A</td>
      <td>META</td>
      <td>2.58%</td>
    </tr>
    <tr>
      <th>...</th>
      <td>...</td>
      <td>...</td>
      <td>...</td>
    </tr>
    <tr>
      <th>496</th>
      <td>Mohawk Industries, Inc.</td>
      <td>MHK</td>
      <td>0.01%</td>
    </tr>
    <tr>
      <th>497</th>
      <td>Whirlpool Corp.</td>
      <td>WHR</td>
      <td>0.01%</td>
    </tr>
    <tr>
      <th>498</th>
      <td>Zions Bancorporation N.a.</td>
      <td>ZION</td>
      <td>0.01%</td>
    </tr>
    <tr>
      <th>499</th>
      <td>V.F. Corporation</td>
      <td>VFC</td>
      <td>0.01%</td>
    </tr>
    <tr>
      <th>500</th>
      <td>Paramount Global Class B</td>
      <td>PARA</td>
      <td>0.01%</td>
    </tr>
  </tbody>
</table>
<p>500 rows × 3 columns</p>
</div>



3. Also optionally clip weights


```python
sap["PortfolioFraction"] = sap["Portfolio%"].str.strip("%").astype('float') / 100
sap["PortfolioFraction"] = sap["PortfolioFraction"].clip(lower=0, upper=0.05)
sap
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Company</th>
      <th>Symbol</th>
      <th>Portfolio%</th>
      <th>PortfolioFraction</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>Microsoft Corp</td>
      <td>MSFT</td>
      <td>7.14%</td>
      <td>0.0500</td>
    </tr>
    <tr>
      <th>1</th>
      <td>Apple Inc.</td>
      <td>AAPL</td>
      <td>6.08%</td>
      <td>0.0500</td>
    </tr>
    <tr>
      <th>2</th>
      <td>Nvidia Corp</td>
      <td>NVDA</td>
      <td>4.70%</td>
      <td>0.0470</td>
    </tr>
    <tr>
      <th>3</th>
      <td>Amazon.com Inc</td>
      <td>AMZN</td>
      <td>3.75%</td>
      <td>0.0375</td>
    </tr>
    <tr>
      <th>4</th>
      <td>Meta Platforms, Inc. Class A</td>
      <td>META</td>
      <td>2.58%</td>
      <td>0.0258</td>
    </tr>
    <tr>
      <th>...</th>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
    </tr>
    <tr>
      <th>496</th>
      <td>Mohawk Industries, Inc.</td>
      <td>MHK</td>
      <td>0.01%</td>
      <td>0.0001</td>
    </tr>
    <tr>
      <th>497</th>
      <td>Whirlpool Corp.</td>
      <td>WHR</td>
      <td>0.01%</td>
      <td>0.0001</td>
    </tr>
    <tr>
      <th>498</th>
      <td>Zions Bancorporation N.a.</td>
      <td>ZION</td>
      <td>0.01%</td>
      <td>0.0001</td>
    </tr>
    <tr>
      <th>499</th>
      <td>V.F. Corporation</td>
      <td>VFC</td>
      <td>0.01%</td>
      <td>0.0001</td>
    </tr>
    <tr>
      <th>500</th>
      <td>Paramount Global Class B</td>
      <td>PARA</td>
      <td>0.01%</td>
      <td>0.0001</td>
    </tr>
  </tbody>
</table>
<p>500 rows × 4 columns</p>
</div>



4. Reweight now that some of the tickers were removed or clipped


```python
increase_prec = 1 - sap["PortfolioFraction"].sum() + 1
sap["PortfolioFraction"] = sap["PortfolioFraction"] * increase_prec
sap
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Company</th>
      <th>Symbol</th>
      <th>Portfolio%</th>
      <th>PortfolioFraction</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>Microsoft Corp</td>
      <td>MSFT</td>
      <td>7.14%</td>
      <td>0.052395</td>
    </tr>
    <tr>
      <th>1</th>
      <td>Apple Inc.</td>
      <td>AAPL</td>
      <td>6.08%</td>
      <td>0.052395</td>
    </tr>
    <tr>
      <th>2</th>
      <td>Nvidia Corp</td>
      <td>NVDA</td>
      <td>4.70%</td>
      <td>0.049251</td>
    </tr>
    <tr>
      <th>3</th>
      <td>Amazon.com Inc</td>
      <td>AMZN</td>
      <td>3.75%</td>
      <td>0.039296</td>
    </tr>
    <tr>
      <th>4</th>
      <td>Meta Platforms, Inc. Class A</td>
      <td>META</td>
      <td>2.58%</td>
      <td>0.027036</td>
    </tr>
    <tr>
      <th>...</th>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
    </tr>
    <tr>
      <th>496</th>
      <td>Mohawk Industries, Inc.</td>
      <td>MHK</td>
      <td>0.01%</td>
      <td>0.000105</td>
    </tr>
    <tr>
      <th>497</th>
      <td>Whirlpool Corp.</td>
      <td>WHR</td>
      <td>0.01%</td>
      <td>0.000105</td>
    </tr>
    <tr>
      <th>498</th>
      <td>Zions Bancorporation N.a.</td>
      <td>ZION</td>
      <td>0.01%</td>
      <td>0.000105</td>
    </tr>
    <tr>
      <th>499</th>
      <td>V.F. Corporation</td>
      <td>VFC</td>
      <td>0.01%</td>
      <td>0.000105</td>
    </tr>
    <tr>
      <th>500</th>
      <td>Paramount Global Class B</td>
      <td>PARA</td>
      <td>0.01%</td>
      <td>0.000105</td>
    </tr>
  </tbody>
</table>
<p>500 rows × 4 columns</p>
</div>



5) Import a dataframe of your current equity holdings & cash. You're a bit on your own here but my brokerage allows me to download a CSV of all of my holding. I'm going to assume you have a simplified version of that.


```python
#Write out some test data
with open('holdings.csv', 'w') as f:
    f.write("""symbol,shares
AMZN,10
NVDA,1
MSFT,1
YUM,100
ZTS,10
GME,100""")
```


```python
holdings = pd.read_csv('holdings.csv')
holdings
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>symbol</th>
      <th>shares</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>AMZN</td>
      <td>10</td>
    </tr>
    <tr>
      <th>1</th>
      <td>NVDA</td>
      <td>1</td>
    </tr>
    <tr>
      <th>2</th>
      <td>MSFT</td>
      <td>1</td>
    </tr>
    <tr>
      <th>3</th>
      <td>YUM</td>
      <td>100</td>
    </tr>
    <tr>
      <th>4</th>
      <td>ZTS</td>
      <td>10</td>
    </tr>
    <tr>
      <th>5</th>
      <td>GME</td>
      <td>100</td>
    </tr>
  </tbody>
</table>
</div>




```python
cash_to_invest = 10000
```

6) Get last share price of all of holdings and compute our total assests to be invested


```python
import yfinance as yf

def get_current_price(symbol):
    ticker = yf.Ticker(symbol.replace(".","-"))
    todays_data = ticker.history(period='2d')
    return todays_data['Close'].iloc[0]

holdings["last$"] = holdings["symbol"].apply(get_current_price)
holdings["total$"] = holdings["shares"] * holdings["last$"]

total_assets = holdings['total$'].sum() + cash_to_invest
total_assets
print(f"total_assets:", total_assets)
holdings
```

    total_assets: 30298.690364837646





<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>symbol</th>
      <th>shares</th>
      <th>last$</th>
      <th>total$</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>AMZN</td>
      <td>10</td>
      <td>177.580002</td>
      <td>1775.800018</td>
    </tr>
    <tr>
      <th>1</th>
      <td>NVDA</td>
      <td>1</td>
      <td>852.369995</td>
      <td>852.369995</td>
    </tr>
    <tr>
      <th>2</th>
      <td>MSFT</td>
      <td>1</td>
      <td>414.920013</td>
      <td>414.920013</td>
    </tr>
    <tr>
      <th>3</th>
      <td>YUM</td>
      <td>100</td>
      <td>138.550003</td>
      <td>13855.000305</td>
    </tr>
    <tr>
      <th>4</th>
      <td>ZTS</td>
      <td>10</td>
      <td>187.860001</td>
      <td>1878.600006</td>
    </tr>
    <tr>
      <th>5</th>
      <td>GME</td>
      <td>100</td>
      <td>15.220000</td>
      <td>1522.000027</td>
    </tr>
  </tbody>
</table>
</div>




```python

```

7) Also get lastest share price from all S&P 500 symbols


```python
sap["last$"] = sap["Symbol"].apply(get_current_price)
```


```python
sap
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Company</th>
      <th>Symbol</th>
      <th>Portfolio%</th>
      <th>PortfolioFraction</th>
      <th>last$</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>Microsoft Corp</td>
      <td>MSFT</td>
      <td>7.14%</td>
      <td>0.052395</td>
      <td>414.920013</td>
    </tr>
    <tr>
      <th>1</th>
      <td>Apple Inc.</td>
      <td>AAPL</td>
      <td>6.08%</td>
      <td>0.052395</td>
      <td>175.100006</td>
    </tr>
    <tr>
      <th>2</th>
      <td>Nvidia Corp</td>
      <td>NVDA</td>
      <td>4.70%</td>
      <td>0.049251</td>
      <td>852.369995</td>
    </tr>
    <tr>
      <th>3</th>
      <td>Amazon.com Inc</td>
      <td>AMZN</td>
      <td>3.75%</td>
      <td>0.039296</td>
      <td>177.580002</td>
    </tr>
    <tr>
      <th>4</th>
      <td>Meta Platforms, Inc. Class A</td>
      <td>META</td>
      <td>2.58%</td>
      <td>0.027036</td>
      <td>498.190002</td>
    </tr>
    <tr>
      <th>...</th>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
    </tr>
    <tr>
      <th>496</th>
      <td>Mohawk Industries, Inc.</td>
      <td>MHK</td>
      <td>0.01%</td>
      <td>0.000105</td>
      <td>120.830002</td>
    </tr>
    <tr>
      <th>497</th>
      <td>Whirlpool Corp.</td>
      <td>WHR</td>
      <td>0.01%</td>
      <td>0.000105</td>
      <td>106.550003</td>
    </tr>
    <tr>
      <th>498</th>
      <td>Zions Bancorporation N.a.</td>
      <td>ZION</td>
      <td>0.01%</td>
      <td>0.000105</td>
      <td>39.169998</td>
    </tr>
    <tr>
      <th>499</th>
      <td>V.F. Corporation</td>
      <td>VFC</td>
      <td>0.01%</td>
      <td>0.000105</td>
      <td>15.730000</td>
    </tr>
    <tr>
      <th>500</th>
      <td>Paramount Global Class B</td>
      <td>PARA</td>
      <td>0.01%</td>
      <td>0.000105</td>
      <td>10.300000</td>
    </tr>
  </tbody>
</table>
<p>500 rows × 5 columns</p>
</div>



8) Compute shares we should own to match give our current investable assets


```python
sap["$NeededToMatch"] = sap["PortfolioFraction"] * total_assets
sap["SharesToMatch"] = (sap["$NeededToMatch"] / sap["last$"]).astype('int') # assume no factional shares
sap
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Company</th>
      <th>Symbol</th>
      <th>Portfolio%</th>
      <th>PortfolioFraction</th>
      <th>last$</th>
      <th>$NeededToMatch</th>
      <th>SharesToMatch</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>Microsoft Corp</td>
      <td>MSFT</td>
      <td>7.14%</td>
      <td>0.052395</td>
      <td>414.920013</td>
      <td>1587.499882</td>
      <td>3</td>
    </tr>
    <tr>
      <th>1</th>
      <td>Apple Inc.</td>
      <td>AAPL</td>
      <td>6.08%</td>
      <td>0.052395</td>
      <td>175.100006</td>
      <td>1587.499882</td>
      <td>9</td>
    </tr>
    <tr>
      <th>2</th>
      <td>Nvidia Corp</td>
      <td>NVDA</td>
      <td>4.70%</td>
      <td>0.049251</td>
      <td>852.369995</td>
      <td>1492.249889</td>
      <td>1</td>
    </tr>
    <tr>
      <th>3</th>
      <td>Amazon.com Inc</td>
      <td>AMZN</td>
      <td>3.75%</td>
      <td>0.039296</td>
      <td>177.580002</td>
      <td>1190.624911</td>
      <td>6</td>
    </tr>
    <tr>
      <th>4</th>
      <td>Meta Platforms, Inc. Class A</td>
      <td>META</td>
      <td>2.58%</td>
      <td>0.027036</td>
      <td>498.190002</td>
      <td>819.149939</td>
      <td>1</td>
    </tr>
    <tr>
      <th>...</th>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
    </tr>
    <tr>
      <th>496</th>
      <td>Mohawk Industries, Inc.</td>
      <td>MHK</td>
      <td>0.01%</td>
      <td>0.000105</td>
      <td>120.830002</td>
      <td>3.175000</td>
      <td>0</td>
    </tr>
    <tr>
      <th>497</th>
      <td>Whirlpool Corp.</td>
      <td>WHR</td>
      <td>0.01%</td>
      <td>0.000105</td>
      <td>106.550003</td>
      <td>3.175000</td>
      <td>0</td>
    </tr>
    <tr>
      <th>498</th>
      <td>Zions Bancorporation N.a.</td>
      <td>ZION</td>
      <td>0.01%</td>
      <td>0.000105</td>
      <td>39.169998</td>
      <td>3.175000</td>
      <td>0</td>
    </tr>
    <tr>
      <th>499</th>
      <td>V.F. Corporation</td>
      <td>VFC</td>
      <td>0.01%</td>
      <td>0.000105</td>
      <td>15.730000</td>
      <td>3.175000</td>
      <td>0</td>
    </tr>
    <tr>
      <th>500</th>
      <td>Paramount Global Class B</td>
      <td>PARA</td>
      <td>0.01%</td>
      <td>0.000105</td>
      <td>10.300000</td>
      <td>3.175000</td>
      <td>0</td>
    </tr>
  </tbody>
</table>
<p>500 rows × 7 columns</p>
</div>



9) Join to our current holdings


```python
movesWithHoldings = sap.merge(holdings[["symbol", "shares"]], left_on='Symbol', right_on='symbol', how='outer')
movesWithHoldings["shares"] = movesWithHoldings["shares"].fillna(0)
movesWithHoldings["SharesToMatch"] = movesWithHoldings["SharesToMatch"].fillna(0)
movesWithHoldings.loc[movesWithHoldings["Symbol"].isna(),"Symbol"] = movesWithHoldings[movesWithHoldings["Symbol"].isna()]["symbol"]
movesWithHoldings
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Company</th>
      <th>Symbol</th>
      <th>Portfolio%</th>
      <th>PortfolioFraction</th>
      <th>last$</th>
      <th>$NeededToMatch</th>
      <th>SharesToMatch</th>
      <th>symbol</th>
      <th>shares</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>Microsoft Corp</td>
      <td>MSFT</td>
      <td>7.14%</td>
      <td>0.052395</td>
      <td>414.920013</td>
      <td>1587.499882</td>
      <td>3.0</td>
      <td>MSFT</td>
      <td>1.0</td>
    </tr>
    <tr>
      <th>1</th>
      <td>Apple Inc.</td>
      <td>AAPL</td>
      <td>6.08%</td>
      <td>0.052395</td>
      <td>175.100006</td>
      <td>1587.499882</td>
      <td>9.0</td>
      <td>NaN</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>2</th>
      <td>Nvidia Corp</td>
      <td>NVDA</td>
      <td>4.70%</td>
      <td>0.049251</td>
      <td>852.369995</td>
      <td>1492.249889</td>
      <td>1.0</td>
      <td>NVDA</td>
      <td>1.0</td>
    </tr>
    <tr>
      <th>3</th>
      <td>Amazon.com Inc</td>
      <td>AMZN</td>
      <td>3.75%</td>
      <td>0.039296</td>
      <td>177.580002</td>
      <td>1190.624911</td>
      <td>6.0</td>
      <td>AMZN</td>
      <td>10.0</td>
    </tr>
    <tr>
      <th>4</th>
      <td>Meta Platforms, Inc. Class A</td>
      <td>META</td>
      <td>2.58%</td>
      <td>0.027036</td>
      <td>498.190002</td>
      <td>819.149939</td>
      <td>1.0</td>
      <td>NaN</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>...</th>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
    </tr>
    <tr>
      <th>496</th>
      <td>Whirlpool Corp.</td>
      <td>WHR</td>
      <td>0.01%</td>
      <td>0.000105</td>
      <td>106.550003</td>
      <td>3.175000</td>
      <td>0.0</td>
      <td>NaN</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>497</th>
      <td>Zions Bancorporation N.a.</td>
      <td>ZION</td>
      <td>0.01%</td>
      <td>0.000105</td>
      <td>39.169998</td>
      <td>3.175000</td>
      <td>0.0</td>
      <td>NaN</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>498</th>
      <td>V.F. Corporation</td>
      <td>VFC</td>
      <td>0.01%</td>
      <td>0.000105</td>
      <td>15.730000</td>
      <td>3.175000</td>
      <td>0.0</td>
      <td>NaN</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>499</th>
      <td>Paramount Global Class B</td>
      <td>PARA</td>
      <td>0.01%</td>
      <td>0.000105</td>
      <td>10.300000</td>
      <td>3.175000</td>
      <td>0.0</td>
      <td>NaN</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>500</th>
      <td>NaN</td>
      <td>GME</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>0.0</td>
      <td>GME</td>
      <td>100.0</td>
    </tr>
  </tbody>
</table>
<p>501 rows × 9 columns</p>
</div>



11) Find the shares we need to match the S&P 500 prop


```python
movesWithHoldings["SharesDifference"] = movesWithHoldings["SharesToMatch"] - movesWithHoldings["shares"]
movesWithHoldings
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Company</th>
      <th>Symbol</th>
      <th>Portfolio%</th>
      <th>PortfolioFraction</th>
      <th>last$</th>
      <th>$NeededToMatch</th>
      <th>SharesToMatch</th>
      <th>symbol</th>
      <th>shares</th>
      <th>SharesDifference</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>Microsoft Corp</td>
      <td>MSFT</td>
      <td>7.14%</td>
      <td>0.052395</td>
      <td>414.920013</td>
      <td>1587.499882</td>
      <td>3.0</td>
      <td>MSFT</td>
      <td>1.0</td>
      <td>2.0</td>
    </tr>
    <tr>
      <th>1</th>
      <td>Apple Inc.</td>
      <td>AAPL</td>
      <td>6.08%</td>
      <td>0.052395</td>
      <td>175.100006</td>
      <td>1587.499882</td>
      <td>9.0</td>
      <td>NaN</td>
      <td>0.0</td>
      <td>9.0</td>
    </tr>
    <tr>
      <th>2</th>
      <td>Nvidia Corp</td>
      <td>NVDA</td>
      <td>4.70%</td>
      <td>0.049251</td>
      <td>852.369995</td>
      <td>1492.249889</td>
      <td>1.0</td>
      <td>NVDA</td>
      <td>1.0</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>3</th>
      <td>Amazon.com Inc</td>
      <td>AMZN</td>
      <td>3.75%</td>
      <td>0.039296</td>
      <td>177.580002</td>
      <td>1190.624911</td>
      <td>6.0</td>
      <td>AMZN</td>
      <td>10.0</td>
      <td>-4.0</td>
    </tr>
    <tr>
      <th>4</th>
      <td>Meta Platforms, Inc. Class A</td>
      <td>META</td>
      <td>2.58%</td>
      <td>0.027036</td>
      <td>498.190002</td>
      <td>819.149939</td>
      <td>1.0</td>
      <td>NaN</td>
      <td>0.0</td>
      <td>1.0</td>
    </tr>
    <tr>
      <th>...</th>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
    </tr>
    <tr>
      <th>496</th>
      <td>Whirlpool Corp.</td>
      <td>WHR</td>
      <td>0.01%</td>
      <td>0.000105</td>
      <td>106.550003</td>
      <td>3.175000</td>
      <td>0.0</td>
      <td>NaN</td>
      <td>0.0</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>497</th>
      <td>Zions Bancorporation N.a.</td>
      <td>ZION</td>
      <td>0.01%</td>
      <td>0.000105</td>
      <td>39.169998</td>
      <td>3.175000</td>
      <td>0.0</td>
      <td>NaN</td>
      <td>0.0</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>498</th>
      <td>V.F. Corporation</td>
      <td>VFC</td>
      <td>0.01%</td>
      <td>0.000105</td>
      <td>15.730000</td>
      <td>3.175000</td>
      <td>0.0</td>
      <td>NaN</td>
      <td>0.0</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>499</th>
      <td>Paramount Global Class B</td>
      <td>PARA</td>
      <td>0.01%</td>
      <td>0.000105</td>
      <td>10.300000</td>
      <td>3.175000</td>
      <td>0.0</td>
      <td>NaN</td>
      <td>0.0</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>500</th>
      <td>NaN</td>
      <td>GME</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>0.0</td>
      <td>GME</td>
      <td>100.0</td>
      <td>-100.0</td>
    </tr>
  </tbody>
</table>
<p>501 rows × 10 columns</p>
</div>



12) Find tickers that we need to sell


```python
movesWithHoldings[movesWithHoldings["SharesDifference"] < 0][["Symbol", "SharesDifference"]] 
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Symbol</th>
      <th>SharesDifference</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>3</th>
      <td>AMZN</td>
      <td>-4.0</td>
    </tr>
    <tr>
      <th>104</th>
      <td>ZTS</td>
      <td>-10.0</td>
    </tr>
    <tr>
      <th>220</th>
      <td>YUM</td>
      <td>-100.0</td>
    </tr>
    <tr>
      <th>500</th>
      <td>GME</td>
      <td>-100.0</td>
    </tr>
  </tbody>
</table>
</div>



13) Find tickers that we need to buy


```python
movesWithHoldings[movesWithHoldings["SharesDifference"] > 0][["Symbol", "SharesDifference"]]
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Symbol</th>
      <th>SharesDifference</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>MSFT</td>
      <td>2.0</td>
    </tr>
    <tr>
      <th>1</th>
      <td>AAPL</td>
      <td>9.0</td>
    </tr>
    <tr>
      <th>4</th>
      <td>META</td>
      <td>1.0</td>
    </tr>
    <tr>
      <th>5</th>
      <td>GOOGL</td>
      <td>4.0</td>
    </tr>
    <tr>
      <th>6</th>
      <td>BRK.B</td>
      <td>1.0</td>
    </tr>
    <tr>
      <th>7</th>
      <td>GOOG</td>
      <td>3.0</td>
    </tr>
    <tr>
      <th>10</th>
      <td>JPM</td>
      <td>2.0</td>
    </tr>
    <tr>
      <th>12</th>
      <td>V</td>
      <td>1.0</td>
    </tr>
    <tr>
      <th>13</th>
      <td>XOM</td>
      <td>2.0</td>
    </tr>
    <tr>
      <th>15</th>
      <td>JNJ</td>
      <td>1.0</td>
    </tr>
    <tr>
      <th>17</th>
      <td>PG</td>
      <td>1.0</td>
    </tr>
    <tr>
      <th>19</th>
      <td>AMD</td>
      <td>1.0</td>
    </tr>
    <tr>
      <th>20</th>
      <td>MRK</td>
      <td>1.0</td>
    </tr>
    <tr>
      <th>21</th>
      <td>ABBV</td>
      <td>1.0</td>
    </tr>
    <tr>
      <th>24</th>
      <td>CVX</td>
      <td>1.0</td>
    </tr>
    <tr>
      <th>26</th>
      <td>WMT</td>
      <td>3.0</td>
    </tr>
    <tr>
      <th>28</th>
      <td>BAC</td>
      <td>4.0</td>
    </tr>
    <tr>
      <th>29</th>
      <td>KO</td>
      <td>2.0</td>
    </tr>
    <tr>
      <th>34</th>
      <td>ABT</td>
      <td>1.0</td>
    </tr>
    <tr>
      <th>35</th>
      <td>DIS</td>
      <td>1.0</td>
    </tr>
    <tr>
      <th>36</th>
      <td>WFC</td>
      <td>2.0</td>
    </tr>
    <tr>
      <th>37</th>
      <td>CSCO</td>
      <td>2.0</td>
    </tr>
    <tr>
      <th>39</th>
      <td>INTC</td>
      <td>2.0</td>
    </tr>
    <tr>
      <th>41</th>
      <td>ORCL</td>
      <td>1.0</td>
    </tr>
    <tr>
      <th>44</th>
      <td>CMCSA</td>
      <td>3.0</td>
    </tr>
    <tr>
      <th>47</th>
      <td>VZ</td>
      <td>3.0</td>
    </tr>
    <tr>
      <th>49</th>
      <td>UBER</td>
      <td>1.0</td>
    </tr>
    <tr>
      <th>53</th>
      <td>PFE</td>
      <td>4.0</td>
    </tr>
    <tr>
      <th>57</th>
      <td>PM</td>
      <td>1.0</td>
    </tr>
    <tr>
      <th>62</th>
      <td>RTX</td>
      <td>1.0</td>
    </tr>
    <tr>
      <th>68</th>
      <td>T</td>
      <td>5.0</td>
    </tr>
    <tr>
      <th>74</th>
      <td>NEE</td>
      <td>1.0</td>
    </tr>
    <tr>
      <th>82</th>
      <td>C</td>
      <td>1.0</td>
    </tr>
    <tr>
      <th>85</th>
      <td>BMY</td>
      <td>1.0</td>
    </tr>
    <tr>
      <th>89</th>
      <td>SCHW</td>
      <td>1.0</td>
    </tr>
    <tr>
      <th>91</th>
      <td>MDLZ</td>
      <td>1.0</td>
    </tr>
    <tr>
      <th>92</th>
      <td>BSX</td>
      <td>1.0</td>
    </tr>
    <tr>
      <th>111</th>
      <td>CSX</td>
      <td>1.0</td>
    </tr>
    <tr>
      <th>116</th>
      <td>MO</td>
      <td>1.0</td>
    </tr>
    <tr>
      <th>120</th>
      <td>SLB</td>
      <td>1.0</td>
    </tr>
    <tr>
      <th>133</th>
      <td>USB</td>
      <td>1.0</td>
    </tr>
    <tr>
      <th>150</th>
      <td>GM</td>
      <td>1.0</td>
    </tr>
    <tr>
      <th>153</th>
      <td>FCX</td>
      <td>1.0</td>
    </tr>
    <tr>
      <th>169</th>
      <td>F</td>
      <td>2.0</td>
    </tr>
    <tr>
      <th>238</th>
      <td>PCG</td>
      <td>1.0</td>
    </tr>
    <tr>
      <th>242</th>
      <td>KMI</td>
      <td>1.0</td>
    </tr>
    <tr>
      <th>249</th>
      <td>KVUE</td>
      <td>1.0</td>
    </tr>
    <tr>
      <th>345</th>
      <td>WBD</td>
      <td>1.0</td>
    </tr>
    <tr>
      <th>400</th>
      <td>VTRS</td>
      <td>1.0</td>
    </tr>
    <tr>
      <th>423</th>
      <td>AMCR</td>
      <td>1.0</td>
    </tr>
  </tbody>
</table>
</div>




```python

```


```python

```
