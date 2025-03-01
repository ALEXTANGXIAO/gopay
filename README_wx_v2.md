<div align=center><img width="240" height="240" alt="Logo was Loading Faild!" src="https://raw.githubusercontent.com/go-pay/gopay/main/logo.png"/></div>

---

### 微信支付V2 API

> #### 推荐使用V3接口，官方在V3接口实现未覆盖或gopay未开发的接口，还继续用V2接口。

* 统一下单：`client.UnifiedOrder()`
    * JSAPI - JSAPI支付（或小程序支付）
    * NATIVE - Native支付
    * APP - app支付
    * MWEB - H5支付
* 提交付款码支付：`client.Micropay()`
* 查询订单：`client.QueryOrder()`
* 关闭订单：`client.CloseOrder()`
* 撤销订单：`client.Reverse()`
* 申请退款：`client.Refund()`
* 查询退款：`client.QueryRefund()`
* 下载对账单：`client.DownloadBill()`
* 下载资金账单（正式）：`client.DownloadFundFlow()`
* 交易保障：`client.Report()`
* 拉取订单评价数据（正式）：`client.BatchQueryComment()`
* 企业付款（正式）：`client.Transfer()`
* 查询企业付款（正式）：`client.GetTransferInfo()`
* 授权码查询OpenId（正式）：`client.AuthCodeToOpenId()`
* 公众号纯签约（正式）：`client.EntrustPublic()`
* APP纯签约-预签约接口-获取预签约ID（正式）：`client.EntrustAppPre()`
* H5纯签约（正式）：`client.EntrustH5()`
* 支付中签约（正式）：`client.EntrustPaying()`
* 请求单次分账（正式）：`client.ProfitSharing()`
* 请求多次分账（正式）：`client.MultiProfitSharing()`
* 查询分账结果（正式）：`client.ProfitSharingQuery()`
* 添加分账接收方（正式）：`client.ProfitSharingAddReceiver()`
* 删除分账接收方（正式）：`client.ProfitSharingRemoveReceiver()`
* 完结分账（正式）：`client.ProfitSharingFinish()`
* 分账回退（正式）：`client.ProfitSharingReturn()`
* 分账回退结果查询（正式）：`client.ProfitSharingReturnQuery()`
* 企业付款到银行卡API（正式）：`client.PayBank()`
* 查询企业付款到银行卡API（正式）：`client.QueryBank()`
* 获取RSA加密公钥API（正式）：`client.GetRSAPublicKey()`
* 发放现金红包：`client.SendCashRed()`
* 发放现金裂变红包：`client.SendGroupCashRed()`
* 发放小程序红包：`client.SendAppletRed()`
* 查询红包记录：`client.QueryRedRecord()`
* 订单附加信息提交（海关）：`client.CustomsDeclareOrder()`
* 订单附加信息查询（海关）：`client.CustomsDeclareQuery()`
* 订单附加信息重推（海关）：`client.CustomsReDeclareOrder()`
* 自定义方法请求微信API接口：`client.PostWeChatAPISelf()`

### 微信公共V2 API

