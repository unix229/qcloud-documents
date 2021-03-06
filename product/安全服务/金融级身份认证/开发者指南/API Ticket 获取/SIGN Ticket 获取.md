### 注意事项
- **前置条件**：确保 Access Token 已经正常获取，获取方式见 [Access Token 获取](https://cloud.tencent.com/document/product/655/13813)。
- SIGN ticket 主要用于合作方**后台服务端业务请求**生成签名鉴权参数之一，用于后台查询验证结果、调用其他业务服务等。
- API ticket 为 SIGN 类型，有效期为最长为 3600S，此处 API ticket 的必须缓存在磁盘，并定时刷新，刷新的机制如下：
 - 由于 API ticket 的生命周期依赖于 Access Token。最长为 3600S，故为了简单方便，建议 API ticket 的刷新机制与 Access Token 定时机制原理一致，严格按照每50分钟请求新的 API ticket，原 API ticket 1 小时 (3600S) 失效，期间两个 API ticket 都能使用。
 - 在获取新的 API ticket，请注意返回的 expire_in 为此 ticket 的最大生存周期，最大为 3600S，具体以实际返回为准，如果 expire_in 大于 600，那么下次刷新时间为 expire_in – 600S；如果返回的 expire_in 小于等于 600，那么下次取 ticket 时需要立刻刷新。

### 获取 SIGN ticket
请求 URL: 
```
https://idasc.webank.com/api/oauth2/api_ticket
```
请求方法：GET
请求参数：

| 参数           | 说明                                       | 类型   | 长度     | 是否必填                                     |
| ------------ | ---------------------------------------- | ---- | ------ | ---------------------------------------- |
| app_id       | 腾讯服务分配的 App ID                           | 字符串  | 腾讯服务分配 | 必填 ，腾讯服务分配的 App ID                       |
| access_token | 根据 [Access Token 获取](https://cloud.tencent.com/document/product/655/13813) 页面中的指引进行获取 | 字符串  | 腾讯服务分配 | 必填，根据 [Access Token 获取](https://cloud.tencent.com/document/product/655/13813) 页面中的指引进行获取 |
| type         | ticket 类型                                | 字符串  | 20     | 必填 ，默认值：**SIGN** (必须大写)                  |
| version      | 版本号                                      | 字符串  | 20     | 必填 ，默认值：1.0.0                            |

请求示例：
```
https://idasc.webank.com/api/oauth2/api_ticket?app_id=xxx&access_token=xxx&type=SIGN&version=1.0.0
```
响应：
```
{
"code":"0",
"msg":"请求成功",
 "transactionTime":"20151022044027", 
 "tickets":[
{"value":"ticket_string",
"expire_in":"3600"，
"expire_time":"20151022044027"}
]
 ｝
```

>**注意**:
> - code 不是 0 是表示获取失败，可以根据 code 和 msg 字段定位和调试。
> - expire_in 为 access_token 的最大生存时间，单位秒，合作伙伴在判定有效期时以此为准。
> - expire_time 为 access_token 失效的绝对时间，由于各服务器时间差异，不能使用作为有效期的判定依据，只展示使用。
> - access_token 失效时，该 access_token 生成的 ticket 都失效
