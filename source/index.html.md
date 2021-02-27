--- 

title: MidChains API Documentation

language_tabs: 
   - python 
   - java

toc_footers: 
   - <a href='www.midchians.com'>Sign Up to MidChains</a> 

# includes: 
#    - errors 

search: true 

--- 

# INTRODUCTION 

## Overview

Welcome to MidChains API documentation. These documents outline trading platform functionality, market details, and APIs.

APIs are separated into two categories: Market data feed and trading. Feed APIs provide market data and are public. Trading APIs require authentication and provide access to placing orders and other account information. 

**Version:** 1.0.0 

## Rate Limits

The MidChains API is rate limited to prevent abuse that would degrade our ability to maintain consistent API performance for all users.

### WebSocket API

#### Public Enpoints
We throttle public endpoints by IP: 3 requests per second, up to 6 requests per second in bursts. Some endpoints may have custom rate limits.

#### Private Endpoints
We throttle private endpoints by user ID: 5 requests per second, up to 10 requests per second in bursts. Some endpoints may have custom rate limits.

### Financial Information Exchange API
The FIX API throttles the number of incoming messages to 10 commands per second. 

## Time Format

The MidChains API timestamp fields us the Epoch/POSIX format. Unix and POSIX measure time as the number of seconds that have passed since 1 January 1970 00:00:00 UT

```python

'time': '1614331055706'

```

## Changelog

Recent changes and additions to MidChains API.

### 2021-01-30

# &nbsp;

# WEBSOCKET API

The websocket market data feed provides real-time market data updates for orders and trades.

`wss://ws.midchains.com:8081`

> Example Structure.

```python
from websocket import create_connection

ws = create_connection("wss://ws.midchains.com:8081")
ws.send('''
   {
      "type": "TYPE",
      "request": "REQUEST"
   }
''')

print(ws.recv())

ws.close()

```

## Protocol Overview 

The websocket feed uses a bidirectional protocol, which encodes all messages as JSON objects. All messages have a type attribute that can be used to handle the message appropriately.

Please note that new message types can be added at any point in time. Clients are expected to ignore messages they do not support.


# Subscribe Market Data

**Category:** Market Data<br />
**Permissions:** Public<br />
**Endpoint:** subscribe

Retrieves the latest order book information and then subscribes the user to ongoing market data event updates for the trading pairs specified.. 

The **Subscribe** call responds with the response shown below. Messages are then periodically sent in the same format as this response *UpdateEvent* information when best-bid/best-offer issue, until you close the connection.

### Request


> You can identify multiple trading pairs (security) by using '*".

```python

   {
      "type": "subscribe",
      "request": [
         {
            "msg":"book",
            "security":"BTCUSD",
            "dest":"CROX"
         }
      ]
   }

```

> Response

```python

   {
      'security': 'LTCUSD', 
      'books': [{
         'side': 'B', 
         'act': 'U', 
         'src': 'CROX', 
         'price': '184.3', 
         'qty': '2.85', 
         'id': 120946647655360, 
         'time': '1614331055706', 
         'mpid': 'ANON', 
         'key': 'NA'
      }], 
      'type': 'book'
   }

```

| Key          | Value                                                        | Required |
| ------------ | ------------------------------------------------------------ | -------- |
| msg          | **string**. Specify the type of data to subscribe to. use 'book' for order book data. | Yes |
| security     | **string**. Specify the trading pair you wish to subscribe to. | Yes |
| dest         | **string**. This value will alway be set to CROX. | Yes |

### Response

| Key               | Value                                                        |
| -------------------- | ------------------------------------------------------------ |
| Security           | **string**. The trading pair being subscribed to. |
| Side               | **string**. The order side.        |
| Price              | **string**. The price of the order.           |
| Qty                | **string**. The size of the order.       |
| Time               | **string**. The time of the order, in POSIX format.        |


# Subscribe Trade History

**Category:** Trade History<br />
**Permissions:** Public<br />
**Endpoint:** subscribe

