
<!-- README.md is generated from README.Rmd. Please edit that file -->
<!-- badges: start -->

[![Project Status: Active – The project has reached a stable, usable
state and is being actively
developed.](https://www.repostatus.org/badges/latest/active.svg)](https://www.repostatus.org/#active)
[![Codecov test
coverage](https://codecov.io/gh/msperlin/yfR/branch/main/graph/badge.svg)](https://app.codecov.io/gh/msperlin/yfR?branch=main)
[![R build
(rcmdcheck)](https://github.com/msperlin/yfR/workflows/R-CMD-check/badge.svg)](https://github.com/msperlin/yfR/actions)
[![Status at rOpenSci Software Peer
Review](https://badges.ropensci.org/523_status.svg)](https://github.com/ropensci/software-review/issues/523)
<!-- badges: end -->

# Motivation

`yfR` facilitates importing data from Yahoo finance by organizing the
data in the `tidy` format and speeding up the process using a cache
system and parallel computing. `yfR` is the second and
backwards-incompatible version of
[BatchGetSymbols](https://CRAN.R-project.org/package=BatchGetSymbols),
released in 2016 (see vignette “yfR and BatchGetSymbols”).

In a nutshell, [Yahoo Finance](https://finance.yahoo.com/) provides a
vast repository of stock price data around the globe. It cover a
signficant number of markets and assets, being used extensively in
academic research and teaching. In order to import the financial data,
all you need is a ticker (id of a stock, e.g. “FB” for [Meta
platforms](https://finance.yahoo.com/quote/FB?p=FB&.tsrc=fin-srch)) and
a time period.

# Finding tickers

The easiest way to find the tickers of a company stock is to search for
it in [Yahoo Finance’s](https://finance.yahoo.com/) website. At the top
page you’ll find a search bar.

![YF Search](search-yf.png?raw=true "Example of search in YF") From
there, you’ll that a company can have many different stocks traded at
different markets. As the example shows, Petrobras is traded at NYQ (New
York Exchange), SAO (Sao Paulo/Brazil - B3 exchange) and BUE (Buenos
Aires/Argentina Exchange), all with different symbols (tickers).

## Features

-   Fetchs daily/weekly/monthly/annual stock prices/returns from yahoo
    finance and outputs a dataframe (tibble) in the long format (stacked
    data);

-   A new feature called “collections” facilitates download of multiple
    tickers from a particular market/index. You can, for example,
    download data for all stocks in the SP500 index with a simple call
    to `yf_collection_get()`;

-   A session-persistent smart cache system is available by default.
    This means that the data is saved locally and only missing portions
    are downloaded, if needed.

-   All dates are compared to a benchmark ticker such as SP500 and,
    whenever an individual asset does not have a sufficient number of
    dates, the software drops it from the output. This means you can
    choose to ignore tickers with high number of missing dates.

-   A customized function called `yf_convert_to_wide()` can transform
    the long dataframe into a wide format (tickers as columns), much
    used in portfolio optimization. The output is a list where each
    element is a different target variable (prices, returns, volumes).

-   Parallel computing with package `furrr` is available, speeding up
    the data importation process.

## Warnings

-   Yahoo finance data is far from perfect or reliable, specially for
    individual stocks. In my experience, using it for research code with
    stock **indices** is fine and I can match it with other data
    sources. But, adjusted stock prices for **individual assets** is
    messy as stock events such as splits or dividends are not properly
    registered. I was never able to match it with other data sources,
    specially for long time periods with lots of corporate events. My
    advice is to **never use the yahoo finance data of individual stocks
    in production** (research papers or academic documents – thesis and
    dissertations). If adjusted price data of individual stocks is
    important for your research, **use other data sources** such as
    [EOD](https://eodhistoricaldata.com/), [SimFin](https://simfin.com/)
    or [Economática](https://economatica.com/).

## Installation

    # CRAN (not yet available)
    #install.packages('yfR')

    # Github (dev version)
    devtools::install_github('msperlin/yfR')

## A simple example of usage

``` r
library(yfR)

# set options for algorithm
my_ticker <- 'FB'
first_date <- Sys.Date() - 30
last_date <- Sys.Date()

# fetch data
df_yf <- yf_get(tickers = my_ticker, 
                     first_date = first_date,
                     last_date = last_date)
#> 
#> ── Running yfR for 1 stocks | 2022-05-01 --> 2022-05-31 (30 days) ──
#> 
#> ℹ Downloading data for benchmark ticker ^GSPC
#> ℹ (1/1) Fetching data for FB
#> !    - not cached
#> ✔    - cache saved successfully
#> ✔    - got 20 valid rows (2022-05-02 --> 2022-05-27)
#> ✔    - got 100% of valid prices -- Time for some tea?
#> ℹ Binding price data

# output is a tibble with data
head(df_yf)
#> # A tibble: 6 × 11
#>   ticker ref_date   price_open price_high price_low price_close   volume
#>   <chr>  <date>          <dbl>      <dbl>     <dbl>       <dbl>    <dbl>
#> 1 FB     2022-05-02       201.       212.      201.        211. 49915300
#> 2 FB     2022-05-03       210.       215.      208.        212. 41556300
#> 3 FB     2022-05-04       211.       224.      207.        223. 41375900
#> 4 FB     2022-05-05       219.       220.      206.        208. 41129200
#> 5 FB     2022-05-06       207.       209.      201.        204. 34733600
#> 6 FB     2022-05-09       200.       203.      196.        196. 36303200
#> # … with 4 more variables: price_adjusted <dbl>, ret_adjusted_prices <dbl>,
#> #   ret_closing_prices <dbl>, cumret_adjusted_prices <dbl>
```

# Acknowledgements

Package `yfR` is based on [quantmod](https://www.quantmod.com/)
(@joshuaulrich) and uses one of its functions (`quantmod::getSymbols`)
for fetching data from Yahoo Finance. I’m thankfull for the author for
his work in keeping the package working properly.
