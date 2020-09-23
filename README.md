# 获取小程序码
 微信小程序生成小程序码有2种方式，一种是在小程序后台-工具-生成小程序码；另外一种是通过接口调用的形式生成小程序码。第一种就不详细说明了，这里只介绍第二种方法。

 ## 通过接口生成小程序码
 
<!-- TOC -->

- 分类 A 适用于需要的码数量较少的业务场景 - 生成小程序码，可接受 path 参数较长，生成个数受限，wxacode.get 与 wxacode.createQRCode 总共生成的码数量限制为 100,000，请谨慎调用。
    - 接口: [wxacode.createQRCode](https://developers.weixin.qq.com/miniprogram/dev/api-backend/open-api/qr-code/wxacode.createQRCode.html)
    - 接口: [wxacode.get](https://developers.weixin.qq.com/miniprogram/dev/api-backend/open-api/qr-code/wxacode.get.html)

   注：path 参数指 扫码进入的小程序页面路径，最大长度 128 字节，不能为空；对于小游戏，可以只传入 query 部分，来实现传参效果，如：传入 "?foo=bar"，即可在 wx.getLaunchOptionsSync 接口中的 query 参数获取到 {foo:"bar"}。

- 分类 B：[`适用于需要的码数量极多的业务场景`](https://developers.weixin.qq.com/miniprogram/dev/api-backend/open-api/qr-code/wxacode.getUnlimited.html)
    - 生成小程序码，可接受页面参数较短，生成个数不受限。
    * 但是这个接口的入参（scene，page）有一些限制。
    * 调用分钟频率受限（5000次/分钟），如需大量小程序码，建议预生成

<!-- /TOC -->
scene，page 参数说明


|  属性   | 类型  |  默认值   | 必填  | 说明  |
|  ----  | ----  |  ----  | ----  | ----  |
| scene  | string |   | 是 | 最大32个可见字符，只支持数字，大小写英文以及部分特殊字符：!#$&'()*+,/:;=?@-._~，其它字符请自行编码为合法字符（因不支持%，中文无法使用 urlencode 处理，请使用其他编码方式） |
| page  | string | 主页  | 否 | 必须是已经发布的小程序存在的页面（否则报错），例如 pages/index/index, 根路径前不要填加 /,不能携带参数（参数请放在scene字段里），如果不填写这个字段，默认跳主页面 |

### 实际业务场景中可能需要扫描或识别小程序码打开一个H5页面，这时候scene的长度可能就超出了，如果我们需要传递的参数比较多应该怎么处理比较合适？

* 解决方案：首先需要服务端提供2个接口，第一个接口A的功能是将比较长的URL转换成位数可控的唯一随机字符串存入数据库中；第二个接口B是通过这个唯一随机字符串反查URL。
* 使用场景：H5页面中首先调用A接口，获取到唯一随机字符串StringA,将StringA作为入参scene的值去调用getwxacodeunlimit接口，生成小程序码。微信端扫描或识别小程序码后打开webview，在webview中获取到scene参数解析之后获取到唯一随机字符串并调用接口B，获取到H5页面传入的URL，然后通过webview打开这个URL。