Retrieves the latest trade information and then subscribes the user to ongoing trade event updates for the trading pairs specified.. 

The **Subscribe** call responds with the response shown below. TMessages are then periodically sent in the same format as this response until you close the connection.

### Request


> You  must specify a single trading pair

```python

   {
      "type": "subscribe",
      "request": [
         {
            "msg":"trade",
            "security":"BTCUSD",
            "dest":"CROX"
         }
      ]
   }

```

> Response

```python

   {
      'security': 'BTCUSD', 
      'src': 'CROX', 
      'price': '150', 
      'qty': '1', 
      'time': '1614334523954', 
      'type': 'trade', 
      'matchid': '5Z3YF5Z0N8PK'
   }

```

| Key          | Value                                                        | Required |
| ------------ | ------------------------------------------------------------ | ---------|
| msg          | **string**. Specify the type of data to subscribe to. use 'trade' for trade history data. | Yes |
| security     | **string**. Specify the trading pair you wish to subscribe to. | Yes |
| dest         | **string**. This value will alway be set to CROX. | Yes |

### Response

| Key               | Value                                                        |
| -------------------- | ------------------------------------------------------------ |
| Security           | **string**. The trading pair being subscribed to. |
| Price              | **string**. The price of the order.           |
| Qty                | **string**. The size of the order.       |
| Time               | **string**. The time of the order, in POSIX format.        |



# Authentication

**Category:** Authentication<br />
**Permissions:** Trading<br />

Once client establishes the web socket connection, the client has 30 seconds to complete the login process. Otherwise, the socket will be closed by MidChains.

## CHALLENGE
First client needs to send a challenge request to the system.

### Response

| Key               | Value                                                        |
| -------------------- | ------------------------------------------------------------ |
| Key                  | **string**. The trading pair being subscribed to. |

> Request

```python

   {
      "type": "challenge"
   }

```

> Response

```python

   {
      "result":"OK",
      "type":"challenge",
      "key":"MIGfMA0GCSqGSIb3DQEBAQUAA4GNADCBiQKBgQCvBXTe278Lwg2MoI7iGKolSYuF+sNFKrsZplxCN9x0kItU3KIf8+1q60ILLwLewCEf7foxzpWp32j9YYU9vNBghuJ7BHcDYTffTRcv+QdNno491j701Hq7DIw13AGCQQTRcnfclvblnytIEWoQsiUvPJcdiWgqJIX3IQGA47a+uwIDAQAB"
   }

```


The response contains a “key” field, which contains a public key string the client must use to encrypt the password field. The “key” field is a 64 base ASCII print out of the binary byte array for the corresponding public key of asymmetric public/ private key pair. MidChains will hold the private key while sending clients the public key. Client will first convert the ASCII back to binary byte array. Then use that binary byte array to create a “RSA” public key instance. Afterwards, client will use that RSA public key instance to encrypt info required in this order entry API, e.g. password encryption.


>  EXAMPLE.

```java

   byte[] array = javax.xml.bind.DatatypeConverter.parseBase64Binary(key);
   KeyFactorykf = KeyFactory.getInstance(“RSA”);
   publicKey = kf.generatePublic(new X509EncodedKeySpec(array));
   Cipher = Cipher.getInstance(“RSA”);
   cipher.init(Cipher.ENCRYPT_MODE, publicKey);
   byte[] result = cipher.doFinal ("PASSWORD".getBytes(“UTF-8”));
   String output = javax.xml.bind.DatatypeConverter.printBase64Binary(result);

```

```python

    pubkey = f'-----BEGIN PUBLIC KEY-----\n'+key+'\n-----END PUBLIC KEY-----'
    # encryption
    text = "PASSWORD".encode('utf-8')
    pub_bio = BIO.MemoryBuffer(pubkey.encode('utf-8'))
    pub_rsa = RSA.load_pub_key_bio(pub_bio)
    secret = pub_rsa.public_encrypt(text, RSA.pkcs1_padding)
    sign = base64.b64encode(secret) 
    sign = sign.decode("utf-8")

```

