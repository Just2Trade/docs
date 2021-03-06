# Trading


## Validate an order

```shell
curl -X POST
  --header 'Content-Type: application/json'
  --header 'Accept: application/json'
  --header 'Authorization: Bearer {token here}'
  -d '{ \ 
   "account_number": "12345678@vision", \ 
   "symbol": "string", \ 
   "quantity": 1, \ 
   "price": 21.0, \ 
   "stop_price": 0, \ 
   "time_in_force": "day", \ 
   "order_type": "limit", \ 
   "side": "buy", \ 
   "comment": "string", \ 
   "exchange": "string" \ 
 }' 'https://api.just2trade.com/orders/validate'
```

```cpp--mql
void ValidateOrder(string access_token, string account_number)
{
  //--- REST client's HTTP vars
  string uri = "https://api.just2trade.com/orders/validate";
  char post[];
  char result[];
  string headersResult;
  string header;
  int res;
   
  //--- Form the request body 
  header = "Content-Type: application/json\r\n"; 
  header += "Accept: application/json\r\n";     
  header += "Authorization: Bearer "+access_token+"\r\n";
  string postData = "{ \"account_number\": \"" + account_number + "\", \"symbol\": \"ZVZZT\", \"quantity\": 50, \"price\": 10.00, \"order_type\": \"limit\", \"side\": \"buy\" }";
  
  ArrayResize(post, StringToCharArray(postData, post, 0, WHOLE_ARRAY, CP_UTF8) - 1);

  //--- reset last error
  ResetLastError();

  //--- post data to REST API
  res = WebRequest("POST", uri, header, 50, post, result, headersResult);
   
  //--- check errors
  if (res == -1)
  {
    Print("Error code =", GetLastError());
  }
  else
  {
    //--- successful
    Print("Server response: ", CharArrayToString(result, 0, -1));
  }          
}
```

> Request example

```json
{
  "account_number": "12345678@vision",
  "symbol": "BAC",
  "quantity": 1,
  "price": 21.0,
  "stop_price": 0,
  "time_in_force": "day",
  "order_type": "limit",
  "side": "buy",
  "comment": "any comment",
  "exchange": "Auto"
}
```

> Response example

```json
{
  "is_valid": false,
  "validation_message": "You cannot sell more shares than your effective long position (effective long position = long position less open sell orders) using one order ticket. Please check your pending order(s) for potential outstanding duplicate order(s). Industry regulations require that two order tickets be submitted: one to sell your effective long position and one to sell your desired short position."
}
```

The method verifies an order and responds with the validation message if the order can not be placed at the moment. The order is not sent to market.

### Request
parameter | description
---- | ----
account_number | Required. The account number in the format of `123456578@vision`
symbol | Required. The security symbol, stocks in Nasdaq CMS convention, options in OCC
quantity | Required. The positive decimal, number of shares or contracts
price | Required positive decimal if the `order_type` is `limit` or `stop_limit`
stop_price | Required positive decimal if the `order_type` is `stop` or `stop_limit`
time_in_force | Optional, `day` by default. Specifies how long the order remains in effect. Possible values are `day`, `good_till_cancel`, `good_till_crossing`
order_type | Optional, `market` by default. Possible values are `market`, `limit`, `stop`, `stop_limit`, `market_on_close`, `limit_on_close`
side | Optional, `buy` by default. Available values are `buy` and `sell`. `buy` opens long position, `sell` closes the position. The system will detemine the proper side according to the current position, but you are still required to place two orders to revert the position from long to short and the other way around.
comment | Optional, any string
exchange | Optional, `auto` by default. The routing instructions for order execution. The actual values are dynamic and depend on the account settings. Some of the possible values are `NASDAQ` or `ARCA`

### Response

The method returns the following json structure:

parameter | description
---- | ----
is_valid | `true` or `false`
validation_message | Optional reject reason


## Place an order
```shell
curl -X POST
  --header 'Content-Type: application/json'
  --header 'Accept: application/json'
  --header 'Authorization: Bearer {token here}'
  -d '{ \ 
   "account_number": "12345678@vision", \ 
   "symbol": "BAC", \ 
   "quantity": 1, \ 
   "price": 20, \ 
   "stop_price": 0, \ 
   "time_in_force": "day", \ 
   "order_type": "limit", \ 
   "side": "buy", \ 
   "comment": "any comment", \ 
   "exchange": "Auto" \ 
 }' 'https://api.just2trade.com/orders/place'
```

```cpp--mql
void PlaceOrder(string access_token, string account_number)
{
  //--- REST client's HTTP vars
  string uri = "https://api.just2trade.com/orders/place";
  char post[];
  char result[];
  string headersResult;
  string header;
  int res;
   
  //--- Form the request body 
  header = "Content-Type: application/json\r\n"; 
  header += "Accept: application/json\r\n";     
  header += "Authorization: Bearer "+access_token+"\r\n";
  string postData = "{ \"account_number\": \"" + account_number + "\", \"symbol\": \"ZVZZT\", \"quantity\": 50, \"price\": 10.00, \"order_type\": \"limit\", \"side\": \"buy\" }";
  
  ArrayResize(post, StringToCharArray(postData, post, 0, WHOLE_ARRAY, CP_UTF8) - 1);

  //--- reset last error
  ResetLastError();

  //--- post data to REST API
  res = WebRequest("POST", uri, header, 50, post, result, headersResult);
   
  //--- check errors
  if (res == -1)
  {
    Print("Error code =", GetLastError());
  }
  else
  {
    //--- successful
    Print("Server response: ", CharArrayToString(result, 0, -1));
  }          
}
```