* `wechat.GetParamSign()` => 获取微信支付所需参数里的Sign值（通过支付参数计算Sign值）
* `wechat.GetSanBoxParamSign()` => 获取微信支付沙箱环境所需参数里的Sign值（通过支付参数计算Sign值）
* `wechat.GetMiniPaySign()` => 获取微信小程序支付所需要的paySign
* `wechat.GetH5PaySign()` => 获取微信内H5支付所需要的paySign
* `wechat.GetAppPaySign()` => 获取APP支付所需要的paySign
* `wechat.ParseNotifyToBodyMap()` => 解析微信支付异步通知的参数到BodyMap
* `wechat.ParseNotify()` => 解析微信支付异步通知的参数
* `wechat.ParseRefundNotify()` => 解析微信退款异步通知的参数
* `wechat.VerifySign()` => 微信同步返回参数验签或异步通知参数验签
* `wechat.Code2Session()` => 登录凭证校验：获取微信用户OpenId、UnionId、SessionKey
* `wechat.GetAppletAccessToken()` => 获取微信小程序全局唯一后台接口调用凭据
* `wechat.GetAppletPaidUnionId()` => 微信小程序用户支付完成后，获取该用户的 UnionId，无需用户授权
* `wechat.GetPublicUserInfo()` => 微信公众号：获取用户基本信息
* `wechat.GetPublicUserInfoBatch()` => 微信公众号：批量获取用户基本信息
* `wechat.DecryptOpenDataToStruct()` => 加密数据，解密到指定结构体
* `wechat.DecryptOpenDataToBodyMap()` => 加密数据，解密到 BodyMap
* `wechat.GetOpenIdByAuthCode()` => 授权码查询openid
* `wechat.GetOauth2AccessToken()` => 微信第三方登录，code 换取 access_token
* `wechat.RefreshOauth2AccessToken()` => 刷新微信第三方登录后，获取到的 access_token
* `wechat.CheckOauth2AccessToken()` => 检验授权凭证（access_token）是否有效
* `wechat.GetOauth2UserInfo()` => 微信开放平台：获取用户个人信息
* `wechat.DecryptRefundNotifyReqInfo()` => 解密微信退款异步通知的加密数据

---

## 1、初始化GoPay客户端并做配置（HTTP请求均默认设置tls.Config{InsecureSkipVerify: true}）

* #### 微信V2

