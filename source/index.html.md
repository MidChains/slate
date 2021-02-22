--- 

title: MidChains API Documentation

language_tabs: 
   - python 

toc_footers: 
   - <a href='#'>Sign Up for a Developer Key</a> 
   - <a href='https://github.com/lavkumarv'>Documentation Powered by lav</a> 

includes: 
   - errors 

search: true 

--- 

# Introduction 

Welcome to MidChains API documentation. These documents outline trading platform functionality, market details, and APIs.

APIs are separated into two categories: Market data feed and trading. Feed APIs provide market data and are public. Trading APIs require authentication and provide access to placing orders and other account information. 

**Version:** 1.0.0 

# WebSocket Market Data Feed

The websocket market data feed provides real-time market data updates for orders and trades.

`wss://ws-midchains.com`

## ***POST*** 

**Summary:** Add a new pet to the store

**Description:** 

### HTTP Request 
`***POST*** /pet` 

**Parameters**

| Name | Located in | Description | Required | Type |
| ---- | ---------- | ----------- | -------- | ---- |
| body | body | Pet object that needs to be added to the store | Yes |  |

**Responses**

| Code | Description |
| ---- | ----------- |
| 405 | Invalid input |


## SubscribeLevel1

**Category:** User<br />
**Permissions:** Operator, Trading, Level1MarketData<br />
**Call Type:** Synchronous

Retrieves the latest Level 1 Ticker information and then subscribes the user to ongoing Level 1 market data event updates for one specific instrument. 

The **SubscribeLevel1** call responds with the Level 1 response shown below. The OMS then periodically sends in the same format as this response *Leve1UpdateEvent* information when best-bid/best-offer issue, until you send the **UnsubscribeLevel1** call.

Only a user with Operator permission can issue Level1MarketData permission using the call ***AddUserMarketDataPermission.** 

### Request

> You can identify the instrument with its ID or with its market symbol (string).

```python
{
	"OMSId":  1,
	"InstrumentId": 0
}
```

> Or

```python
{
	"OMSId":  1,
	"Symbol": "BTCUSD"
}
```

| Key          | Value                                                        |
| ------------ | ------------------------------------------------------------ |
| OMSId        | **integer**. The ID of the Order Management System on which the instrument trades. |
| InstrumentId | **integer**. The ID of the instrument you’re tracking. *Conditionally optional.* |
| Symbol       | **string**. The symbol of the instrument you’re tracking. *Conditionally optional.* |

### Response

The **SubscribeLevel1** response and *Level1UpdateEvent* both provide the same information. 

```python
{
  "OMSId": 1,
  "InstrumentId": 1,
  "BestBid": 6423.57,
  "BestOffer": 6436.53,
  "LastTradedPx": 6423.57,
  "LastTradedQty": 0.96183964,
  "LastTradeTime": 1534862990343,
  "SessionOpen": 6249.64,
  "SessionHigh": 11111,
  "SessionLow": 4433,
  "SessionClose": 6249.64,
  "Volume": 0.96183964,
  "CurrentDayVolume": 3516.31668185,
  "CurrentDayNumTrades": 8529,
  "CurrentDayPxChange": 173.93,
  "CurrentNotional": 0.0,
  "Rolling24HrNotional": 0.0,
  "Rolling24HrVolume": 4319.63870783,
  "Rolling24NumTrades": 10585,
  "Rolling24HrPxChange": -0.4165607307408487,
  "TimeStamp": "1534862990358"
}
```

