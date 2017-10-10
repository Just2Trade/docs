# Accounts

## Get accounts balances

```shell
curl
    -X GET
    'https://api.just2trade.com/accounts'
```

> Response example

```json
[
    {
        "account_number": "12345678@vision",
        "trade_platform": "ETNA",
        "margin_type": "marginx2",
        "restriction": "none",
        "daytrades_count": 0,
        "account_value_total": 9880.6806,
        "cash": 5283.44,
        "day_trading_buying_power": 0,
        "margin_buying_power": 15243,
        "non_margin_buying_power": 7621.5,
        "position_market_value": 4597.2406,
        "unsettled_cash": 0
    },
    {
        "account_number": "11111111@cor",
        "trade_platform": "ETNA",
        "margin_type": "daytrader",
        "restriction": "restricted",
        "restriction_reason": "please fund the account",
        "daytrades_count": 0,
        "account_value_total": 0,
        "cash": 0,
        "day_trading_buying_power": 0,
        "margin_buying_power": 0,
        "non_margin_buying_power": 0,
        "position_market_value": 0,
        "unsettled_cash": 0
    }
]
```

Get balance information for all user accounts as an array of the following json structures:

name | description
---- | ----
account_number | the account number
trade_platform | the trading platform this account is traded on
margin_type | the margin type, possible values are `cash`, `marginx1`, `marginx2`, `daytrader`
restriction | restriction level effective on the account, possible values are `none` - no restrictions, `restricted` - opening transactions are not allowed but closing orders are accepted, `disabled` - no activity is allowed in the account, `closed` - the account is closed
restriction_reason | optional description explaining why the account is restricted
daytrades_count | day trades counter
account_value_total | total account liquidation value
cash | account debit balance when negative, credit balance when positive
day_trading_buying_power | day trading buying power for marginable securities
margin_buying_power | the buying power for marginable securities
non_margin_buying_power | the buying power for non-marginable securities
position_market_value | sum of all positions current market values. The value is negative for short positions
unsettled_cash | unsettled cash for cash accounts

## Get account positions

```shell
curl
    -X GET
    'https://api.just2trade.com/accounts/12345678@vision/positions'
```

```json
[
    {
        "symbol": "KEYS",
        "quantity": 90,
        "average_open_price": 26.061,
        "current_price": 42.17,
        "lot_size": 1,
        "security_type": "common_stock"
    },
    {
        "symbol": "GRNH",
        "quantity": 1,
        "average_open_price": 0.0975,
        "current_price": 0.040600000000000004,
        "lot_size": 1,
        "security_type": "common_stock"
    },
    {
        "symbol": "TWTR",
        "quantity": 13,
        "average_open_price": 17.74043,
        "current_price": 17.38,
        "lot_size": 1,
        "security_type": "common_stock"
    },
    {
        "symbol": "RAD",
        "quantity": 1,
        "average_open_price": 2.37,
        "current_price": 1.87,
        "lot_size": 1,
        "security_type": "common_stock"
    }
]
```

Get specified account positions as an array of the following json structures:

name | description
---- | ----
symbol | security symbol
quantity | number of shares or option contracts
average_open_price | average historical cost basis
current_price | current price
lot_size | this has the value of 1 for all stocks and usually 100 for all options
security_type | the type of security, the most common values are `common_stock`, `preferred_stock`, `option`