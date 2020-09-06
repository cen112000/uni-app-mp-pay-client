## uni-app 微信小程序获取openid
	1，前端调用uni.login()获取code凭证。
	2，将code传入后端，由后端根据code获取用户的openid。
## 小程序支付说明（开发环境为‘本地’）
	1，点击按钮‘支付0.1元’，向后端发起请求，如果正常，返回参数如下：
	// appId: "xxx" //小程序的appid
	// nonceStr: "xxx",//随机字符
	// package: "xxx" //微信小程序需要package参数,
	// paySign: "xxx",//支付签名
	// signType: "MD5" //加密方式 默认'MD5'
	// timeStamp: "xxx" //时间戳
	2，前端使用uni.requestPayment()进行支付。