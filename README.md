
对接文档
# 印度支付通道对接文档

```diff
-请仔细阅读红色部分文字
```

## 目录

- [H5支付下单接口](#H5支付下单接口)
- [代付接口](#代付接口)
- [支付和代付异步通知](#支付和代付异步通知)
- [代付订单查询](#代付订单查询)
- [代付商户可提现余额查询接口说明](#代付商户可提现余额查询接口说明)


**文档属性**

版本号∶ v1.4
更新日期 ∶ 2020-01-01
# H5支付下单接口
## 1. 下单接口说明
H5支付通过浏览器或者APP的Webview直接跳转到生成支付信息URL的方式直接调起支付。
## 1.1 请求方式 
get方法
浏览器或者Webview跳转
## 1.2 请求网关地址
找管理员获取支付下单网关地址

## 1.3 下单参数说明

| 参数        | 参数名称   |  类型(长 度) |参数说明 |是否必填
| --------   | -----:  | :----:  |:----:  |:----:  |
| mch_id    | 商户号  |  String(5)     |签约商户的商户号|是
|out_trade_no|商户订单 号| String(24)| 合作商户唯一的订单号。合作商户唯一的订单号,最长24位| 是
|total_fee |交易金额 |String(24) |总金额，以分为单位，不允许 包含任何字、符号。| 是
|pay_type |支付类型 |String(2)| 11：支付宝WAP支付； 17：支付宝个人转账WAP支付； 21：微信WAP支付； 31：QQ钱包WAP支付； 41：银联快捷支付； 42:银行卡网关支付； 51：京东H5支付。| 是
|body |商品名称| String| 商品标题/交易标题/订单标题/ 订单关键字等。 |否
|bank_code| 银行代码|String |银行代码，详情见本文档附录。 银行卡网关支付必填参数 |否
|card_type |银行卡类型 | String |借记卡：1 信用卡：2， 银行卡网关支付必填参数。 |否
|card_no| 银行卡卡号|String |银行卡卡号，个别银联快捷支付通道，需要必传 该字段。 使用前请联系管理员|否
|sign |签名| String(32) |对支付信息使用MD5签名| 
|is_raw |是否原生支付 |Int |1：原生支付，没有回调地址； 2：有回调地址的Wap支付。（默认情况填1，有特殊情况管理员会通知） |是
|callback_url |同步跳转地址| String(256) |付完成之后跳转到商户指定URL地址，个别通道 是无效的，如果有需要联系管理员确认（不是订单通知地址,订单通知地址在支付系统后台配 置）。|是 

```diff
-备注：callback_url 不是回调通知地址，回调通知地址是需要在商户后台进行配置
```

## 1.4. 接入步骤
 1.4.1 生成订单MD5签名
 
 订单信息（商户订单号+交易金额+商户号+商户秘钥）进行UTF-8编码后台再进行32位 小写MD5编码。 
 拼接方式是直接拼接，不包含符号+，比如订单号123456789，商户号abcd，拼接后是 123456789abcd 
 
 1.4.2 初始化配置参数 1. 根据下单参数说明生成GET请求的URL地址 
 ```html
 http://api.xxxxx.com/lpay/pay/gateway? 
 sign=XXX& 
 mch_id=XXXX& 
 out_trade_no=XXXXX& 
 total_fee=XXXXXX& 
 pay_type=XX& 
 is_raw=1& 
 body=XXXXXXX& 
 callback_url=XXX
```

实例：
```html
1. https://api.xxxxx.com:8888/lpay/pay/gateway?
sign=b1ad420f2233d6f257c0baf7b54f9481&mch_id=10009&out_trade_no=201710
10170633626081&total_fee=1&pay_type=21&is_raw=1&body=%E5%A5%B6%E8%8C%B
6&callback_url=http%3A%2F%2Fpay.bhwjq.com%2Flpay%2Fpay%2Fcomplete
```
备注：body和callback_url需要进行URLEncoder进行编码

2. 通过浏览器或者Webview跳转上面生成的URL地



# 代付接口

## 1. 下单接口说明
代付通过下单接口直接返回订单对应JSON数据。

### 1.1 请求方式
POST

### 1.2 请求网关地址
正式请求地址请联系年轻帅气的系统管理员获取 
https://api.xxxxx.com/lpay/agentpay/gateway

### 1.3 下单参数说明
### 参数说明 Tables

| 参数        | 参数名称   |  类型(长 度) |参数说明 |是否必填
| --------   | -----:  | :----:  |:----:  |:----:  |
| mch_id    | 商户号  |  String(5)     |签约商户的商户号|是
|out_trade_no|商户订单 号| String(24)| 合作商户唯一的订单号。| 是
|total_fee |交易金额 |String(24) |总金额，以分为单位，不允许 包含任何字、符号。| 是
|pay_type |支付类型 |String(2)| 86:代付；| 是
|body |商品名称| String| 商品标题/交易标题/订单标题/ 订单关键字等。 |是
|bank_account| 收款人账 号 |String |收款人银行账号或者UPI， ID。 |是 
|account_holder |收款人姓 名| String |收款人姓名。 |是 
|deposit_bank_code| 开户行代 码 |String |开户行代码。必填但是 不是必须正确，没有可随便填写|是
|deposit_bank |开户行名 称| String |开户行名称。必填但是 不是必须正确，没有可随便填写|是
|acct_type |转账类型 |String |bank：银行账户，wallet：用户钱包，upi用户传upi。 |是 
|account_holder_mobile |开户人手 机号| String |开户人手机号。必填但是不是 必须正确 |是 
|province |开户行所 属省份| String |开户行所属省份号。必填但是 不是必须正确，没有可随便填写 |是 
|city |开户行所 属城市| String |开户行所属城市。必填但是 不是必须正确，没有可随便填写 |是 
|sub_branch |收款方IFSC| String |收款方IFSC，印度银行卡代付必须填正确的(IFSC)| 是 
|account_holder_id| 收款方邮箱| String |收款方邮箱| 是 
|sign |签名 |String(32)| 对支付信息使用MD5签名。| 是


## 2. 接入步骤
### 2.1 生成订单MD5签名
```html
订单信息（商户订单号+交易金额+收款人账号+收款人姓名+开户行名称+开户人手机号 +商户号+商户秘钥）进行UTF-8编码的MD5编码。
订单信息（out_trade_no+total_fee+bank_account+account_holder+deposit_bank+account_holder_mobile+user_mch_id+商户秘钥）进行UTF-8编码的MD5编码。
备注：直接字符串拼接不包符号 “+”
```
### 2.2 请求支付网关接口
1.使用POST方式请求网关，传递参数如下
```html
sign=XXX
mch_id=XXXX
out_trade_no=XXXXX
total fee=XxxXXX
pay_type=XX
body=XXXXXXX
```
`备注：普通表单提交数据,非JSON数据}`

2.接收返回JSON数据，数据如下∶
JSON数据说明
| 参数        | 参数名称   |  类型(长 度) |参数说明 |是否必填
| --------   | -----:  | :----:  |:----  |:----:  |
|result_code |结果代码 |Int |0：订单成功； 1：订单失败； 此处返回的是代付订单提交是否成功状态，不是 代付打款是否成功，代付是否成功通过代付查询 接口，或者是异步回调获取| 是 
|err_msg| 错误信息 |String(256) |result_code=1时传递错误信息| 否 
|out_trade_no| 商户订单号| String(24) |合作商户唯一的订单号。 |是 
|transaction_id |平台订单号| String(24) |平台订单号。| 是


实例数据
```html
{  
  out_trade_no: "8000120170912170755", 
  transaction_id: "80001c868000120170912170755",  
  result_code: 0  
} 
```

# 支付和代付异步通知
## 1 异步支付结果通知
聚合支付系统在接收到各类支付通道的支付结果通知后，会根据后台配置的通知URL， 通过POST请求的形式将支付结果作为参数通知到商户系统，商户系统根据订单通知的支 付结果进行后续业务逻辑的处理。当商户收到服务器异步通知并打印出success时，视为 该笔订单处理完成，否则平台会通过一定的策略（如通知频率 5/15/180/1800/1800/1800/1800/3600，单位：秒）定期重新发起通知。

异步通知参数说明
| 参数        | 参数名称   |  类型(长 度) |参数说明 |是否必填
| --------   | -----:  | :----:  |:----  |:----:  |
|out_trade_no |商户订单号 |String(24) |合作商户唯一的订单号。| 是 
|transaction_id| 平台订单号 |String(24) |平台交易号| 是
|total_fee| 交易金额| String(24) |总金额，以分为单位，不允许包含任何字、符 号。 |是 
|time_end |支付完成时间| String(14) |支付完成时间，格式为yyyyMMddHHmmss， 如2009年12月27日9点10分10秒表示为 20091227091010。| 是
|status |订单状态| Int |0：成功； 非0：失败。| 是
|pay_type |支付类型| String(1) |1：支付宝； 2：微信 ； 3：QQ钱包。| 是
|sign |签名| String(32) |订单信息MD5签名。| 是

```diff
-备注：通知信息可以通过request对象获取。 
-备注2：收到支付结果异步通知之后返回success。
-备注3通知地址需要在商户后台配置
```


## MD5签名

订单信息（商户订单号+平台订单号+支付完成时间+商户号+商户秘钥）进行UTF-8编 码的MD5编码。


# 代付订单查询

## 1. 代付订单查询接口说明 
代付订单查询通过下单接口直接返回订单状态对应JSON数据。 
### 1.1 请求方式 
POST GET 
### 1.2 请求网关地址 
正式请求地址请联系系统管理员获取 
http://api.找管理员.com/lpay/agentpay/queryOrder

### 1.3 订单查询参数说明 
| 参数        | 参数名称   |  类型(长 度) |参数说明 |是否必填
| --------   | -----:  | :----:  |:----  |:----:  |
|mch_id |商户号 |String(5) |签约商户的商户号。| 是 
|out_trade_no| 商户订单号 |String(24) |合作商户唯一的订单号。| 是
|sign| 签名 |String(32) |对支付信息使用MD5签名。| 是

## 2. 接入步骤
### 2.1 订单查询MD5签名 
订单签名信息（商户订单号+商户号+商户秘钥）进行UTF-8编码的MD5编码。
### 2.2 请求支付网关接口 
1. 使用POST方式请求网关，传递参数如下
```html
sign=XXX 
mch_id=XXXX 
out_trade_no=XXXXX 
```
备注：普通表单提交数据,非JSON数据 
2. 接收返回JSON数据，数据如下：
JSON数据说明
| 参数        | 参数名称   |  类型(长 度) |参数说明 |是否必填
| --------   | -----:  | :----:  |:----  |:----:  |
|result_code |结果代码 |Int |0：审核中； 1：审核完成； 2：打款进行中； 3：打款成功； 4：订单关闭； 5：打款失败； 6：订单不存在； -1：签名验证失败；不是订单状态，是查 询不成功 -2：提交上游中； -3：代付异常,联系管理员； 只有状态3是代表打款成功，其他状态都不 是成功，只有状态5是打款失败，其他都不 是 |是
|err_msg |错误信息 |String(256) |result_code=-1时传递错误信息 否 mch_id 商户号 String(5) 签约商户的商户号。| 是
|out_trade_no| 商户订单 号 |String(24) |合作商户唯一的订单号。| 是
|total_fee |订单金额| Int| 订单存在时返回订单金额。 |否 
|sign |订单查询 结果签名 |String(32)| 订单查询结果签名。| 是

实例数据
```html
{ mch_id: "10009" 
  out_trade_no: "1505277760485" 
  result_code: 3  
  total_fee: 1000 
  sign: "d4wwwewewew12251658weweweb8590d2d6" 
  }
```
订单查询结果MD5签名 
订单信息（商户订单号+金额+结果代码+商户号+商户秘钥）进行UTF-8编码的 MD5编码。 
备注：订单不存在时签名串中去掉金额，即订单信息（商户订单号+结果代码+商户 号+商户秘钥）进行UTF-8编码的MD5编码

# 代付商户可提现余额查询接口说明 
## 1.1 请求方式 
POST GET 
## 1.2 请求网关地址
http://api.找管理员要地址.com/lpay/agentpay/queryCustomerAmount

1.3 可提现余额查询参数说明
| 参数        | 参数名称   |  类型(长 度) |参数说明 |是否必填
| --------   | -----:  | :----:  |:----  |:----:  |
|mch_id |商户号 |String(5) |签约商户的商户号。| 是
|sign| 签名|String(32) |对支付信息使用MD5签名。| 是

## 2. 接入步骤 
### 2.1 可提现余额查询MD5签名 
签名信息（商户号+商户秘钥）进行UTF-8编码的MD5编码。 
### 2.2 请求支付网关接口 
 1. 使用POST方式请求网关，传递参数如下 
 ```html
 sign=XXX 
 mch_id=XXXX
```
 备注：普通表单提交数据,非JSON数据 
 2. 接收返回JSON数据，数据如下：
 JSON数据说明
| 参数        | 参数名称   |  类型(长 度) |参数说明 |是否必填
| --------   | -----:  | :----:  |:----  |:----:  |
|result_code |结果代码 |Int |0：正常； -1：签名验证失败； |是 
|err_msg |错误信息 |String(256) |result_code=-1时传递错误信息 否 mch_id 商户号 String(5) 签约商户的商户号。| 是 
|total_amount |总金额 |Int |商户可提现余额和冻结金额总和 (单位：分)。| 否
|account_amount |可提现余 额 |Int |商户可提现余额(单位：分)。 |否 
|frozen_amount |冻结金额 |Int |商户提现中冻结金额(单位：分)。| 否 
|sign |查询结果 签名 |String(32) |查询结果签名 |是

实例数据
 ```html
{ 
 mch_id: "10009" 
 total_amount: 300 
 account_amount: 200
 frozen_amount: 100
 result_code: 0
 sign: "82904a6d4af7d73ewf0faddd1e1081d94dfc96c5" 
 }
 ```
 ## 1. 余额查询结果签名MD5签名 
 结果信息（总金额+可提现余额+冻结金额+结果代码+商户号+商户秘钥）进行UTF- 8编码的MD5编码。
 备注：签名不正确时，即结果签名信息（结果代码+商户号+商户秘钥）进行UTF-8 编码的MD5编码