## LOGIN

After encryption of password, the next step is to send in the login request. The string in the “pass” field is from the output result in sample above.

| Key               | Value                                                        | Required |
| -------------------- | ------------------------------------------------------------ | ----- |
| Userid                  | **string**. The username of the user. | Yes |
| pass                  | **string**. The pass phrase generated from the challenge and steps above. | Yes |

### Response

| Key               | Value                                                        |
| -------------------- | ------------------------------------------------------------ |
| Result                  | **string**. The reposne will either contain OK if successful or a rejection reason. |

MidChains will send current open orders and position info upon login. If a user places a new order, MidChains will send an order update automatically. If the user trades, they will receive a trade update and corresponding position updates.


> Request

```python
   {
      "type":"login",
      "userid":"USERNAME",
      "pass":"s7UW26iGE/iVfk2ihPYIcyzRqZRi/Ztb23UNMomf3xrBzGKUHKzfNwZe5PIR/0zvfevYvkJnKLQVhR4U9/kObD/Ir0z6mBfLLgFwEcRm08jYI/nk7lDU+W32PqduTOCThlkXYueQslK54vR9rKvMs="
   }
```

> Response

```python
   {
      "result":"OK",
      "type":"login"
   }
   or
   {
      "result":"invalid user/password",
      "type":"login"
   }
```


## LOGOUT

> Request

```python
   {
      "type":"logout",
   }
```

> Response

```python
   {
      "result":"OK",
      "type":"logout"
   }
```

This is for clean logout. Once MidChains receives this message, it will immediately terminate the web socket connection.


# Order Placement

**Category:** Trading<br />
**Permissions:** Trading<br />

You can place two types of orders: limit and market. Orders can only be placed if your account has sufficient funds.

## ADDORDER
You can place two types of orders: limit and market. Orders can only be placed if your account has sufficient funds.

| Key                | Value                                  | Required |
| -----------------  | ----------------------------------- | -------- |
| Security           | **string**. The trading pair you wish to place the order in. | Yes |
| Side               | **string**. The order side. Options are buy 'B' or sell 'S'. | Yes |
| Qty                | **string**. Specify the quantity of order. Required for all orders except market buy orders. | Conditionally |
| Price              | **string**. Specify the order price. Required for limit orders | Conditionally |
| Amount             | **string**. Specify the amount in fiat for the order. Required for market buy orders. | Conditionally |
| Ordertype          | **string**. Specify if sending a limit or market order. Options are 'LMT' or 'MKT'. Default is 'LMT'.
| TIF                | **string**. Options are GTC, IOC, FOK, GTT. GTC is the default | No |
| Stoptype           | **string**. Used to place a stop limit ot stop market order by setiing value to 'STOP'. Default is not set. | No |
| Stopprice          | **string**. Specifiy the stop price if stop type is set. | Conditionally |
| Exptime            | **string**. The expiry time of the order, in POSIX format. Required if TIF is set to GTT| Conditionally |

> Request

```python

   {
      "type":"addorder",
      "security":"LTCUSD",
      "side":"B",
      "ordertype":"MKT",
      "tif":"IOC",
      "amount":"180"
   }

```

> Response

```python

   {
      "result":"OK",
      "tif":"IOC",
      "ordertype":"MKT"
      "refno":"5Z451OTIU191C3",
      "security":"LTCUSD",
      "side":"B",
      "amount":"180",
      "type":"addorder",
   }
   or
   {
      "result":"cannot use qty for market buy order",
      "tif":"IOC",
      "ordertype":"MKT"
      "security":"LTCUSD",
      "side":"B",
      "qty":"180",
      "type":"addorder",
   } 
```

## CANCELORDER
To cancel an open order, you can use the cancelorder endpoint. You must pass the MidChains assigned 'refno' to specify what order to cancel.

| Key                | Value                                  | Required |
| -----------------  | ----------------------------------- | -------- |
| Security           | **string**. The trading pair you wish to cancel the order for. | Yes |
| Refno               | **string**. The MidChains generated order id. | Yes |