> Request example

```json
{
  "account_number": "12345678@vision",
  "symbol": "BAC",
  "quantity": 1,
  "price": 20.0,
  "stop_price": 0,
  "time_in_force": "day",
  "order_type": "limit",
  "side": "buy",
  "comment": "any comment",
  "exchange": "Auto"
}
```

> Response example

```json
{
  "success": true,
  "data": "201710041710516537"
}
```

The order is accepted immediately, the method returns assigned id. The order is still validated with exactly same logic as in [validate] (#validate-an-order) method and is sent to market on successful validation pass. Otherwise, the order will reject asynchronously and you can query its status by calling the [details] (#get-order-details) method.

### Response

name | value
---- | ----
success | true
data | the id assigned to the order


## Get order details
```shell
curl -X GET
  --header 'Accept: application/json'
  --header 'Authorization: Bearer {token here}'
  'https://api.just2trade.com/orders/{id}'
```

```cpp--mql
string GetOrderDetails(string access_token, string id)
{
  //--- REST client's HTTP vars
  string header;
  char result[];
  char post[];
  string headersResult;
  int res;
  string uri = "https://api.just2trade.com/orders/" + id;
     
  //--- Form the request body 
  header += "Accept: application/json\r\n";     
  header += "Authorization: Bearer " + access_token + "\r\n";
          
  //--- post data to REST API
  res = WebRequest("GET", uri, header, 5000, post, result, headersResult);
     
  if(res == -1)
  {
    Print("Error code = ", GetLastError());
  }
  else
  {
    Print("Successful, server response: " + CharArrayToString(result, 0, -1) + " Res: " + (string)res);
  }  
  return CharArrayToString(result, 0, -1);
}
```
> Response example

```json
{
  "account_number": "12345678@vision",
  "client_id": "20171003209384646",
  "exchange": "Auto",
  "quantity": 1,
  "executed_quantity": 0,
  "order_status": "new",
  "price": 20,
  "stop_price": 0,
  "time_in_force": "day",
  "order_type": "limit",
  "order_side": "buy",
  "symbol": "BAC"
}
```

Get the order details by the specified order id

### Request
name | description
---- | ----
id | Required. The order id

### Response

name | description
---- | ----
account_number | the account number in the format of `123456578@vision`
client_id | the order id
exchange | the routing instructions
quantity | number of shares or contracts requested by the order
executed_quantity | number of shares or contracts executed by this time
order_status | the order status. Possible values are `new`, `pending_new`, `partially_filled`, `filled`, `pending_cancel`, `canceled`, `pending_replace`, `replaced`, `rejected`, `done_for_day`, `expired`
price | limit price if applicable
stop_price | stop price is applicable
time_in_force | order duration instructions
order_type | type of the order
order_side | side of the order
symbol | security symbol


## Get active orders

```shell
curl -X GET
  --header 'Accept: application/json'
  --header 'Authorization: Bearer {token here}'
  'https://api.just2trade.com/accounts/{account_number}/activeorders'
```

> Response example

```json
[
  {
    "account_number": "12345678@vision",
    "client_id": "20171003209384646",
    "exchange": "Auto",
    "quantity": 1,
    "executed_quantity": 0,
    "order_status": "new",
    "price": 20,
    "stop_price": 0,
    "time_in_force": "day",
    "order_type": "limit",
    "order_side": "buy",
    "symbol": "BAC"
  }
]
```

Get an array of active orders


## Cancel an order
```shell
curl -X POST
  --header 'Content-Type: application/json'
  --header 'Accept: application/json'
  --header 'Authorization: Bearer {token here}'
  -d '{ \ 
   "message": "string" \ 
 }' 'https://api.just2trade.com/orders/20171003209384646/cancel'
```

> Response example

```json
{
  "success": true,
  "data": "201710041710516537"
}
```

Cancel the specified order

### Request

the order is identified by its id on the url. The request can also contain optional info

name | description
---- | ----
message | Optional, any string


### Response

name | value
---- | ----
success | true
data | the cancellation request id


## *Estimate fee charges - not live yet*

```shell
curl -X POST
  --header 'Content-Type: application/json'
  --header 'Accept: application/json'
  --header 'Authorization: Bearer {token here}'
  -d '{ \ 
   "commission_plan": "per_trade", \ 
   "symbol": "TSLA", \ 
   "quantity": 50, \ 
   "side": "sell", \ 
   "direct_route": true \ 
 }' 'https://api.just2trade.com/pricing/fees'
```

> Response example

```json
[
  {
    "amount": "2.5000",
    "type": "Broker commission"
  },
  {
    "amount": "0.1500",
    "type": "ECN"
  },
  {
    "amount": "0.1827",
    "type": "SEC"
  },
  {
    "amount": "0.0060",
    "type": "TAF"
  }
]
```

The method returns estimated fees for specified order parameters, breaking down all charges by type

### Request

name | description
---- | ----
commission_plan | Optional. Available values are `per_share`, `per_trade`, `zero`. Default is `per_trade`
symbol | Required. A stock or an option
quantity | Required. Order quantity
side | Required. Available values are `buy`, `sell`, `sell_short`, `buy_to_cover`
direct_route | Optional boolean value. `false` by default.

### Response

An array of items breaking down all charges

name | description
---- | ----
amount | Dollar amount
type | Charge description
