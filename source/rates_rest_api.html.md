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
To prevent excessive use, the limit is approximately 10,000 requests per business day. Please contact us if you wish to request an increase.
</aside>

### Request Object

Parameter | Type | Description
--------- | ----------- | -----------
mpid | string | **REQUIRED**. The Market Participant Identifier, assigned by Clear Street (e.g. CLST)
security_type | string | **REQUIRED**. The type of security (e.g. Ticker, FIGI, CUSIP, ISIN, or SEDOL)
security | string | **REQUIRED**. The security identifier

> Request Object

```json
{
  "mpid": "CLST",
  "security_type": "Ticker",
  "security": "SPY"
}
```

### Responses

<details>
  <summary><aside class="success">200 OK</aside></summary>
  
  A successful response
  ```json
  {
    "rate_id": "d18adab0-1527-11ee-be56-0242ac120002" 
    "borrow_rate": -20
  }
  ```

  Parameter | Type | Description
  --------- | ----------- | -----------
  rate_id | string | An ID for the rate response
  borrow_rate | string | Borrow rate for the security if held overnight
  
</details>

<details>
  <summary><aside class="warning">401 Unauthorized</aside></summary>
  
  Entering an MPID in which you are not authorized to view
  ```json
  {
    "message": "You do not have authorization to view this MPID"
  }
  ```
  Parameter | Type | Description
  --------- | ----------- | -----------
  message | string | Error message
  
</details>

<details>
  <summary><aside class="warning">400 Bad Request</aside></summary>
  
  An MPID that does not exist or one you are not authorized to view
  ```json
  {
    "message": "MPID is either invalid or you do not have authorization to view it"
  }
  ```
  Parameter | Type | Description
  --------- | ----------- | -----------
  message | string | Error message

  An invalid security type
  ```json
  {
    "message": "Security type is either missing or invalid"
  }
  ```
  Parameter | Type | Description
  --------- | ----------- | -----------
  message | string | Error message

  An invalid security
  ```json
  {
    "message": "Security is either missing or invalid"
  }
  ```
  Parameter | Type | Description
  --------- | ----------- | -----------
  message | string | Error message
  
</details>