> Request

```python

   {
      "type":"cancelorder",
      "security":"BTCUSD",
      "refno":"5FSDIAGCE768A0"
   }

```

> Response

```python

   {
      "result":"OK",
      "refno":"5FSDIAGCE768A0",
      "security":"BTCUSD",
      "type":"cancelorder"
   }
   or
   {
      "result":"order not found",
      "refno":"5FSDIAGCE768A0",
      "security":"BTCUSD",
      "type":"cancelorder"
   }


```

# Order & Trade Query

**Category:** Trading<br />
**Permissions:** Trading<br />

Users can retrieve open and executed order information.

## QUERYORDER
Users can retrieve open and executed order information.

| Key                | Value                                  | Required |
| -----------------  | ----------------------------------- | -------- |
| Security           | **string**. The trading pair you wish to query orders for. | Yes |
| Refno               | **string**. The MidChains generated order id. | Yes |

### RESPONSE

| Key                | Value                                  | Required |
| -----------------  | ----------------------------------- | -------- |
| Updtime           | **string**. Time of the last order update, in POSIX format. | - |
| TIF               | **string**. The order's time in force. | - |
| Execamount        | **string**. The amount of the order that has executed in fiat value. | - |
| Qty               | **string**. The order's original quantity. | - |
| Security          | **string**. The trading pair. | - |
| Liveqty           | **string**. The order's remaining open quantity. | - |
| Refno             | **string**. The MidChains generated order id. | - |
| Execqty           | **string**. The order quantity that has been executed. | - |
| Side              | **string**. The order side | - |


> Request

```python

   {
      "type":"queryorder",
      "security":"LTCUSD",
      "refno":"5GS08WTIE8CWA0"
   }

```

> Response

```python

   {
      "result":"OK",
      "refno":"5GS08WTIE8CWA0",
      "data": {
         "updtime":"1557505235085",
         "tif":"GTC",
         "execamount":"10000",
         "qty":"1000",
         "security":"BTCUSD",
         "type":"order",
         "clientorderid":"ID1",
         "liveqty":"900",
         "category":"CROX",
         "refno":"5GS08WTIE8CWA0",
         "price":"100",
         "execqty":"100",
         "side":"S"
      },
      "security":"BTCUSD",
      "type":"queryorder"
   }
   or
   {
      "result":"order not found",
      "refno":"5GS08WTIE8CWA0",
      "security":"BTCUSD",
      "type":"queryorder"
   } 

```

## QUERYMULTIORDERS
Users can retrieve open and executed order information.

| Key                | Value                                  | Required |
| -----------------  | ----------------------------------- | -------- |
| Security           | **string**. The trading pair you wish to query orders for. | No |
| Fromtime           | **string**. Specify the cut off time in POSIX format. | No |

### RESPONSE

| Key                | Value                                  | Required |
| -----------------  | ----------------------------------- | -------- |
| Updtime           | **string**. Time of the last order update, in POSIX format. | - |
| TIF               | **string**. The order's time in force. | - |
| Execamount        | **string**. The amount of the order that has executed in fiat value. | - |
| Qty               | **string**. The order's original quantity. | - |
| Security          | **string**. The trading pair. | - |
| Liveqty           | **string**. The order's remaining open quantity. | - |
| Refno             | **string**. The MidChains generated order id. | - |
| Execqty           | **string**. The order quantity that has been executed. | - |
| Side              | **string**. The order side | - |


> Request

```python

   {
      "type":"querymultiorder",
      "security":"LTCUSD",
      "refno":"5GS08WTIE8CWA0"
   }

```

> Response

