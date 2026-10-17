# MCP 工具调用要点与字段陷阱

> 服务端实时返回的工具描述与 input schema **优先于本文档**。参数有出入时以运行时 schema 为准。

## 服务概况

| 项 | 值 |
|---|---|
| 端点 | `https://mcp.mcd.cn` |
| 传输 | Streamable HTTP |
| 工具总数 | 33（本 Skill 实际调用 8 个，见 MCP_INTEGRATION.md） |
| 限流 | 每 Token 每分钟 600 次，超限返回 429 |
| 鉴权 | `Authorization: Bearer <MCD_MCP_TOKEN>` |
| 覆盖范围 | 中国大陆（不含港澳台） |

## 到店自取核心链路

```
query-nearby-stores → query-meals → calculate-price → create-order
```

### query-nearby-stores
查询可点餐门店，仅支持到店场景。

| 参数 | 必填 | 说明 |
|---|---|---|
| `searchType` | 是 | `1` 搜索收藏餐厅（默认），`2` 按位置搜索 |
| `city` | 条件必填 | `searchType=2` 时必填 |
| `keyword` | 条件必填 | `searchType=2` 时必填，位置关键词 |
| `beType` | 是 | `1` 到店自取 |

注意：用户没有收藏记录时 `searchType=1` 可能返回空，此时应引导用户提供位置改用 `searchType=2`。

### query-meals
查询门店可售餐品列表。

| 参数 | 必填 | 说明 |
|---|---|---|
| `storeCode` | 是 | 门店编码，来自上一步 |
| `beCode` | 是 | BE 编码。**到店自取场景传 `null`** |

返回可能是 toon 紧凑格式，形如 `{productName,price,code}: 后面跟逗号分隔的行`，而非标准 JSON。解析失败时按原文提取，不要猜测字段值。

### query-meal-detail
查询单个餐品详情，含套餐选配信息。`storeCode` 必填，到店场景 `beCode` 为 `null`。

### calculate-price
下单前**必须**调用。同时用于获取 `takeWayCode`。

| 参数 | 必填 | 说明 |
|---|---|---|
| `storeCode` | 是 | 门店编码 |
| `beCode` | 条件 | 到店场景为 `null` |
| `items` | 是 | `[{productCode, quantity}]` |
| `couponId` / `couponCode` | 否 | 使用优惠券时传 |

返回关键字段：

```json
{
  "data": {
    "originalPrice": 5000,
    "price": 4500,
    "deliveryPrice": 0,
    "packingPrice": 0,
    "discount": 500,
    "takeWayList": [
      { "code": "eat-in", "name": "堂食" },
      { "code": "locker-in", "name": "外带柜" }
    ]
  }
}
```

⚠️ **`takeWayCode` 只能取自 `takeWayList[].code`**，硬编码字符串会失败。

### create-order
创建订单。到店场景 `orderType=1`。

| 参数 | 必填 | 说明 |
|---|---|---|
| `storeCode` | 是 | 门店编码 |
| `orderType` | 是 | `1` 到店，`2` 外送 |
| `beType` | 条件 | 到店场景为 `1` |
| `beCode` | 条件 | 到店场景必须为 `null`；外送为地址的 beCode |
| `takeWayCode` | 条件 | **到店场景必传**，从算价返回取；外送必须不传 |
| `addressId` | 条件 | 外送场景必填 |
| `items` | 是 | `[{productCode, quantity, couponId?, couponCode?}]` |

建单后仅到「待支付」状态。**不要尝试代付、不要重复下单**，把支付链接交给用户。

## 优惠券工具

| 工具 | 用途 |
|---|---|
| `available-coupons` | 查询可领取的麦麦省优惠券 |
| `query-store-coupons` | 查询当前门店可用的优惠券 |
| `auto-bind-coupons` | 一键领取全部可领券（**写操作，需用户确认**） |

省钱的正确做法：先查可领券 → 提示用户领取 → 再算价，让优惠真实生效。不要承诺无法兑现的价格。

## 其他工具

| 工具 | 用途 |
|---|---|
| `campaign-calendar` | 当月营销活动日历 |
| `list-nutrition-foods` | 常见餐品营养成分 |
| `query-my-account` | 积分账户详情 |
| `query-order` | 订单状态与详情 |
| `delivery-query-addresses` | 配送地址列表 |
| `delivery-create-address` | 新增配送地址（**写操作，写入姓名手机号地址**） |

## 三个必踩的坑

| 坑 | 后果 | 正确做法 |
|---|---|---|
| 金额单位不统一 | 说「4150 元」错了 100 倍 | **仅** `calculate-price` 返回分，除 100；其余工具已是元 |
| 漏传 `takeWayCode` | 到店建单直接失败 | 先算价，从 `takeWayList` 取码再建单 |
| 外送传了 `takeWayCode` | 报错 | 外送场景（`orderType=2`）不能带此参数 |

## 错误处理

| 情况 | 处理方式 |
|---|---|
| 401 鉴权失败 | Token 过期，提示用户重新获取，不重试 |
| 429 限流 | 等待后重试，最多 1 次 |
| 菜单解析失败 | 原文提取，不编造商品编码 |
| 无可用门店 | 换城市或关键词重查一次，仍失败则告知用户 |
| 算价超出预算 | 换方案重新算价，不向用户报错 |

**编造数字是本 Skill 最严重的失败。** 查不到就说查不到，绝不用常识补数字。