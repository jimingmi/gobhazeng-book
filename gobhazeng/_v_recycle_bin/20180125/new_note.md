# 钱包-买基金

## 购买初始化

**Method:** GET

**URL:** https://trade.5ifund.com:9445/trade/pc_buyInitOne.action

**Req:**

```json
fundCode    基金代码
```

**Resp:** 新增钱包信息

```json
singleData:{
    moneytostockTzeroFlag
    moneytostockfeeratioTzero       交易费率
    tzeroFundCode                   超级基金代码
    fundtzeroList                   结果集List
}
```

### Demo

**URL:** https://trade.5ifund.com:9445/trade/pc_buyInitOne.action?fundCode=000697

**Resp:**

```json
"singleData": {
    "moneytostockTzeroFlag": "1",
    "fundtzeroList": [
        {
            "availableVol": "873025.00",            可用余额
            "bankAccount": "6230204177411444",      银行帐号
            "bankFundNo": "307600100007728",        用户在代销机构的基金帐号
            "bankName": "工商银行",                  银行名称
            "disCount": "0.0500",
            "fundCode": "000773",                   超级基金代码
            "fundName": "万家现金宝",                超级基金名称
            "isrealtime_flag": null,
            "minAccountBalance": "0.01",
            "nd_tradeFeeRatio": "0.0150",
            "spUser": "00999000001002086",
            "supportShareType": "0",
            "tradeFeeRatio": "0.0008",              交易费率
            "transactionAccountId": "600100007728", 交易账号
            "usable_flag": null
        }
    ],
    "tzeroFundCode": "000773",
    "moneytostockfeeratioTzero": 0.0008
｝
```

## 转购

**Method:** POST

**URL:** https://trade.5ifund.com:9445/trade/pc_sendZG.action

### Demo

**URL:** https://trade.5ifund.com:9445/trade/pc_sendZG.action

**Req:**

```json
{
    "rsZcDto": {
        "codeOfTargetFund": "000697",               基金代码
        "targetShareType": "0",                     份额类型
        "fundName": "汇添富移动互联股票",             基金名称
        "taCode": "47",                             TA代码
        "transActionAccountId": "600100006919",     交易账号
        "tradePassword": "123456a",                 交易密码
        "totalUsableVolText": 100,
        "userName": "100000000002",                 客户号
        "fundCode": "000773",                       钱包基金代码
        "fundType": "3",                            钱包基金类型
        "shareType": "0",                           钱包份额类型
        "operator": "124",                          来源
        "bankFundNo": "307600100006919",            用户在代销机构的基金帐号
        "spUser": "00999000001002296"
    }
}
```

**Resp:**

```json
{
    "code": "0000",
    "custId": null,
    "listData": null,
    "message": "万家T+0转出成功",
    "singleData": "00000000000100250170",
    "url": null
}
```

# 钱包-买收益宝

同买基金

# 钱包-买理财宝

同买基金

# 撤单

## 钱包购买-撤回到钱包

**Method:** POST

**URL:** https://trade.5ifund.com:9445/trade/pc_sendZGCD.action

### Demo

**URL:** https://trade.5ifund.com:9445/trade/pc_sendZGCD.action

**Req:**

```json
{
    "rsCdDto": {
        "transActionAccountId": "600100006919",
        "capitalMethod": "1",
        "tradePassword": "123456a",
        "revokeAppSheetNo": "00000000000100250271",
        "businessCode": "022",
        "checkFlag": "0",
        "operator": "124"
    }
}
```

**Resp:**

```json
{
    "code": "0000",
    "custId": null,
    "listData": null,
    "message": "万家T+0撤单成功",
    "singleData": null,
    "url": null
}
```

## 银行卡购买-撤回到钱包

**Method:** POST

**URL:** https://trade.5ifund.com:9445/trade/pc_revokeToTzero.action

### Demo

**URL:** https://trade.5ifund.com:9445/trade/pc_revokeToTzero.action

**Req:**

```json
{
    "rsRevokeDto": {
        "transActionAccountId": "600110009582",
        "capitalMethod": "1",
        "tradePassword": "123456a",
        "revokeAppSheetNo": "00000000000100250032",
        "businessCode": "022",
        "checkFlag": "0",
        "operator": "124",
        "fundCode": "000773",
        "shareType": "0"
    }
}
```

**Resp:**

```json
{
    "code": "0000",
    "custId": null,
    "listData": null,
    "message": "撤单到同花顺钱包成功",
    "singleData": "00000000000100250368",
    "url": null
}
```

# 钱包-定投-合约设立

## 定投初始化

**Method:** GET

**URL:** https://trade.5ifund.com:9445/tradePlan/pc_planInitOne.action

**Req:**

```json
fundCode
```

**Resp:**

```json
{
    "singleData":{
        tzeroFundCode
        realTimeCount
        realTimeList
    }
}

```

### Demo

**URL:** https://trade.5ifund.com:9445/tradePlan/pc_planInitOne.action?fundCode=003013

**Resp:**

```json
{
    "singleData": {
    "tzeroFundCode": "000773",
    "realTimeList": [
        {
            "availableVol": "873025.00",
            "bankAccount": "6230204177411444",
            "bankName": "工商银行",
            "transActionAccountId": "600100007728"
        }
    ],
    "realTimeCount": 1
}
}
```

## 定投


**Method:** POST

**URL:** https://trade.5ifund.com:9445/tradePlan/pc_plan.action

**Req:** 新增字段"agmType"

### Demo

