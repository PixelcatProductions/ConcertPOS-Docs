# Introduction

Hello there! This developer documentation is showing you how to use our powerful WebSocket API to integrate ConcertPOS with custom clients, bots or your own API.

## Bugs

Bugs can be reported at the [Github Repository](https://github.com/PixelcatProductions/ConcertPOS-Docs) of these docs, [Pull Requests](https://github.com/PixelcatProductions/ConcertPOS-Docs/pulls?q=is%3Apr+is%3Aopen+sort%3Aupdated-desc) or [New issues](https://github.com/PixelcatProductions/ConcertPOS-Docs/issues?q=is%3Aissue+is%3Aopen+sort%3Aupdated-desc) are welcome!

## Clients

A client for ConcertPOS is the heart of our Software, if you believe the original client is not good enough, feel free to develop a custom one!

# Resources

This page displays all our WebSocket objects!

## Websockets

Our API is entirely handled by websockets, which means a constant connection has to be established in order to fully integrate ConcertPOS.

### Shards

We handle server load by sharding our backend into several servers. Manually you can connect to one of the shards and all events will be distributed to all other shards.

Available shards: `exon,cyton,zeus`

### Connect

Connect to one of the gateways by using the connection url with the shard as a prefix


`wss://shard-{Shard}.ws.concertpos.com/INSTANCE_SLUG` 

e.g.

`wss://shard-exon.ws.concertpos.com/INSTANCE_SLUG`

Where `INSTANCE_SLUG` is the slug of your ConcertPOS instance


## Send Commands

Sending to the Gateway is relatively easy. All requests should be json encoded with the following template.

```json
{
	"command": "YOUR_COMMAND",
	"parameters": {
		"api_key": "YOUR_API_KEY"
	}
}
```


### Models

We use several models for sending commands to simplify the processing and understanding.

#### Product 

```json
{
	"ID": 1,
	"Amount": 1
}
```

All requests should contain the api_key parameter, else your request will be blocked.


## Receive/Handle Data

Handling data is as easy as sending ones, the result will always be in JSON and contains a few model with their corresponding templates below.

### Command Model

This model is currently unused

```json
{
	"type": "command",
	"command": "some_server_response",
	"result": "some_result"
}
```

### Event Model

This model is used as callbacks for your requests,

```json
{
	"type": "event",
	"result": {
		"event": "some_event",
		"parameters": {}
	}
}
```

### Error Model

This model is used to indicate errors

```json
{
	"type": "error",
	"result": {
		"error": "some_error",
		"code": 0
	}
}
```

### Broadcast Model

This model is used as a method for admins or Pixelcat Productions Employees to broadcast messages

```json
{
	"type": "broadcast",
	"result": {
		"message": "some_message",
		"parameters": {}
	}
}
```

#### updatepos

This broadcast is used to indicate that the POS should be updated, either because prices, payment methods or products have been updated.

```json
{
	"type": "broadcast",
	"result": {
		"message": "updatepos",
		"parameters": {}
	}
}
```

## Commands

Commands are the way to communicate with the gateway. Without them no changes to your instance can be made.


### getconfig

!> Rate Limit Properties: +25/100 [per request] [30 seconds]

With `getconfig` you can retrieve all they key/value configuration of your instance.

```json
{
	"command": "getconfig",
	"parameters": {
		"api_key": "YOUR_API_KEY"
	}
}
```


### getdeposit

!> Rate Limit Properties: +10/100 [per request] [30 seconds]

With `getdeposit` you can retrieve the price for your deposit product.

```json
{
	"command": "getdeposit",
	"parameters": {
		"api_key": "YOUR_API_KEY"
	}
}
```



### getlicense

!> Rate Limit Properties: +4/100 [per request] [30 seconds]

With `getlicense` you retrieve latest licensing info from your instance using the K/V Config license_key.


!> This command is heavily rate limited and should be called once on application startup and be cached!

```json
{
	"command": "getlicense",
	"parameters": {
		"api_key": "YOUR_API_KEY"
	}
}
```

### listpm

!> Rate Limit Properties: +20/100 [per request] [30 seconds]

With `listpm` you retrieve all Payment Methods for the (`POSID`) Point of sale.


!> This command contains required parameters: `POSID`

```json
{
	"command": "listpm",
	"parameters": {
		"api_key": "YOUR_API_KEY",
		"POSID": 1
	}
}
```

### listproducts

!> Rate Limit Properties: +25/100 [per request] [30 seconds]

With `listproducts` you retrieve all assigned products for the (`POSID`) Point of sale.


!> This command contains required parameters: `POSID`

```json
{
	"command": "listproducts",
	"parameters": {
		"api_key": "YOUR_API_KEY",
		"POSID": 1
	}
}
```

### payitems

!> Rate Limit Properties: +1/100 [per request] [30 seconds]

With `payitems` you create new transactions for each item in `Products` using the Payment Method `PaymentMethod` in `POSID`


!> This command contains required parameters: `POSID`,`Products` (array),`PaymentMethod`

!> You can supply all products of the POS as only Products with the Amount higher than 0 are processed.
For more infos about the Product model, please refer to [this page](#product).

```json
{
	"command": "payitems",
	"parameters": {
		"api_key": "YOUR_API_KEY",
		"POSID": 1,
		"PaymentMethod": 1,
		"Products": [{
			"ID": 1,
			"Amount": 1
		}]
	}
}
```


### registerdevice

!> Rate Limit Properties: +10/100 [per request] [30 seconds]

With `registerdevice` you register a new device or updates the last ping with the designated `UUID`.

!> This command contains required parameters: `UUID`


```json
{
	"command": "registerdevice",
	"parameters": {
		"api_key": "YOUR_API_KEY",
		"UUID": "13e12ff5-c853-4049-acbc-b1307c00691d"
	}
}
```

### version

!> Rate Limit Properties: +50/100 [per request] [30 seconds]

With `version` you get the latest version string of the server.



Request:

```json
{
	"command": "version",
	"parameters": {
		"api_key": "YOUR_API_KEY",
	}
}
```

Response:

```json
{
	"type": "event",
	"result": {
		"event": "version",
		"parameters": {
			"Version": "1.0.1"
		}
	}
}
```


### getstatus


!> Rate Limit Properties: +15/100 [per request] [30 seconds]

With `getstatus` you pull the latest status info from https://status.pixelcatproductions.net/api/v1/status



Request:

```json
{
	"command": "getstatus",
	"parameters": {
		"api_key": "YOUR_API_KEY",
	}
}
```

Response:

```json
{
	"type": "event",
	"result": {
		"event": "getstatus",
		"parameters": {
			"status": "success",
			"message": "All systems are operational"
		}
	}
}
```


### listtransactions

!> Rate Limit Properties: +2/100 [per request] [30 seconds]

With `listtransactions` you retrieve all transactions of the `POSID`.

!> This command contains required parameters: `POSID`

?> Some parameters are optional: `Limit` if not specified, all transactions will be displayed by default


Request:
```json
{
	"command": "listtransactions",
	"parameters": {
		"api_key": "YOUR_API_KEY",
		"Limit": 50,
		"POSID": 1
	}
}
```

Response:

```json
{
	"type": "event",
	"result": {
		"event": "listtransactions",
		"parameters": {
			"Count": 4,
			"Transactions": [{
				"ID": 9215,
				"Payment_ID": 1,
				"PointOfSaleID": 1,
				"Product_ID": 1,
				"AmountProduct": 1,
				"AmountMoney": 18,
				"timestamp": "2020-05-13T15:27:02.000Z",
				"hash": "2ac3078aabce35508db346eb699b8688d6c59a376c46648d3b5108a05294b1f0ca274ecd01e63a09356afc5670751e585beb48823ef1eca6a7897b2455d218c0",
				"Note": null
			}, {
				"ID": 9195,
				"Payment_ID": 1,
				"PointOfSaleID": 1,
				"Product_ID": 15,
				"AmountProduct": 1,
				"AmountMoney": -1,
				"timestamp": "2020-05-09T15:46:47.000Z",
				"hash": "075175167e14f3ea94108fdf903b5b8cd6444027b40cd28ecabec958a548fb5c5f59a305868520a7b07074ca39f8f49a5f2fac40d6fe03e5ed23afdab6292bb3",
				"Note": null
			}, {
				"ID": 9194,
				"Payment_ID": 1,
				"PointOfSaleID": 1,
				"Product_ID": 36,
				"AmountProduct": 1,
				"AmountMoney": 10,
				"timestamp": "2020-05-09T15:46:47.000Z",
				"hash": "e73f7df42de8218bd00f3df7fb99d90d38beaaf31f89fff4c8819804239a2b09d883ecbd325f846b960afbfdf66436f45722bb5acb10c21472d6764be393ae54",
				"Note": null
			}, {
				"ID": 9188,
				"Payment_ID": 1,
				"PointOfSaleID": 1,
				"Product_ID": 1,
				"AmountProduct": 5,
				"AmountMoney": 90,
				"timestamp": "2020-05-08T23:17:07.000Z",
				"hash": "242c3d6372b415e6c6b35e1c31600297e9adb8990b48c45c6e6446d4629f4482231a4b6a57160ee6da9c3be0b66eaef02fbc158d7e8fedffaf678e2afee494b0",
				"Note": null
			}]
		}
	}
}
```
