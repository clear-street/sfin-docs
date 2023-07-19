# Introduction

The Clear Street rates service enables users to request the borrow rate for a security

# API Endpoints

## Rates

### HTTP Request

`POST - /rates`

```
POST - /rates
```

1. Client requests the current borrow rate for a security

2. Clear Street responds with the borrow rate for the matching criteria

<aside class="notice">
Borrow rates can only be fetched during business hours starting from 4:00 AM to 10:00 PM EST.
To prevent excessive use, the limit is approximately 10,000 requests per day. Please contact us if you wish to request an increase.
</aside>

### Request Object

| Parameter     | Type   | Description                                                                           |
| ------------- | ------ | ------------------------------------------------------------------------------------- |
| mpid          | string | **REQUIRED**. The Market Participant Identifier, assigned by Clear Street (e.g. CLST) |
| security_type | string | **REQUIRED**. The type of security (e.g. Ticker, FIGI, CUSIP, ISIN, or SEDOL)         |
| security      | string | **REQUIRED**. The security identifier                                                 |

> Request Object

```json
{
  "mpid": "AAAA",
  "security_type": "TICKER",
  "security": "SPY"
}
```

### Responses

<details>
  <summary><aside class="success">200 OK</aside></summary>

A successful response

```json
{
  "rate_id": "d18adab0-1527-11ee-be56-0242ac120002",
  "mpid": "AAAA",
  "security_type": "TICKER",
  "security": "SPY",
  "borrow_rate": 5.183
}
```

| Parameter     | Type   | Description                                          |
| ------------- | ------ | ---------------------------------------------------- |
| rate_id       | string | An ID for the rate response                          |
| mpid          | string | The Market Participant Identifier that was requested |
| security_type | string | The type of security that was requested              |
| security      | string | The security identifier that was requested           |
| borrow_rate   | number | Borrow rate for the security if held overnight       |

</details>

<details>
  <summary><aside class="warning">400 Bad Request</aside></summary>

MPID does not have a valid billing configuration

```json
{
  "errorMessage": "Mpid AAAA not configured",
  "timestamp": "2023-07-17T21:06:27.394+00:00",
  "errorDetails": null
}
```

| Parameter    | Type   | Description                                                    |
| ------------ | ------ | -------------------------------------------------------------- |
| message      | string | Error message                                                  |
| timestamp    | string | Timestamp of message                                           |
| errorDetails | Object | A JSON object containing a message explaining the errorMessage |

An invalid security type

```json
{
  "errorMessage": "Invalid request",
  "timestamp": "2023-07-17T21:06:27.394+00:00",
  "errorDetails": {
    "securityType": "Invalid security type"
  }
}
```

| Parameter    | Type   | Description                                                    |
| ------------ | ------ | -------------------------------------------------------------- |
| errorMessage | string | Error message                                                  |
| timestamp    | string | Timestamp of message                                           |
| errorDetails | Object | A JSON object containing a message explaining the errorMessage |

An invalid security

```json
{
  "errorMessage": "Unable to determine instrument for provided id TICKER abcde",
  "timestamp": "2023-07-17T21:06:27.394+00:00",
  "errorDetails": null
}
```

| Parameter    | Type   | Description                                                    |
| ------------ | ------ | -------------------------------------------------------------- |
| message      | string | Error message                                                  |
| timestamp    | string | Timestamp of message                                           |
| errorDetails | Object | A JSON object containing a message explaining the errorMessage |

Request made outside of business hours

```json
{
  "errorMessage": "Invalid request",
  "timestamp": "2023-07-17T21:06:27.394+00:00",
  "errorDetails": {
    "securityType": "Request outside of business hours"
  }
}
```

| Parameter    | Type   | Description                                                    |
| ------------ | ------ | -------------------------------------------------------------- |
| message      | string | Error message                                                  |
| timestamp    | string | Timestamp of message                                           |
| errorDetails | Object | A JSON object containing a message explaining the errorMessage |

</details>