微信官方文档：[官方文档](https://pay.weixin.qq.com/wiki/doc/api/index.html)

> 注意：微信支付下单等操作可用沙箱环境测试是否成功，但真正支付时，请使用正式环境 `isProd = true`，不然会报错。

> 微信证书二选一：只传 `apiclient_cert.pem` 和 `apiclient_key.pem` 或者只传 `apiclient_cert.p12`

```go
import (
    "github.com/go-pay/gopay/wechat"
)

// 初始化微信客户端
//    appId：应用ID
//    mchId：商户ID
//    apiKey：API秘钥值
//    isProd：是否是正式环境
client := wechat.NewClient("wxdaa2ab9ef87b5497", mchId, apiKey, false)

// 打开Debug开关，输出请求日志，默认关闭
client.DebugSwitch = gopay.DebugOn

// 设置国家：不设置默认 中国国内
//    wechat.China：中国国内
//    wechat.China2：中国国内备用
//    wechat.SoutheastAsia：东南亚
//    wechat.Other：其他国家
client.SetCountry(wechat.China)

// 添加微信pem证书
client.AddCertPemFilePath()
client.AddCertPemFileContent()

// 添加微信pkcs12证书
client.AddCertPkcs12FilePath()
client.AddCertPkcs12FileContent()
```

## 2、初始化并赋值BodyMap（client的方法所需的入参）

* #### 微信请求参数
  * 微信V2接口通用参数（mch_id、appid、sign）无需传入，client 请求时会默认处理

具体参数请根据不同接口查看：[微信支付接口文档](https://pay.weixin.qq.com/wiki/doc/api/index.html)

```go
import (
    "github.com/go-pay/gopay/pkg/util"
    "github.com/go-pay/gopay/wechat"
)

// 初始化 BodyMap
bm := make(gopay.BodyMap)
bm.Set("nonce_str", util.GetRandomString(32)).
    Set("body", "H5支付").
    Set("out_trade_no", number).
    Set("total_fee", 1).
    Set("spbill_create_ip", "127.0.0.1").
    Set("notify_url", "https://www.fmm.ink").
    Set("trade_type", TradeType_H5).
    Set("device_info", "WEB").
    Set("sign_type", SignType_MD5).
    SetBodyMap("scene_info", func(bm gopay.BodyMap) {
        bm.SetBodyMap("h5_info", func(bm gopay.BodyMap) {
            bm.Set("type", "Wap")
            bm.Set("wap_url", "https://www.fmm.ink")
            bm.Set("wap_name", "H5测试支付")
        })
    }) /*.Set("openid", "o0Df70H2Q0fY8JXh1aFPIRyOBgu8")*/
```

## 3、client 方法调用

* #### 微信V2

```go
wxRsp, err := client.UnifiedOrder(bm)
wxRsp, err := client.Micropay(bm)
wxRsp, err := client.QueryOrder(bm)
wxRsp, err := client.CloseOrder(bm)
wxRsp, err := client.Reverse(bm)
wxRsp, err := client.Refund(bm)
wxRsp, err := client.QueryRefund(bm)
wxRsp, err := client.DownloadBill(bm)
wxRsp, err := client.DownloadFundFlow(bm)
wxRsp, err := client.BatchQueryComment(bm)
wxRsp, err := client.Transfer(bm)
...
```

## 4、微信统一下单后，获取微信小程序支付、APP支付、微信内H5支付所需要的 paySign

* #### 微信V2

微信小程序支付官方文档：[微信小程序支付API](https://developers.weixin.qq.com/miniprogram/dev/api/open-api/payment/wx.requestPayment.html)

APP支付官方文档：[APP端调起支付的参数列表文档](https://pay.weixin.qq.com/wiki/doc/api/app/app.php?chapter=9_12)

微信内H5支付官方文档：[微信内H5支付文档](https://pay.weixin.qq.com/wiki/doc/api/wxpay/ch/pay/OfficialPayMent/chapter5_5.shtml)

```go
import (
    "github.com/go-pay/gopay/wechat"
)

// ====微信小程序 paySign====
timeStamp := strconv.FormatInt(time.Now().Unix(), 10)
packages := "prepay_id=" + wxRsp.PrepayId   // 此处的 wxRsp.PrepayId ,统一下单成功后得到
// 获取微信小程序支付的 paySign
//    appId：AppID
//    nonceStr：随机字符串
//    packages：统一下单成功后拼接得到的值
//    signType：签名方式，务必与统一下单时用的签名方式一致
//    timeStamp：时间
//    apiKey：API秘钥值
paySign := wechat.GetMiniPaySign(AppID, wxRsp.NonceStr, packages, wechat.SignType_MD5, timeStamp, apiKey)

// ====APP支付 paySign====
timeStamp := strconv.FormatInt(time.Now().Unix(), 10)
// 获取APP支付的 paySign
// 注意：package 参数因为是固定值，无需开发者再传入
//    appId：AppID
//    partnerid：partnerid
//    nonceStr：随机字符串
//    prepayId：统一下单成功后得到的值
//    signType：签名方式，务必与统一下单时用的签名方式一致
//    timeStamp：时间
//    apiKey：API秘钥值
paySign := wechat.GetAppPaySign(appid, partnerid, wxRsp.NonceStr, wxRsp.PrepayId, wechat.SignType_MD5, timeStamp, apiKey)

// ====微信内H5支付 paySign====
timeStamp := strconv.FormatInt(time.Now().Unix(), 10)
packages := "prepay_id=" + wxRsp.PrepayId   // 此处的 wxRsp.PrepayId ,统一下单成功后得到
// 获取微信内H5支付 paySign
//    appId：AppID
//    nonceStr：随机字符串
//    packages：统一下单成功后拼接得到的值
//    signType：签名方式，务必与统一下单时用的签名方式一致
//    timeStamp：时间
//    apiKey：API秘钥值
paySign := wechat.GetH5PaySign(AppID, wxRsp.NonceStr, packages, wechat.SignType_MD5, timeStamp, apiKey)
```

## 5、同步返回参数验签Sign、异步通知参数解析和验签Sign、异步通知返回

> 异步通知请求参数需要先解析，解析出来的结构体或BodyMap再验签（此处需要注意，`http.Request.Body` 只能解析一次，如果需要解析前调试，请处理好Body复用问题）

[Gin Web框架（推荐）](https://github.com/gin-gonic/gin)

[Echo Web框架](https://github.com/labstack/echo)


* #### 微信V2

```go
import (
    "github.com/go-pay/gopay"
    "github.com/go-pay/gopay/wechat"
)

// ====同步返回参数验签Sign====
wxRsp, err := client.UnifiedOrder(bm)
// 微信同步返回参数验签或异步通知参数验签
//    apiKey：API秘钥值
//    signType：签名类型（调用API方法时填写的类型）
//    bean：微信同步返回的结构体 wxRsp 或 异步通知解析的结构体 notifyReq
//    返回参数 ok：是否验签通过
//    返回参数 err：错误信息
ok, err := wechat.VerifySign(apiKey, wechat.SignType_MD5, wxRsp)

// ====支付异步通知参数解析和验签Sign====
// 解析支付异步通知的参数
//    req：*http.Request
//    ctx.Request   是 gin 框架的获取 *http.Request
//    ctx.Request() 是 echo 框架的获取 *http.Request
//    返回参数 notifyReq：通知的参数
//    返回参数 err：错误信息
notifyReq, err := wechat.ParseNotifyToBodyMap(ctx.Request)

// 验签操作
ok, err := wechat.VerifySign(apiKey, wechat.SignType_MD5, notifyReq)

// ====退款异步通知参数解析，退款通知无sign，不用验签====
// 
// 解析退款异步通知的参数，解析出来的 req_info 是加密数据，需解密
//    req：*http.Request
//    ctx.Request   是 gin 框架的获取 *http.Request
//    ctx.Request() 是 echo 框架的获取 *http.Request
//    返回参数 notifyReq：通知的参数
//    返回参数 err：错误信息
notifyReq, err := wechat.ParseNotifyToBodyMap(c.Request)
 或
notifyReq, err := wechat.ParseRefundNotify(c.Request)

// ==解密退款异步通知的加密参数 req_info ==
refundNotify, err := wechat.DecryptRefundNotifyReqInfo(notifyReq.ReqInfo, apiKey)

// ==异步通知，返回给微信平台的信息==
rsp := new(wechat.NotifyResponse) // 回复微信的数据
rsp.ReturnCode = gopay.SUCCESS
rsp.ReturnMsg = gopay.OK

// 此写法是 gin 框架返回微信的写法
c.String(http.StatusOK, "%s", rsp.ToXmlString())
// 此写法是 echo 框架返回微信的写法
return c.String(http.StatusOK, rsp.ToXmlString())
```

## 6、公共API（仅部分说明）

* #### 微信V2 公共API

官方文档：[code2Session](https://developers.weixin.qq.com/miniprogram/dev/api-backend/open-api/login/auth.code2Session.html)

button按钮获取手机号码：[button组件文档](https://developers.weixin.qq.com/miniprogram/dev/component/button.html)

微信解密算法文档：[解密算法文档](https://developers.weixin.qq.com/miniprogram/dev/framework/open-ability/signature.html)

```go
import (
    "github.com/go-pay/gopay/wechat"
)

// 获取微信小程序用户的OpenId、SessionKey、UnionId
//    appId：微信小程序的APPID
//    appSecret：微信小程序的AppSecret
//    wxCode：小程序调用wx.login 获取的code
sessionRsp, err := wechat.Code2Session(appId, appSecret, wxCode)

// ====解密微信加密数据到指定结构体====

//小程序获取手机号
data := "Kf3TdPbzEmhWMuPKtlKxIWDkijhn402w1bxoHL4kLdcKr6jT1jNcIhvDJfjXmJcgDWLjmBiIGJ5acUuSvxLws3WgAkERmtTuiCG10CKLsJiR+AXVk7B2TUQzsq88YVilDz/YAN3647REE7glGmeBPfvUmdbfDzhL9BzvEiuRhABuCYyTMz4iaM8hFjbLB1caaeoOlykYAFMWC5pZi9P8uw=="
iv := "Cds8j3VYoGvnTp1BrjXdJg=="
session := "lyY4HPQbaOYzZdG+JcYK9w=="
phone := new(wechat.UserPhone)
// 解密开放数据
//    encryptedData：包括敏感数据在内的完整用户信息的加密数据，小程序获取到
//    iv：加密算法的初始向量，小程序获取到
//    sessionKey：会话密钥，通过 wechat.Code2Session() 方法获取到
//    beanPtr：需要解析到的结构体指针，操作完后，声明的结构体会被赋值
err := wechat.DecryptOpenDataToStruct(data, iv, session, phone)
xlog.Debug(*phone)
// 获取微信小程序用户信息
sessionKey := "tiihtNczf5v6AKRyjwEUhQ=="
encryptedData := "CiyLU1Aw2KjvrjMdj8YKliAjtP4gsMZMQmRzooG2xrDcvSnxIMXFufNstNGTyaGS9uT5geRa0W4oTOb1WT7fJlAC+oNPdbB+3hVbJSRgv+4lGOETKUQz6OYStslQ142dNCuabNPGBzlooOmB231qMM85d2/fV6ChevvXvQP8Hkue1poOFtnEtpyxVLW1zAo6/1Xx1COxFvrc2d7UL/lmHInNlxuacJXwu0fjpXfz/YqYzBIBzD6WUfTIF9GRHpOn/Hz7saL8xz+W//FRAUid1OksQaQx4CMs8LOddcQhULW4ucetDf96JcR3g0gfRK4PC7E/r7Z6xNrXd2UIeorGj5Ef7b1pJAYB6Y5anaHqZ9J6nKEBvB4DnNLIVWSgARns/8wR2SiRS7MNACwTyrGvt9ts8p12PKFdlqYTopNHR1Vf7XjfhQlVsAJdNiKdYmYVoKlaRv85IfVunYzO0IKXsyl7JCUjCpoG20f0a04COwfneQAGGwd5oa+T8yO5hzuyDb/XcxxmK01EpqOyuxINew=="
iv2 := "r7BXXKkLb8qrSNn05n0qiA=="

// 微信小程序 用户信息
userInfo := new(wechat.AppletUserInfo)
err = wechat.DecryptOpenDataToStruct(encryptedData, iv2, sessionKey, userInfo)
xlog.Debug(*userInfo)

data := "Kf3TdPbzEmhWMuPKtlKxIWDkijhn402w1bxoHL4kLdcKr6jT1jNcIhvDJfjXmJcgDWLjmBiIGJ5acUuSvxLws3WgAkERmtTuiCG10CKLsJiR+AXVk7B2TUQzsq88YVilDz/YAN3647REE7glGmeBPfvUmdbfDzhL9BzvEiuRhABuCYyTMz4iaM8hFjbLB1caaeoOlykYAFMWC5pZi9P8uw=="
iv := "Cds8j3VYoGvnTp1BrjXdJg=="
session := "lyY4HPQbaOYzZdG+JcYK9w=="
    
// 解密开放数据到 BodyMap
//    encryptedData:包括敏感数据在内的完整用户信息的加密数据
//    iv:加密算法的初始向量
//    sessionKey:会话密钥
bm, err := wechat.DecryptOpenDataToBodyMap(data, iv, session)
if err != nil {
    xlog.Debug("err:", err)
    return
}
xlog.Debug("WeChatUserPhone:", bm)
```

## License

```
Copyright 2019 Jerry

Licensed under the Apache License, Version 2.0 (the "License");
you may not use this file except in compliance with the License.
You may obtain a copy of the License at

    http://www.apache.org/licenses/LICENSE-2.0

Unless required by applicable law or agreed to in writing, software
distributed under the License is distributed on an "AS IS" BASIS,
WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
See the License for the specific language governing permissions and
limitations under the License.
```