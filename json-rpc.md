
# RELAYER API (JSON-RPC & WEBSOCKET)
`LOOPRING RELAYER` is a relay helping to communicate with eth network and apply curd operations for the loopring order. This document define the json-rpc & websocket api for communicating with replay backend.

This document contains the following sections:
- Endport
- JSON-RPC Methods
- Websocket API
- Common Define(object, enum, etc..)
- Error Code

## Endport
```
JSON-RPC  : http://relay.loopring.org
Websocket : wss://relay.loopring.org/ws
```

## JSON-RPC Methods 

* [eth_gasPrice](https://github.com/ethereum/wiki/wiki/JSON-RPC#eth_gasprice)
* [eth_blockNumber](https://github.com/ethereum/wiki/wiki/JSON-RPC#eth_blocknumber)
* [eth_getBalance](https://github.com/ethereum/wiki/wiki/JSON-RPC#eth_getbalance)
* [eth_getTransactionCount](https://github.com/ethereum/wiki/wiki/JSON-RPC#eth_gettransactioncount)
* [eth_sendRawTransaction](https://github.com/ethereum/wiki/wiki/JSON-RPC#eth_sendrawtransaction)
* [eth_call](https://github.com/ethereum/wiki/wiki/JSON-RPC#eth_call)
* [eth_estimateGas](https://github.com/ethereum/wiki/wiki/JSON-RPC#eth_estimategas)
* [eth_getTransactionByHash](https://github.com/ethereum/wiki/wiki/JSON-RPC#eth_getTransactionByHash)
* [eth_setTokenAllowance](#eth_setTokenAllowance)
* [loopring_submitOrder](#loopring_submitOrder)
* [loopring_cancelOrder](#loopring_cancelOrder)
* [loopring_getOrderByHash](#loopring_getOrderByHash)
* [loopring_getOrdersByAddress](#loopring_getOrdersByAddress)
* [loopring_getDepth](#loopring_getDepth)
* [loopring_ticker](#loopring_ticker)
* [loopring_getDealHistory](#loopring_getDealHistory)
* [loopring_getCandleTicks](#loopring_getCandleTicks)

## Websocket APIs



## JSON RPC API Reference

***

#### eth_setTokenAllowance

Change token allowance quantity. 
This method extendes method `approve` in `ERC20` contract to support changing amount to positive integer while previous amount is also a positive integer.

##### Parameters

1. `String` - The first raw transaction.
2. `String` - The second raw transaction. if you want changing allowance in this conditon: `a -> b where a > 0 and b > 0`, this param must be applied.

```js
params: [
  "0xa3b225d374f2b47254eb970870f07244567435058bb8eb3ab970870f4d072445642e7522acdb429064", // required
  "0xd46e8dd67c5d32be8d46e8dd67c5d32be8058bb8eb970870f072445675058bb8eb970870f072445675" // optional
]
```

##### Returns

`String` - content like `SUBMIT_SUCCESS` for async request.

##### Example
```js
// Request
curl -X POST --data '{"jsonrpc":"2.0","method":"eth_setTokenAllowance","params":["0xa3b225d374f2b47254eb970870f07244567435058bb8eb3ab970870f4d072445642e7522acdb429064", "0xd46e8dd67c5d32be8d46e8dd67c5d32be8058bb8eb970870f072445675058bb8eb970870f072445675"],"id":67}'
 
// Result
{
  "id":67,
  "jsonrpc":"2.0",
  "result": "SUBMIT_SUCCESS"
}
```

***

#### loopring_submitOrder

Submit loopring order.

##### Parameters

`JSON Object` - The order object(refer to [LoopringProtocol](https://github.com/Loopring/protocol/blob/master/contracts/LoopringProtocol.sol))
  - `address` - Order submit address
  - `tokenS` - Token to sell.
  - `tokenB` - Token to buy.
  - `amountS` - Maximum amount of tokenS to sell.
  - `amountB` - Minimum amount of tokenB to buy if all amountS sold.
  - `expiration` - Indicating when this order will expire.
  - `rand` - A random number to make this order's hash unique.
  - `lrcFee` - Max amount of LRC to pay for miner. The real amount to pay is proportional to fill amount.
  - `buyNoMoreThanAmountB` - If true, this order does not accept buying more than `amountB`.
  - `savingSharePercentage` - The percentage of savings paid to miner.
  - `v` - ECDSA signature parameter v.
  - `r` - ECDSA signature parameter r.
  - `s` - ECDSA signature parameter s.

```js
params: {
  "address" : "0x847983c3a34afa192cfee860698584c030f4c9db1",
  "tokenS" : "Eth",
  "tokenB" : "Lrc",
  "amountS" : 100.3,
  "amountB" : 3838434,
  "expiration" 1406014710,
  "rand" : 3848348,
  "lrcFee" : 20,
  "buyNoMoreThanAmountB" : true,
  "savingSharePercentage" : 50, // 0~100
  "v" : 112,
  "r" : "239dskjfsn23ck34323434md93jchek3",
  "s" : "dsfsdf234ccvcbdsfsdf23438cjdkldy",
}
```

##### Returns

`String` - The order hash.

##### Example
```js
// Request
curl -X POST --data '{"jsonrpc":"2.0","method":"loopring_submitOrder","params":{see above},"id":64}'

// Result
{
  "id":64,
  "jsonrpc": "2.0",
  "result": "0x47173285a8d7341e5e972fc677286384f802f8ef42a5ec5f03bbfa254cb01fad"
}
```

***

#### loopring_cancelOrder

Cancel loopring order.

##### Parameters

`JSON Object` - include order hash and signature params
  - `orderHash` - The order hash.
  - `v` - ECDSA signature parameter v.
  - `r` - ECDSA signature parameter r.
  - `s` - ECDSA signature parameter s.

```js
params: {
  "orderHash" : "0x47173285a8d7341e5e972fc677286384f802f8ef42a5ec5f03bbfa254cb01fad",
  "v" : 112,
  "r" : "239dskjfsn23ck34323434md93jchek3",
  "s" : "dsfsdf234ccvcbdsfsdf23438cjdkldy",
}
```

##### Returns

`String` - content like `SUBMIT_SUCCESS` for async request.

##### Example
```js
// Request
curl -X POST --data '{"jsonrpc":"2.0","method":"loopring_cancelOrder","params":{see above},"id":64}'

// Result
{
  "id":64,
  "jsonrpc": "2.0",
  "result": "SUBMIT_SUCCESS"
}
```

***

#### loopring_getOrderByHash

Get loopring order detail info by order hash.

##### Parameters

`String` - The order hash

```js
params: ["0x47173285a8d7341e5e972fc677286384f802f8ef42a5ec5f03bbfa254cb01fad"]
```

##### Returns

1. `JSON Object` - The original order info when submitting.(refer to [LoopringProtocol](https://github.com/Loopring/protocol/blob/master/contracts/LoopringProtocol.sol))
  - `address` - Order submit address
  - `tokenS` - Token to sell.
  - `tokenB` - Token to buy.
  - `amountS` - Maximum amount of tokenS to sell.
  - `amountB` - Minimum amount of tokenB to buy if all amountS sold.
  - `expiration` - Indicating when this order will expire.
  - `rand` - A random number to make this order's hash unique.
  - `lrcFee` - Max amount of LRC to pay for miner. The real amount to pay is proportional to fill amount.
  - `buyNoMoreThanAmountB` - If true, this order does not accept buying more than `amountB`.
  - `savingSharePercentage` - The percentage of savings paid to miner.
  - `v` - ECDSA signature parameter v.
  - `r` - ECDSA signature parameter r.
  - `s` - ECDSA signature parameter s.
  - `ts` - The submit TimeStamp.

2. `status` `STRING` - Order status. refer to `Order Status Set` (include Pending, PartiallyExecuted, FullyExecuted, Cancelled)
3. `totalDealedAmountS` - The total amount of TokenS that have been selled. 
4. `totalDealedAmountB` - The total amount of TokenB that have been buyed.
5. `matchList` -  The match records related to this order.

##### Example
```js
// Request
curl -X POST --data '{"jsonrpc":"2.0","method":"loopring_getOrderByHash","params":{see above},"id":64}'

// Result
{
  "id":64,
  "jsonrpc": "2.0",
  "result": {
    "orginalOrder" : {
      "address" : "0x847983c3a34afa192cfee860698584c030f4c9db1",
      "tokenS" : "Eth",
      "tokenB" : "Lrc",
      "amountS" : 100.3,
      "amountB" : 3838434,
      "expiration" : "2017-11-11 19:00:01",
      "rand" : 3848348,
      "lrcFee" : 20,
      "buyNoMoreThanAmountB" : true,
      "savingSharePercentage" : 50, // 0~100
      "v" : 112,
      "r" : "239dskjfsn23ck34323434md93jchek3",
      "s" : "dsfsdf234ccvcbdsfsdf23438cjdkldy",
      "ts" : 1506014710000
    },
    "status" : "PartiallyExecuted",
    "totalDealedAmountS" : 30,
    "totalDealedAmountB" : 29333.21,
    "matchList" : {
      "total" : 301,
      "pageIndex" : 2,
      "pageSize" : 20
      "data" : [
        {
          "ts" : "1506014710000",
          "amountS" : 30.31,
          "amountB" : 3934.111,
          "txHash" : "0x1eb8d538bb9727028912f57c54776d90c1927e3b49f34a2e53e9271949ec044c"
        },
        {
          "ts" : "1506014710323",
          "amountS" : 30.31,
          "amountB" : 3934.111,
          "txHash" : "0x1eb8d538bb9727028912f57c54776d90c1927e3b49f34a2e53e9271949ec044c"
        }
      ]
    }
  }
}
```

***

#### loopring_getOrdersByAddress

Get loopring order list by address.

##### Parameters

`String` - The address

```js
params: ["0x847983c3a34afa192cfee860698584c030f4c9db1"]
```

##### Returns

`PageResult of Order` - Order list with page info

1. `data` `LoopringOrder` - The original order info when submitting.(refer to [LoopringProtocol](https://github.com/Loopring/protocol/blob/master/contracts/LoopringProtocol.sol))
  - `address` - Order submit address
  - `tokenS` - Token to sell.
  - `tokenB` - Token to buy.
  - `amountS` - Maximum amount of tokenS to sell.
  - `amountB` - Minimum amount of tokenB to buy if all amountS sold.
  - `expiration` - Indicating when this order will expire.
  - `rand` - A random number to make this order's hash unique.
  - `lrcFee` - Max amount of LRC to pay for miner. The real amount to pay is proportional to fill amount.
  - `buyNoMoreThanAmountB` - If true, this order does not accept buying more than `amountB`.
  - `savingSharePercentage` - The percentage of savings paid to miner.
  - `v` - ECDSA signature parameter v.
  - `r` - ECDSA signature parameter r.
  - `s` - ECDSA signature parameter s.
  - `ts` - The submit TimeStamp.

2. `total` - Total amount of orders.
3. `pageIndex` - Index of page.
4. `pageSize` - Amount per page.

##### Example
```js
// Request
curl -X POST --data '{"jsonrpc":"2.0","method":"loopring_getOrderByHash","params":{see above},"id":64}'

// Result
{
  "id":64,
  "jsonrpc": "2.0",
  "result": {
    "data" : [
      "orginalOrder" : {
        "address" : "0x847983c3a34afa192cfee860698584c030f4c9db1",
        "tokenS" : "Eth",
        "tokenB" : "Lrc",
        "amountS" : 100.3,
        "amountB" : 3838434,
        "expiration" : "2017-11-11 19:00:01",
        "rand" : 3848348,
        "lrcFee" : 20,
        "buyNoMoreThanAmountB" : true,
        "savingSharePercentage" : 50, // 0~100
        "v" : 112,
        "r" : "239dskjfsn23ck34323434md93jchek3",
        "s" : "dsfsdf234ccvcbdsfsdf23438cjdkldy"
      },
      "status" : "PartiallyExecuted",
      "totalDealedAmountS" : 30,
      "totalDealedAmountB" : 29333.21,
      "matchList" : {
        "total" : 301,
        "pageIndex" : 2,
        "pageSize" : 20
        "data" : [
          {
            "ts" : "1506014710000",
            "amountS" : 30.31,
            "amountB" : 3934.111,
            "txHash" : "0x1eb8d538bb9727028912f57c54776d90c1927e3b49f34a2e53e9271949ec044c"
          },
          {
            "ts" : "1506014710323",
            "amountS" : 30.31,
            "amountB" : 3934.111,
            "txHash" : "0x1eb8d538bb9727028912f57c54776d90c1927e3b49f34a2e53e9271949ec044c"
          }
        ]
      },
      {}....
    ]
    "total" : 12,
    "pageIndex" : 1,
    "pageSize" : 10
  }
}
```

***

#### loopring_getDepth

Get depth and accuracy by token pair

##### Parameters

1. `from` - The token to sell
2. `to` - The token to buy
3. `length` - The length of the depth data. defalut is 50.


```js
params: {
  "from" : "Eth",
  "to" : "Lrc",
  "length" : 10 // defalut is 50
}
```

##### Returns

1. `depth` - The depth data.
2. `accuracies` - The accuracies, it's a array of number.

##### Example
```js
// Request
curl -X POST --data '{"jsonrpc":"2.0","method":"loopring_getDepth","params":{see above},"id":64}'

// Result
{
  "id":64,
  "jsonrpc": "2.0",
  "result": {
    "depth" : {
      "buy" : [
        [200.1, 10.3], [199.8, 2], [198.3, 23]
      ],
      "sell" : [
        [205.1, 13], [211.8, 0.5], [321.3, 33]
      ]
    },
    "accuracies" : [0.01, 0.05, 0.1, 0.5]
  }
}
```

***


#### loopring_ticker

Get 24hr merged ticker info from loopring relayer.

##### Parameters

1. `from` - The token to sell
2. `to` - The token to buy

```js
params: {
  "from" : "Eth",
  "to" : "Lrc"
}
```

##### Returns

1. `high`
2. `low`
3. `last`
4. `vol`
5. `buy`
6. `sell`
7. `ts` - Timestamp.

##### Example
```js
// Request
curl -X GET --data '{"jsonrpc":"2.0","method":"loopring_ticker","params":{see above},"id":64}'

// Result
{
  "id":64,
  "jsonrpc": "2.0",
  "result": {
    "high" : 30384.2,
    "low" : 19283.2,
    "last" : 28002.2,
    "vol" : 1038,
    "buy" : 122321,
    "sell" : 12388,
    "ts" : 1506014710000
  }
}
```

***

#### loopring_getDealHistory

Get 24hr merged ticker info from loopring relayer.

##### Parameters

1. `from` - The token to sell
2. `to` - The token to buy
3. `address`
4. `pageIndex`
5. `pageSize`

```js
params: {
  "from" : "Eth",
  "to" : "Lrc"
  "address" : "0x8888f1f195afa192cfee860698584c030f4c9db1",
  "pageIndex" : 1,
  "pageSize" : 20 // max size is 50.
}
```

##### Returns

`PAGE RESULT of OBJECT`
1. `ARRAY OF DATA` - The match histories.
  - `txHash` - The transaction hash of the match.
  - `dealAmountS` - Amount of sell.
  - `dealAmountB` - Amount of buy.
  - `ts` - The timestamp of matching time.
  - `relatedOrderHash` - The order hash.
2. `pageIndex`
3. `pageSize`
4. `total`

##### Example
```js
// Request
curl -X GET --data '{"jsonrpc":"2.0","method":"loopring_getDealHistory","params":{see above},"id":64}'

// Result
{
  "id":64,
  "jsonrpc": "2.0",
  "result": {
    "data" : [
      {
        "txHash" : "0xb903239f8543d04b5dc1ba6579132b143087c68db1b2168786408fcbce568238",
        "dealAmountS" : 20,
        "dealAmountB" : 30.21,
        "ts" : 1506014710000
      }
    ],
    "pageIndex" : 1,
    "pageSize" : 20,
    "total" : 212
  }
}
```

***

#### loopring_getCandleTicks

Get tick infos for kline.

##### Parameters

1. `from` - The token to sell
2. `to` - The token to buy
3. `interval` - The interval of kline. enum like: 1m, 5m, 6h, 1d....
4. `size` - The data size.

```js
params: {
  "from" : "Eth",
  "to" : "Lrc"
  "address" : "0x8888f1f195afa192cfee860698584c030f4c9db1",
  "pageIndex" : 1,
  "pageSize" : 20 // max size is 50.
}
```

##### Returns

`ARRAY of JSON OBJECT`
  - `dealAmountS` - Total amount of sell.
  - `dealAmountB` - Total amount of buy.
  - `ts` - The timestamp of matching time.
  - `open` - The opening price.
  - `close` - The closing price.
  - `high` - The highest price in interval.
  - `low` - The lowest price in interval.


##### Example
```js
// Request
curl -X GET --data '{"jsonrpc":"2.0","method":"loopring_getCandleTicks","params":{see above},"id":64}'

// Result
{
  "id":64,
  "jsonrpc": "2.0",
  "result": {
    "data" : [
      {
        "dealAmountS" : 20,
        "dealAmountB" : 30.21,
        "ts" : 1506014710000
        "open" : 3232.1,
        "close" : 2321,
        "high" : 1231.2,
        "low" : 1234.2
      }
    ]
  }
}
```

***
