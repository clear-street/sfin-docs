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
To prevent excessive use, the limit is approximately 10,000 requests per day. Please contact us at api-support@clearstreet.io if you wish to request an increase.
</aside>

> Example Request Object

```json
{
  "mpid": "AAAA",
  "security_type": "TICKER",
  "security": "SPY"
}
```

### Request Object

| Parameter     | Type   | Description                                                                           |
| ------------- | ------ | ------------------------------------------------------------------------------------- |
| mpid          | string | **REQUIRED**. The Market Participant Identifier, assigned by Clear Street (e.g. CLST) |
| security_type | string | **REQUIRED**. The type of security (e.g. Ticker, FIGI, CUSIP, ISIN, or SEDOL)         |
| security      | string | **REQUIRED**. The security identifier                                                 |

### Successful Response

<aside class="success">200 OK</aside>

> A successful response

```json
{
  "borrow_rate": -1.0,
  "indicative_rate_expiry": "2023-07-21T21:28:15.851138Z",
  "mpid": "AAAA",
  "rate_id": "18d7fbc0-4a95-4b1a-ab15-89165038c367",
  "security": "AAPL",
  "security_type": "TICKER",
  "timestamp": "2023-07-21T21:18:15.851138Z"
}
```

| Parameter              | Type     | Description                                          |
| ---------------------- | -------- | ---------------------------------------------------- |
| borrow_rate            | number   | Borrow rate for the security if held overnight       |
| indicative_rate_expiry | ISO 8601 | Time until quoted rate expires                       |
| mpid                   | string   | The Market Participant Identifier that was requested |
| rate_id                | string   | ID of the rate response                              |
| security               | string   | The security that was requested                      |
| security_type          | string   | The security type that was requested                 |
| timestamp              | ISO 8601 | Timestamp of the rate response                       |

### Error Responses

<aside class="warning">400 Bad Request</aside>

> MPID does not have a valid billing configuration

```json
{
  "errorMessage": "Mpid AAAA not configured",
  "timestamp": "2023-07-17T21:06:27.394+00:00",
  "errorDetails": null
}
```

**MPID does not have a valid billing configuration**

| Parameter    | Type     | Description                                                    |
| ------------ | -------- | -------------------------------------------------------------- |
| message      | string   | Error message                                                  |
| timestamp    | ISO 8601 | Timestamp of message                                           |
| errorDetails | Object   | A JSON object containing a message explaining the errorMessage |

> Invalid security type

```json
{
  "errorMessage": "Invalid request",
  "timestamp": "2023-07-17T21:06:27.394+00:00",
  "errorDetails": {
    "securityType": "Invalid security type"
  }
}
```

**Invalid security type**

| Parameter    | Type     | Description                                                    |
| ------------ | -------- | -------------------------------------------------------------- |
| errorMessage | string   | Error message                                                  |
| timestamp    | ISO 8601 | Timestamp of message                                           |
| errorDetails | Object   | A JSON object containing a message explaining the errorMessage |

> Invalid security

```json
{
  "errorMessage": "Unable to determine instrument for provided id TICKER abcde",
  "timestamp": "2023-07-17T21:06:27.394+00:00",
  "errorDetails": null
}
```

**Invalid security**

| Parameter    | Type     | Description                                                    |
| ------------ | -------- | -------------------------------------------------------------- |
| message      | string   | Error message                                                  |
| timestamp    | ISO 8601 | Timestamp of message                                           |
| errorDetails | Object   | A JSON object containing a message explaining the errorMessage |

> Request made outside of business hours

```json
{
  "errorMessage": "Invalid request",
  "timestamp": "2023-07-17T21:06:27.394+00:00",
  "errorDetails": {
    "securityType": "Request outside of business hours"
  }
}
```

**Request made outside of business hours**

| Parameter    | Type     | Description                                                    |
| ------------ | -------- | -------------------------------------------------------------- |
| message      | string   | Error message                                                  |
| timestamp    | ISO 8601 | Timestamp of message                                           |
| errorDetails | Object   | A JSON object containing a message explaining the errorMessage |