```python

   {
      "result":"OK",
      "data": [
         {
            "updtime":"1562593041099",
            "liveqty":"0",
            "refno":"5IF6Q7Y8LI8WA0",
            "category":"STAGE",
            "execqty":"0",
            "price":"100.59",
            "side":"B",
            "execamount":"0",
            "qty":"100",
            "rec_no":1,
            "security":"ZVZZT",
            "type":"order"
         },
         {
            "updtime":"1562593042339",
            "liveqty":"0",
            "refno":"5IF6Q7Y8LGPKA7",
            "category":"STAGE",
            "execqty":"0",
            "price":"100.58",
            "side":"B",
            "execamount":"0",
            "qty":"100",
            "rec_no":2,
            "security":"ZVZZT",
            "type":"order"
         },
         {
            "updtime":"1562593042339",
            "liveqty":"0",
            "refno":"5IF6Q7Y8LGPJA7",
            "category":"STAGE",
            "execqty":"0",
            "price":"100.57",
            "side":"B",
            "execamount":"0",
            "qty":"100",
            "rec_no":3,
            "security":"ZVZZT",
            "type":"order"
         }
      ]
      "type":"querymultiorders",
      "total_rec":3
   }

   or
   {
      "result":"OK",
      "type":"querymultiorders",
      "total_rec":0
   }


```

## QUERYTRADE
This is to query the trades for a user. 


| Key                | Value                                  | Required |
| -----------------  | ----------------------------------- | -------- |
| Security           | **string**. The trading pair you wish to query trades for. | No |
| Fromtime           | **string**. Specify the cut off time in POSIX format. | No |

### RESPONSE

“total_rec” tells you how many records there are available.

If there are more than 100 records, the system will send you multiple response, each will have 100 records.

| Key                | Value                                  | Required |
| -----------------  | ----------------------------------- | -------- |
| Trdtime           | **string**. The execution time, in POSIX format. | - |
| Execprice         | **string**. The execution price of the trade. | - 
| Security          | **string**. The trading pair. | - |
| Refno             | **string**. The MidChains generated order id. | - |
| Traderefno        | **string**. The MidChains generated execution id. | - |
| Execqty           | **string**. The order quantity that has been executed. | - |
| Rec_no            | **string**. The record number of the response. | - |
| Side              | **string**. The order side | - |


> Request

```python

   {
      "type":"querytrade",
      "security":"LTCUSD",
      "fromtime":"1557323619303"
   }

```

> Response

```python

   {
      "result":"OK",
      "data":[
         {
            "refno":"5GQBE9BCZOO0A3",
            "category":"CROX",
            "execqty":"10",
            "execprice":"30",
            "traderefno":"5GQBE9BCZOO0A32_5GQBE9BCZOO2",
            "side":"B",
            "trdtime":"1557323619303",
            "rec_no":1,
            "security":"MSFT",
            "type":"sale"
         },
         {
            "refno":"5GQBE9BCZN4GA7",
            "category":"CROX",
            "execqty":"10",
            "execprice":"100.5",
            "traderefno":"5GQBE9BCZN4GA72_5GQBE9BCZN4I",
            "side":"B",
            "trdtime":"1557323603427",
            "rec_no":2,
            "security":"ZVZZT",
            "type":"sale"
         }
      ],
      "type":"querytrade",
      "total_rec":2
   }
   or
   {
      "result":"OK",
      "userid":"0003",
      "type":"querytrade",
      "total_rec":0
   }

```

# Account Balances

**Category:** Balances and Positions<br />
**Permissions:** Trading<br />

Users can receive balance information / positions and continuous updates afterwards.

## QUERYPOS
Users can receive balance information / positions and continuous updates afterwards.

| Key                | Value                                  | Required |
| -----------------  | ----------------------------------- | -------- |
| Security           | **string**. The virtual asset or currency to query for. | No |

### RESPONSE

| Key                | Value                                  | Required |
| -----------------  | ----------------------------------- | -------- |
| Curpos           | **string**. The amount of that Virtual Asset or Viat in the users balance | - |
| Security          | **string**. The virtual asset or currency to query for. | - |


> Request

```python

   {
      "type":"querypos",
      "security":"LTC"
   }

```

> Response