| Key               | Value                                                        |
| -------------------- | ------------------------------------------------------------ |
| OMSId                | **integer**. The ID of the Order Management System on which the instrument trades. |
| InstrumentId         | **integer**. The ID of the instrument being tracked.         |
| BestBid              | **real**. The current best bid for the instrument.           |
| BestOffer            | **real**. The current best offer for the instrument.         |
| LastTradedPx         | **real**. The last-traded price for the instrument.          |
| LastTradedQty        | **real**. The last-traded quantity for the instrument.       |
| LastTradeTime        | **long integer**. The time of the last trade, in POSIX format. |
| SessionOpen          | **real**. Opening price. In markets with openings and closings, this is the opening price for the current session; in 24-hour markets, it is the price as of UTC Midnight. |
| SessionHigh          | **real**. Highest price during the trading day, either during a session with opening and closing prices or UTC midnight to UTC midnight. |
| SessionLow           | **real**. Lowest price during the trading day, either during a session with opening and closing prices or UTC midnight to UTC midnight. |
| SessionClose         | **real**. The closing price. In markets with openings and closings, this is the closing price for the current session; in 24-hour markets, it is the price as of UTC Midnight. |
| Volume               | **real**. The last-traded quantity for the instrument, same value as LastTradedQty  |
| CurrentDayVolume     | **real**. The unit volume of the instrument traded either during a session with openings and closings or in 24-hour markets, the period from UTC Midnight to UTC Midnight. |
| CurrentDayNumTrades  | **integer**. The number of trades during the current day, either during a session with openings and closings or in 24-hour markets, the period from UTC Midnight to UTC Midnight. |
| CurrentDayPxChange   | **real**. Current day price change, either during a trading session or UTC Midnight to UTC midnight. |
| CurrentNotional            | **decimal.** Current day quote volume - resets at UTC Midnight. |
| Rolling24HrNotional        | **decimal.** Rolling 24 hours quote volume. |
| Rolling24HrVolume    | **real**. Unit volume of the instrument during the past 24 hours, regardless of time zone. Recalculates continuously. |
| Rolling24HrNumTrades | **integer**. Number of trades during the past 24 hours, regardless of time zone. Recalculates continuously. |
| Rolling24HrPxChange  | **real**. Price change during the past 24 hours, regardless of time zone. Recalculates continuously. |
| TimeStamp            | **long integer**. The time this information was provided, in POSIX format.   |

# Authentication 

|oauth2|*OAuth 2.0*|
|---|---| 

|apiKey|*API Key*|
|---|---| 

# /PET
## ***POST*** 

**Summary:** Add a new pet to the store

**Description:** 

### HTTP Request 
`***POST*** /pet` 

**Parameters**

| Name | Located in | Description | Required | Type |
| ---- | ---------- | ----------- | -------- | ---- |
| body | body | Pet object that needs to be added to the store | Yes |  |

**Responses**

| Code | Description |
| ---- | ----------- |
| 405 | Invalid input |

## ***PUT*** 

**Summary:** Update an existing pet

**Description:** 

### HTTP Request 
`***PUT*** /pet` 

**Parameters**

| Name | Located in | Description | Required | Type |
| ---- | ---------- | ----------- | -------- | ---- |
| body | body | Pet object that needs to be added to the store | Yes |  |

**Responses**

| Code | Description |
| ---- | ----------- |
| 400 | Invalid ID supplied |
| 404 | Pet not found |
| 405 | Validation exception |

# /PET/FINDBYSTATUS
## ***GET*** 

**Summary:** Finds Pets by status

**Description:** Multiple status values can be provided with comma separated strings

### HTTP Request 
`***GET*** /pet/findByStatus` 

**Parameters**

| Name | Located in | Description | Required | Type |
| ---- | ---------- | ----------- | -------- | ---- |
| status | query | Status values that need to be considered for filter | Yes | array |

**Responses**

| Code | Description |
| ---- | ----------- |
| 200 | successful operation |
| 400 | Invalid status value |

# /PET/FINDBYTAGS
## ***GET*** 

**Summary:** Finds Pets by tags

**Description:** Muliple tags can be provided with comma separated strings. Use         tag1, tag2, tag3 for testing.

### HTTP Request 
`***GET*** /pet/findByTags` 

**Parameters**

| Name | Located in | Description | Required | Type |
| ---- | ---------- | ----------- | -------- | ---- |
| tags | query | Tags to filter by | Yes | array |

**Responses**

| Code | Description |
| ---- | ----------- |
| 200 | successful operation |
| 400 | Invalid tag value |

