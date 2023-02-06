---
title: Locates ReST API Specification


toc_footers:
  - [Powered By Slate](https://github.com/slatedocs/slate)

search: true
---

# Introduction
The Clear Street Locates service enables users to request Short Stock Locates via a ReST API. This specification documents the service endpoints and the interaction pattern for this API.

# API Endpoints

## Authentication
```
POST - /auth
```
`POST - /auth`

Users are authenticated using OAuth. For API credentials and additional information, contact us at api-support@clearstreet.io.

### Request Object
```
// Auth Request
{
    "api_key": "abcdefghijklmnopqrstuvwxyz123456"
}
```

`api_key` - API key issued by Clear Street.

### Response Object
```
// Auth Response
{
    "access_token": "xxxxx.yyyyy.zzzzz",
    "expires_in": 600,
    "token_type": "Bearer"
}
```

`access_token` - JSON web token.

`expires_in` - Token expiration time in seconds.

`token_type` - Type of token, e.g. `Bearer`.

Once authenticated, the `access_token` must be passed as a `Bearer` token in the HTTP `Authorization` header for subsequent API requests.

## New Short Stock Locate Request
```
POST - /locates
```
`POST - /locates`

1. Client sends a new Locate request. A single request can contain one or more orders, each order for a single `security_id` and `requested_quantity`.

2. Clear Street responds with a list of `PENDING` orders.  Each order is assigned a unique `clst_reference_id`.  Orders are processed in-order, asynchonously.

### Request Object
```
// Locate Order Request
{
    "mpid": "CPST",
    "trader_account": "100000",
    "reference_id": "a2022122300001",
    "trader_comment": "A request to locate 1000 TSLA shares",
    "orders": [
        {
            "security_id_type": "TICKER",
            "security_id": "TSLA",
            "requested_quantity": 1000,
        }
    ]
}
```

`mpid` - The client Market Participant Identifier, assigned by Clear Street.

`trader_account` - Optional client account ID assigned by Clear Street.

`reference_id` - Client-provided unique reference id for the locate request. This is assigned to all orders in the locate request.

`trader_comment` - Comments provided by the trader.

`orders` - List of locate order objects.

`security_id_type` - Type of the security to be located. Valid values for this field are: TICKER.

`security_id` - Security identifier corresponding to the `security_id_type` for the order.

`requested_quantity` - Quantity requested for the locate.

### Response Object
```
// Locate Order Response
{
    "mpid": "CPST",
    "reference_id": "a2022122300001",
    "trader_account": "100000",
    "request_time": "2022-12-23T13:00:00.000+00:00",
    "order_status": [
        {
            "clst_reference_id": "2af0305ffa5b4c91ba4e7ab45e2d8e4e", 
            "security_id_type": "TICKER", 
            "security_id": "TSLA",
            "requested_quantity": 1000,
            "status": "PENDING", 
        }
    ]
}
```

`mpid` - Market Participant Identifier, assigned by Clear Street.

`reference_id` - Client-provided unique reference id for the locate request.

`trader_account` - Optional client account ID assigned by Clear Street.

`request_time` - Time the locate request was received from the client.

`order_status` - Repeated order status for each security in short stock locate request.

`clst_reference_id` - A unique order reference id assigned by Clear Street.

`requested_quantity` - Quantity requested for the locate.

`security_id_type` - Type of the security to be located. Valid values for this field are: TICKER.

`security_id` - Security identifier corresponding to the `security_id_type` for the order

`status` - Status of the locate. Valid values for this field are: PENDING, OFFERED, FILLED, REJECTED, DECLINED, EXPIRED.

## Get Locate Order

`GET - /locates/:clst_reference_id`

1. Client sends a request using the unique order `clst_reference_id`

2. Clear Street responds with current state of the locate order. If the order has been `OFFERED`, a `locate_id` is assigned and `located_quantity` populated with the number of shares that were located.

### Request Parameters
```
GET - /locates/2af0305ffa5b4c91ba4e7ab45e2d8e4e
```

`clst_reference_id` - Unique order reference id from Clear Street assigned by Clear Street.

### Response Object
```
// Locate Order Status Response
{
    "borrow_rate": -40,
    "clst_reference_id": "2af0305ffa5b4c91ba4e7ab45e2d8e4e", 
    "cost_per_share": 0.01,
    "currency": "USD",
    "desk_comment": "Offer for locate of 1000 TSLA shares",
    "locate_id": "19dd51b8a64d953955c5c202",
    "located_quantity": 1000,
    "located_time": "2022-12-23T13:05:00.000+00:00",
    "mpid": "CPST",
    "reference_id": "a2022122300001",
    "request_time": "2022-12-23T13:00:00.000+00:00",
    "requested_quantity": 1000,
    "trader_account": "100000",
    "trader_comment": "A request for locate of 1000 TSLA shares",
    "security_id": “TSLA”,
    "security_id_type": "TICKER",
    "status": "OFFERED",
    "total_cost": 10.00
}
```
`borrow_rate` - Borrow rate for the security if held overnight.

`clst_reference_id` - A unique order reference id provided by Clear Street.

`cost_per_share` - Cost per share for the locate.

`currency` - Currency for the cost.

`desk_comment` - Comments provided by the Clear Street Stock Loan Desk.

`locate_id` - A unique locate id for the order, available once the order is OFFERED.

`located_quantity` - Quantity offered by Clear Street.

`located_time` - Time security was located.

`mpid` - Market Participant Identifier, assigned by Clear Street.

`reference_id` - Client-provided unique reference id for the locate request.

`request_time` - Time the locate request was received from the client.

`requested_quantity` - Quantity requested for the locate.

`security_id_type` - Type of the security to be located. Valid values for this field are: TICKER.

`security_id` - Security identifier corresponding to the `security_id_type` for the order

`total_cost` - Total cost for the locate.

`trader_account` - Optional client account ID assigned by Clear Street.

`trader_comment` - Comments provided by the trader.

`status` - Status of the locate. Valid values for this field are: PENDING, OFFERED, FILLED, REJECTED, DECLINED, EXPIRED.

## Locate Order Status Update Request
```
PATCH - /locates/19dd51b8a64d953955c5c202/ACCEPT
PATCH - /locates/19dd51b8a64d953955c5c202/DECLINE
```
`PATCH - /locates/:clst_reference_id/:status`

1. Client requests to `ACCEPT`/`DECLINE` a locate offer identified by its unique `clst_reference_id`

2. If successful, Clear Street responds with a 200 status code. If the operation fails, Clear Street response with a 400 status code and an error message.

## Bulk Locate Order Status Update Request
```
PATCH - /locates
```
`PATCH - /locates`

1. Client requests to `ACCEPT`/`DECLINE` multiple locate offers, each identified by its unique `clst_reference_id`.

2. Clear Street responds with a list of locate orders, each reflecting the current state of the order after the `ACCEPT`/`DECLINE` has been processed.

### Request Object
```
// Bulk Locate Order Status Update Request
{
    "locates": [
        {
            "locate_id": "19dd51b8a64d953955c5c202",
            "status": "ACCEPT"
        }
    ],
}
```

`locates` - Repeated `OFFERED` locate orders to be accepted or declined.

`locate_id` - Unique order clst_reference_id provided by Clear Street.

`status` - Action to apply to this order. Valid values are: ACCEPT, DECLINE.

### Response Object
```
// Bulk Locate Order Status Update Response
{
    "locates": [
        {
            "borrow_rate": 20,
            "cost_per_share": 0.010,
            "currency": "USD",
            "desk_comment": "Offer filled for locate of 1000 TSLA shares",
            "locate_id": "19dd51b8a64d953955c5c202",
            "located_quantity": 1000,
            "located_time": "2022-12-23T13:05:00.000+00:00"
            "mpid": "CPST",
            "reference_id": "a2022122300001",
            "request_time": "2022-12-23T13:00:00.000+00:00",
            "requested_quantity": 1000,
            "security_id_type": "TICKER",
            "security_id": "TSLA",
            "total_cost": 10.00,
            "trader_account": "100000",
            "trader_comment": "A request for locate of 1000 TSLA shares",
            "status": "FILLED",
        }
    ]
}
```
`borrow_rate` - Borrow rate for the security if held overnight.

`clst_reference_id` - A unique order reference id provided by Clear Street.

`cost_per_share` - Cost per share for the locate.

`currency` - Currency for the cost.

`desk_comment` - Comments provided by the Clear Street Stock Loan Desk.

`locate_id` - A unique locate id for the order, available once the order is OFFERED.

`located_quantity` - Quantity offered by Clear Street.

`located_time` - Time security was located.

`mpid` - Market Participant Identifier, assigned by Clear Street.

`reference_id` - Client-provided unique reference id for the locate request.

`request_time` - Time the locate request was received from the client.

`requested_quantity` - Quantity requested for the locate.

`security_id_type` - Type of the security to be located. Valid values for this field are: TICKER.

`security_id` - Security identifier corresponding to the `security_id_type` for the order

`total_cost` - Total cost for the locate.

`trader_account` - Optional client account ID assigned by Clear Street.

`trader_comment` - Comments provided by the trader.

`status` - Status of the locate. Valid values for this field are: PENDING, OFFERED, FILLED, REJECTED, DECLINED, EXPIRED.

## Locate Order List Request
```
GET - /locates?reference_id=a2022071300001
GET - /locates?trader_account=100000&mpid=CPST
```
`GET - /locates?reference_id=:reference_id&mpid=:mpid&trader_account=:trader_account`

1. Client requests a list of locate orders, providing filter criteria as request query parameters.  All filter parameters are optional.

2. Clear Street responds with a list of locate orders matching the filter criteria. 

### Request Parameters

`mpid` - Optional Market Participant Identifier, assigned by Clear Street.

`trader_account` -  Optional client account ID assigned by Clear Street.

`reference_id` - Client-provided unique reference id specified in the initial locate request.

### Response Object
```
// List Locate Orders Response
{
    "locates": [
        {
            "borrow_rate" : -40,
            "clst_reference_id" : "19dd51b8a64d953955c5c202",
            "cost_per_share": 0.01,
            "currency": "USD",
            "desk_comment": "Offer filled for locate of 1000 TSLA shares",
            "locate_id": "19dd51b8a64d953955c5c202",
            "located_quantity": 1000,
            "located_time": "2022-12-23T13:05:00.000+00:00"
            "mpid": "CPST",
            "reference_id": "a2022122300001",
            "request_time": "2022-12-23T13:00:00.000+00:00",
            "requested_quantity": 1000,
            "security_id": "TSLA",
            "security_id_type": "TICKER",
            "status": "FILLED",
            "total_cost": 10.00,
            "trader_account": "100000",
            "trader_comment": "A request for locate of 1000 TSLA shares",
        }
    ]
}
```
`borrow_rate` - Borrow rate for the security if held overnight.

`clst_reference_id` - A unique order reference id provided by Clear Street.

`cost_per_share` - Cost per share for the locate.

`currency` - Currency for the cost.

`desk_comment` - Comments provided by the Clear Street Stock Loan Desk.

`locate_id` - A unique locate id for the order, available once the order is OFFERED.

`located_quantity` - Quantity offered by Clear Street.

`located_time` - Time security was located.

`mpid` - Market Participant Identifier, assigned by Clear Street.

`reference_id` - Client-provided unique reference id for the locate request.

`request_time` - Time the locate request was received from the client.

`requested_quantity` - Quantity requested for the locate.

`security_id_type` - Type of the security to be located. Valid values for this field are: TICKER.

`security_id` - Security identifier corresponding to the `security_id_type` for the order

`total_cost` - Total cost for the locate.

`trader_account` - Optional client account ID assigned by Clear Street.

`trader_comment` - Comments provided by the trader.

`status` - Status of the locate. Valid values for this field are: PENDING, OFFERED, FILLED, REJECTED, DECLINED, EXPIRED.