```python

   {
      "result":"OK",
      "firm":"MIDC",
      "data": [
         {
            "curpos":"300",
            "rec_no":1,
            "security":"LTC",
            "type":"position"
         },
         {
            "curpos":"300",
            "rec_no":2,
            "security":"USD",
            "type":"position"
         }
      ],
      "userid":"JOHNUSER",
      "type":"querypos",
      "total_rec":2
   }
   or
   {
      "result":"no position",
      "type":"querypos"
   } 

```

# &nbsp;

# FIX API

## Overview
The information in MidChains FIX API specification describes the adaptation of the standard FIX 4.2 for vendors and subscribers to communicate with the MidChains quotation and execution platform. FIX 4.2 tags, as described in detail on the Financial Information Exchange Protocol Committee website, www.fixprotocol.org as well as custom tags are used extensively in this document and the reader should familiarize themselves with the latest updates to this release. If an application message in Financial Information Exchange Protocol version 4.2, or previous FIX versions, is not included in this document, the message is ignored.

## Connectivity
To connect to MidChains over th FIX API clients must establish a secure connection to the FIX gateway. The connectivity details such as the IP Address and Port Number will be assigned by the MidChains trade operations team. To request these details, clients can contact support@midchains.com or raise a request via the support portal through the www.midchains.com website.

# Message Types
## Application Messages – User to MidChains
### New Order – Single (MsgType = D)
### Order Cancel Request (MsgType = F)
### Order Cancel/Replace Request (MsgType = G)
## Application Messages – MidChains to User
### Execution Report (MsgType = 8)
### Order Cancel Reject (MsgType = 9)
## Administrative Messages
### Heartbeat (MsgType = 0)
### Logon (MsgType = A)
### Test Request (MsgType = 1)
### Resend Request (MsgType = 2)
### Reject (Msg Type = 3)
### Sequence Reset (MsgType = 4)

# Message Header
This section outlines the FIX messages, how they are supported, and to what extent the business data is translated within the FIX Gateway.

| Tag  | Field Name              | Required  | Note |
| ---- | ----------------------- | --------  | ---- |
| 8    | BeginString             | Yes       | FIX.4.2 |
| 9    | BodyLength              | Yes       |      |
| 35    | MsgType               | Yes       |      |
| 49    | SenderCompID              | Yes       | Provided by MidChains |
| 49    | TargetCompID              | Yes       | Provided by MidChains |
| 34    | MsgSeqNum             | Yes       |      |
| 52    | SendingTime              | Yes       |      |


# Message Trailer

| Tag  | Field Name              | Required  | Note |
| ---- | ----------------------- | --------  | ---- |
| 10    | CheckSum             | Yes       |  |


# Login (MsgType = A)
The logon message identifies and authenticates the user or member and establishes a connection to the FIX Gateway. 
The FIX gateway accepts Logon messages as per the FIX specification. Further, the FIX gateway supports the logon sequence required for session authentication. 
After a successful logon as described in the specification the FIX gateway will: 
Initiate retransmission processing via a resend request if the Logon sequence number is greater than the value expected
Initiate logout processing via a Logout message with an appropriate error message, then waits for a confirming Logout before disconnecting if the Logon sequence number is less than expected. If the confirming Logout has not been received within a short period of time the session will be disconnected.
Handle retransmission requests.
Initiate a Logon using the SenderCompID in the message header.
Will forwarded to the FIX client messages that are waiting in the outbound queue
Begin regular message communication.

| Tag  | Field Name              | Required  | Note |
| ---- | ----------------------- | --------  | ---- |
|     | Standard Header             | Yes       |  |
| 108    | HeartBtInt             | Yes       |  |
|     | Standard Trailer             | Yes       |  |

# Logout (MsgType = 5)
The Logout message initiates or confirms the termination of a FIX session. 

The FIX gateway will receive and generate logout messages as required by the FIX Protocol. The gateway follows the prescribed sequence of messages for the proper termination of the session. 
Messages received by the gateway after the client logs out are stored in a log file for transmission to the client once the client logs in again within the same trading day. The messages to be transmitted are dependent on the sequence number reconciliation that occurs on a logon handshake. 