# /PET/{PETID}
## ***GET*** 

**Summary:** Find pet by ID

**Description:** Returns a single pet

### HTTP Request 
`***GET*** /pet/{petId}` 

**Parameters**

| Name | Located in | Description | Required | Type |
| ---- | ---------- | ----------- | -------- | ---- |
| petId | path | ID of pet to return | Yes | long |

**Responses**

| Code | Description |
| ---- | ----------- |
| 200 | successful operation |
| 400 | Invalid ID supplied |
| 404 | Pet not found |

## ***POST*** 

**Summary:** Updates a pet in the store with form data

**Description:** 

### HTTP Request 
`***POST*** /pet/{petId}` 

**Parameters**

| Name | Located in | Description | Required | Type |
| ---- | ---------- | ----------- | -------- | ---- |
| petId | path | ID of pet that needs to be updated | Yes | long |
| name | formData | Updated name of the pet | No | string |
| status | formData | Updated status of the pet | No | string |

**Responses**

| Code | Description |
| ---- | ----------- |
| 405 | Invalid input |

## ***DELETE*** 

**Summary:** Deletes a pet

**Description:** 

### HTTP Request 
`***DELETE*** /pet/{petId}` 

**Parameters**

| Name | Located in | Description | Required | Type |
| ---- | ---------- | ----------- | -------- | ---- |
| api_key | header |  | No | string |
| petId | path | Pet id to delete | Yes | long |

**Responses**

| Code | Description |
| ---- | ----------- |
| 400 | Invalid ID supplied |
| 404 | Pet not found |

# /PET/{PETID}/UPLOADIMAGE
## ***POST*** 

**Summary:** uploads an image

**Description:** 

### HTTP Request 
`***POST*** /pet/{petId}/uploadImage` 

**Parameters**

| Name | Located in | Description | Required | Type |
| ---- | ---------- | ----------- | -------- | ---- |
| petId | path | ID of pet to update | Yes | long |
| additionalMetadata | formData | Additional data to pass to server | No | string |
| file | formData | file to upload | No | file |

**Responses**

| Code | Description |
| ---- | ----------- |
| 200 | successful operation |

# /STORE/INVENTORY
## ***GET*** 

**Summary:** Returns pet inventories by status

**Description:** Returns a map of status codes to quantities

### HTTP Request 
`***GET*** /store/inventory` 

**Parameters**

| Name | Located in | Description | Required | Type |
| ---- | ---------- | ----------- | -------- | ---- |

**Responses**

| Code | Description |
| ---- | ----------- |
| 200 | successful operation |

# /STORE/ORDER
## ***POST*** 

**Summary:** Place an order for a pet

**Description:** 

### HTTP Request 
`***POST*** /store/order` 

**Parameters**

| Name | Located in | Description | Required | Type |
| ---- | ---------- | ----------- | -------- | ---- |
| body | body | order placed for purchasing the pet | Yes |  |

**Responses**

| Code | Description |
| ---- | ----------- |
| 200 | successful operation |
| 400 | Invalid Order |

# /STORE/ORDER/{ORDERID}
## ***GET*** 

**Summary:** Find purchase order by ID

**Description:** For valid response try integer IDs with value >= 1 and <= 10.         Other values will generated exceptions

### HTTP Request 
`***GET*** /store/order/{orderId}` 

**Parameters**

| Name | Located in | Description | Required | Type |
| ---- | ---------- | ----------- | -------- | ---- |
| orderId | path | ID of pet that needs to be fetched | Yes | long |

**Responses**

| Code | Description |
| ---- | ----------- |
| 200 | successful operation |
| 400 | Invalid ID supplied |
| 404 | Order not found |

## ***DELETE*** 

**Summary:** Delete purchase order by ID

**Description:** For valid response try integer IDs with positive integer value.         Negative or non-integer values will generate API errors

### HTTP Request 
`***DELETE*** /store/order/{orderId}` 

**Parameters**

| Name | Located in | Description | Required | Type |
| ---- | ---------- | ----------- | -------- | ---- |
| orderId | path | ID of the order that needs to be deleted | Yes | long |