**URL:** https://trade.5ifund.com:9445/tradePlan/pc_plan.action

**Req:**

```json
｛
    "strPCPlanDTO":{
        "taCode": "47",                         TA代码
        "fundName": "汇添富移动互联股票",         基金名称
        "shareType": "0",                       份额类型
        "fundCode": "000697",                   基金代码
        "fundType": "3",                        基金类型
        "registrar": "汇添富基金管理有限公司",     注册登记机构
        "endType": "1",                         终止条件（0-按协议终止日期，1-按终止期数，2-按终止总金额）
        "endSumAmount": "",                     协议终止总金额
        "endCounter": "999",                    协议终止期数
        "agmEndDay": "",                        协议终止日期
        "purposeOfPeSubs": "",                  定投目的
        "frequencyOfPeSubs": "1",               定投频率（与定投周期配套使用）
        "periodSubTimeUnit": "2",               定投周期单位（0-日，1-周，2-月，与定投频率配套使用）
        "tradePassword": "123456a",             交易密码
        "capitalMethod": "A",                   资金方式
        "payMode": "0",                         支付方式（0-代扣，1-网关）
        "firstAppDay": "20180201",              第一次扣款日（必须大于申请日）
        "money": "100",                         定投金额
        "transActionAccountId": "600100007728"  交易账号
        "agmType":"2"                           协议类型（0-银行卡购买，1-收益宝，2-钱包购买）
    }
｝
```

**Resp:**

```json
{
    "code": "0000",
    "custId": null,
    "listData": null,
    "message": "定投成功",
    "singleData": "000100001734",
    "url": null
}
```

# 赎回-普通基金赎回到钱包

## 赎回初始化

**Method:** GET

**URL:** https://trade.5ifund.com:9445/trade/pc_shareRedemptionInitOne.action

**Req:**

```json
fundCode

transActionAccountId
```

**Resp:** 新增钱包信息

```json
{
    "singleData":{
        useable_flag
        tzeroFundList
        isrealtime_flag
    }
}
```

### Demo

**URL:** https://trade.5ifund.com:9445/trade/pc_shareRedemptionInitOne.action?fundCode=000697&transActionAccountId=600100004600

**Resp:**

```json
{
    "singleData": {
    "useable_flag": "1",
    "tzeroFundList": [
        {
            "fundCode": "000773",
            "fundName": "万家现金宝",
            "fundType": "0",
            "shareType": "0",
            "taCode": "19"
        }
    ],
    "isrealtime_flag": "1"
}
}
```

### 赎回

**Method:** POST

**URL:** https://trade.5ifund.com:9445/trade/pc_shareRedemptionToTzero.action

### Demo

**URL:** https://trade.5ifund.com:9445/trade/pc_shareRedemptionToTzero.action

**Req:**

```json
{
    "strRsTransDTO": {
        "fundName": "万家现金宝",
        "codeOfTargetFund": "000773",
        "targetShareType": "0",
        "fundType": "0",
        "supportShareType": "0",
        "fundCode": "000697",
        "transactionAccountId": "600100004600",
        "tradePassword": "9CBF8A4DCB8E30682B927F352D6559A0",
        "money": "1000.00",
        "operator": "124",
        "largeRedemptionFlag": "0",
        "taCode": "19"
    }
}
```

**Resp:**

```json
{
    "code": "0000",
    "custId": null,
    "listData": null,
    "message": "赎回成功",
    "singleData": null,
    "url": null
}
```

# 钱包-首页-交易查询

**Method:** GET

**URL:** https://trade.5ifund.com:9445/pc_query/trade_revokeInitTzeroWithTradeList.action

### Demo

**URL:** https://trade.5ifund.com:9445/pc_query/trade_revokeInitTzeroWithTradeList.action?offer=1&limit=1

**Resp:**

```json
{
    "code": "0000",
    "custId": "100000000002",
    "listData": null,
    "message": null,
    "singleData": {
        "ovRETCODE": "0000",
        "ov_sum": 29,
        "ovRETMSG": "查询成功",
        "i_n_rowbegin": 1,
        "i_vc_custid": "100000000002",
        "i_n_rownum": 1,
        "ov_cursor": [
            {
                "applicationvol": "100.00",
                "bankFundNo": "307600100006919",
                "c_confirmflagname": "确认成功",
                "c_sharetype": "2",
                "cancelflag": "1",
                "capitalmethod": "1",
                "capitalmethodname": "通联",
                "confirmedamount": "100.00",
                "confirmflag": "3",
                "confirmtime": null,
                "fundTradeNo": "009201801190000000008",
                "moneyflag": "0",
                "nd_nav": "1.0000",
                "num": "1",
                "oriBusinClass": "1",
                "quickflag": "0",
                "seq": "5618",
                "spUser": "00999000001002296",
                "sysTime": "20180123 14:04:29",
                "vc_accepttime": "2018.01.19 09:45:30",
                "vc_appsheetserialno": "00000000000100249689",
                "vc_bankaccount": "6230205578555558",
                "vc_bankname": "中国农业1",
                "vc_businesscode": "052",
                "vc_businessname": "撤单申请",
                "vc_canceltime": null,
                "vc_fundcode": "000773",
                "vc_fundname": "万家现金宝货币",
                "vc_toaccounttime": null,
                "vc_transactionaccountid": "600100006919",
                "vc_transactiondate": "20180119",
                "vc_transactiontime": "09:45:30"
            }
        ],
        "i_vc_appsheetserialno": null,
        "i_vc_fundcode": null
    },
    "url": null
}
```