Upon receipt of a Logout message: 
<br/>
1. A confirming logout message will be sent by the gateway to the client; then,
<br/>
2. The session will be disconnected. The FIX gateway should never initiate a logoff except when a severe error has occurred.

| Tag  | Field Name              | Required  | Note |
| ---- | ----------------------- | --------  | ---- |
|     | Standard Header             | Yes       |  |
| 58    | Text             | No       | Free format text string (Note: this field does not have a specified maximum length) If the Logout message has been sent by the the FIX gateway, then this field will contain the text “Session closed”. |
|     | Standard Trailer             | Yes       |  |


# New Order - Single (MsgType = D)
This message is used to submit an order to the trading system for processing.

| Tag  | Field Name              | Required  | Note |
| ---- | ----------------------- | --------  | ---- |
|     | Standard Header             | Yes       |  |
| 11    | ClOrdId             | Yes       | Unique identifier of the order. Must be unique for each session, max 32 chars. |
| 1    | Account             | No       | Optional identifier from customer, will be passed back in Execution Report. |
| 55    | Symbol             | Yes       | Common, “human understood” representation of the security, bitcoins US Dollar – BTCUSD, etc. |
| 54    | Side           | Yes       | '1' = Buy '2' = Sell |
| 38    | OrderQty           | Yes       | Size of the order. E.G. 10  |
| 43229 | FractionBase             | Yes       | If FractionBase is 100 and OrderQty field = 1, that means the Order Qty sent to the platform is 0.01. |
| 40    | OrdType             | Yes       | '1' = Market '2' = Limit |
| 44    | Price             | No       | Required for limit Ordtypes  |
| 59 | TimeInForce | No | '3' = IOC (Immediate-Or-Cancel) |
| 126 | ExpireTime | No | Required if TimeInForce = GTT (UTC)
|     | Standard Trailer             | Yes       |  |

# Order Cancel Request (MsgType = F)

| Tag  | Field Name              | Required  | Note |
| ---- | ----------------------- | --------  | ---- |
|     | Standard Header             | Yes       |  |
| 41    | OrigClOrdId             | Yes       | ClOrdID of the previous order |
| 37    | OrdId             | No       | Unique Identifier of order assigned by the platform. |
| 11    | ClOrdId             | Yes       | Unique identifier of the order. Must be unique for each session, max 32 chars. |
| 55    | Symbol             | Yes       | Common, “human understood” representation of the security, bitcoins US Dollar – BTCUSD, etc. |
| 54    | Side           | Yes       | '1' = Buy '2' = Sell |
| 38    | OrderQty           | Yes       | Size of the order. E.G. 10  |
| 58 | Text             | No       |  |
|     | Standard Trailer             | Yes       |  |


# Order Cancel/Replace Request (MsgType = G)

| Tag  | Field Name              | Required  | Note |
| ---- | ----------------------- | --------  | ---- |
|     | Standard Header             | Yes       |  |
| 41    | OrigClOrdId             | Yes       | ClOrdID of the previous order |
| 37    | OrdId             | No       | Unique Identifier of order assigned by the platform. |
| 11    | ClOrdId             | Yes       | Unique identifier of the order. Must be unique for each session, max 32 chars. |
| 1    | Account             | No       | Optional identifier from customer, will be passed back in Execution Report. |
| 55    | Symbol             | Yes       | Common, “human understood” representation of the security, bitcoins US Dollar – BTCUSD, etc. |
| 54    | Side           | Yes       | '1' = Buy '2' = Sell |
| 38    | OrderQty           | Yes       | Size of the order. E.G. 10  |
| 44    | Price             | No       | Required for limit Ordtypes  |
| 59 | TimeInForce | No | '3' = IOC (Immediate-Or-Cancel) |
| 126 | ExpireTime | No | Required if TimeInForce = GTT (UTC)
|     | Standard Trailer             | Yes       |  |

# &nbsp;