**Responses**

| Code | Description |
| ---- | ----------- |
| 400 | Invalid ID supplied |
| 404 | Order not found |

# /USER
## ***POST*** 

**Summary:** Create user

**Description:** This can only be done by the logged in user.

### HTTP Request 
`***POST*** /user` 

**Parameters**

| Name | Located in | Description | Required | Type |
| ---- | ---------- | ----------- | -------- | ---- |
| body | body | Created user object | Yes |  |

**Responses**

| Code | Description |
| ---- | ----------- |
| default | successful operation |

# /USER/CREATEWITHARRAY
## ***POST*** 

**Summary:** Creates list of users with given input array

**Description:** 

### HTTP Request 
`***POST*** /user/createWithArray` 

**Parameters**

| Name | Located in | Description | Required | Type |
| ---- | ---------- | ----------- | -------- | ---- |
| body | body | List of user object | Yes |  |

**Responses**

| Code | Description |
| ---- | ----------- |
| default | successful operation |

# /USER/CREATEWITHLIST
## ***POST*** 

**Summary:** Creates list of users with given input array

**Description:** 

### HTTP Request 
`***POST*** /user/createWithList` 

**Parameters**

| Name | Located in | Description | Required | Type |
| ---- | ---------- | ----------- | -------- | ---- |
| body | body | List of user object | Yes |  |

**Responses**

| Code | Description |
| ---- | ----------- |
| default | successful operation |

# /USER/LOGIN
## ***GET*** 

**Summary:** Logs user into the system

**Description:** 

### HTTP Request 
`***GET*** /user/login` 

**Parameters**

| Name | Located in | Description | Required | Type |
| ---- | ---------- | ----------- | -------- | ---- |
| username | query | The user name for login | Yes | string |
| password | query | The password for login in clear text | Yes | string |

**Responses**

| Code | Description |
| ---- | ----------- |
| 200 | successful operation |
| 400 | Invalid username/password supplied |

# /USER/LOGOUT
## ***GET*** 

**Summary:** Logs out current logged in user session

**Description:** 

### HTTP Request 
`***GET*** /user/logout` 

**Parameters**

| Name | Located in | Description | Required | Type |
| ---- | ---------- | ----------- | -------- | ---- |

**Responses**

| Code | Description |
| ---- | ----------- |
| default | successful operation |

# /USER/{USERNAME}
## ***GET*** 

**Summary:** Get user by user name

**Description:** 

### HTTP Request 
`***GET*** /user/{username}` 

**Parameters**

| Name | Located in | Description | Required | Type |
| ---- | ---------- | ----------- | -------- | ---- |
| username | path | The name that needs to be fetched. Use user1 for testing.  | Yes | string |

**Responses**

| Code | Description |
| ---- | ----------- |
| 200 | successful operation |
| 400 | Invalid username supplied |
| 404 | User not found |

## ***PUT*** 

**Summary:** Updated user

**Description:** This can only be done by the logged in user.

### HTTP Request 
`***PUT*** /user/{username}` 

**Parameters**

| Name | Located in | Description | Required | Type |
| ---- | ---------- | ----------- | -------- | ---- |
| username | path | name that need to be updated | Yes | string |
| body | body | Updated user object | Yes |  |

**Responses**

| Code | Description |
| ---- | ----------- |
| 400 | Invalid user supplied |
| 404 | User not found |

## ***DELETE*** 

**Summary:** Delete user

**Description:** This can only be done by the logged in user.

### HTTP Request 
`***DELETE*** /user/{username}` 

**Parameters**

| Name | Located in | Description | Required | Type |
| ---- | ---------- | ----------- | -------- | ---- |
| username | path | The name that needs to be deleted | Yes | string |

**Responses**

| Code | Description |
| ---- | ----------- |
| 400 | Invalid username supplied |
| 404 | User not found |

<!-- Converted with the swagger-to-slate https://github.com/lavkumarv/swagger-to-slate -->
