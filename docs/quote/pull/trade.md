---
id: quote_trade
title: 获取标的成交明细
slug: trade
sidebar_position: 8
---

该接口用于获取标的的成交明细数据。

:::info

[业务指令](../../socket/protocol/request)：`17`

:::

## Request

### Parameters

| Name   | Type   | Required | Description                                                              |
| ------ | ------ | -------- | ------------------------------------------------------------------------ |
| symbol | string | 是       | 标的代码，使用 `ticker.region` 格式，例如：`700.HK`                      |
| count  | int32  | 是       | 请求的逐笔明细数量 <br /><br />**校验规则：**<br />请求数量最大为 `1000` |

### Protobuf

```protobuf
message SecurityTradeRequest {
  string symbol = 1;
  int32 count = 2;
}
```

## Response

### Response Properties

| Name            | Type     | Description                                                                                                                                                                                           |
| --------------- | -------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| symbol          | string   | 标的代码                                                                                                                                                                                              |
| trades          | object[] | 逐笔明细数据                                                                                                                                                                                          |
| ∟ price         | string   | 价格                                                                                                                                                                                                  |
| ∟ volume        | int64    | 成交量                                                                                                                                                                                                |
| ∟ timestamp     | int64    | 成交时间                                                                                                                                                                                              |
| ∟ trade_type    | string   | 交易类型 <br /><br />**可选值：**<br />`*` - 场外交易<br />`D` - 碎股交易<br />`M` - 非自动对盘<br />`P` - 开市前成交盘<br />`U` - 竞价交易<br />`X` - 同一券商非自动对盘<br />`Y` - 同一券商自动对盘 |
| ∟ direction     | int32    | 交易方向 <br /><br />**可选值：**<br />`0` - nature<br />`1` - down<br />`2` - up                                                                                                                     |
| ∟ trade_session | int32    | 交易时段，详见 [TradeSession](../objects#tradesession---交易时段)                                                                                                                                     |

### Protobuf

```protobuf
message SecurityTradeResponse {
  string symbol = 1;
  repeated Trade trades = 2;
}

message Trade {
  string price = 1;
  int64 volume = 2;
  int64 timestamp = 3;
  string trade_type = 4;
  int32 direction = 5;
  TradeSession trade_session = 6;
}
```

### Response JSON Example

```json
{
  "symbol": "AAPL.US",
  "trades": [
    {
      "price": "158.760",
      "volume": 1,
      "timestamp": 1651103979,
      "trade_type": "I",
      "trade_session": 2
    },
    {
      "price": "158.745",
      "volume": 1,
      "timestamp": 1651103985,
      "trade_type": "I",
      "trade_session": 2
    },
    {
      "price": "158.800",
      "volume": 1,
      "timestamp": 1651103995,
      "trade_type": "I",
      "trade_session": 2
    }
  ]
}
```

## 错误码

| 协议错误码 | 业务错误码 | 描述           | 排查建议                         |
| ---------- | ---------- | -------------- | -------------------------------- |
| 3          | 301600     | 无效的请求     | 请求参数有误或解包失败           |
| 3          | 301606     | 限流           | 降低请求频次                     |
| 7          | 301602     | 服务端内部错误 | 请重试或联系技术人员处理         |
| 7          | 301600     | 请求标的不存在 | 检查请求的 `symbol` 是否正确     |
| 7          | 301603     | 标的无行情     | 标的没有请求的行情数据           |
| 7          | 301604     | 无权限         | 没有获取标的行情的权限           |
| 7          | 301607     | 接口限制       | 请求的数据数量超限，减少数据数量 |
