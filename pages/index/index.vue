<template>
	<view class="content">
		<button type="primary">分享</button>
		<button class="m-t" type="primary" open-type="getUserInfo" @click="getMpOpenId">获取用户信息</button>
		<button class="m-t" type="warn" @click="mpPay">支付0.1元</button>
		<button class="m-t" type="warn" @click="getMpOrderInfo">查询订单</button>
	</view>
</template>

<script>
	export default {
		data() {
			return {
				title: 'Hello',
				openid: ""
			}
		},
		onLoad() {
			// #ifdef MP-WEIXIN
			let open = uni.getStorageSync('openid') 
			
			open = open ? JSON.parse(open): {}
			if(!open.openid) {
				this.openid = open.openid
				this.getMpOpenId()
				return
			}
			
			
			// #endif
		},
		methods: {
			getMpOpenId() {
				// #ifdef MP-WEIXIN
					uni.login({
						success: (res) => {
							let code = res.code 
							 uni.request({
							 	url:`http://localhost:7001/api/mp/getOpenId`,
								method:'GET',
								data:{
									code
								},
								success: (result) => {
									if(result.data.code === 0) {
										uni.setStorageSync('openid',JSON.stringify(result.data.data))
									}
								},
								fail: (error) => {
									console.log('获取openid fail ',error)
								}
							 })
						},
						fail: (err) => {
							console.log('获取code失败')
						}
					})
				// #endif
			},
			mpPay() {
				let url = `http://localhost:7001/api/mp/mpPay`
				let open = uni.getStorageSync('openid')
				open = open ? JSON.parse(open): {},
				console.log(open.openid)
				uni.request({
					url,
					data:{
						openid:open.openid
					},
					success: (res) => {
						console.log('支付 ok',res.data )
						// appid: ["wx450b396755a432bb"]
						// mch_id: ["1512077821"]
						// nonce_str: ["8k6Whqi5blpiYqtk"]
						// prepay_id: ["wx06161453762074211ec22b0b50f0420000"]
						// result_code: ["SUCCESS"]
						// return_code: ["SUCCESS"]
						// return_msg: ["OK"]
						// sign: ["E1DC10C376DA2031DFE1BAFC1E44E0CE"]
						// trade_type: ["JSAPI"]
						if(res.data.code === 0) {
							// uni.getProvider({
							// 	service:'payment',
							// 	success: (payment) => {
							// 		console.log('pay',payment)
							// 	},
							// 	fail: (fail) => {
									
							// 	}
							// })
							// appId: "wx450b396755a432bb"
							// nonceStr: "mk5vd4jfns"
							// package: "prepay_id=wx061643198937732e289161105960c40000"
							// paySign: "90F0B7C3D08C6F1D0FB443CA1885A2AB"
							// signType: "MD5"
							// timeStamp: "1599381800"
							console.log(res.data.data)
							uni.setStorageSync('out_trade_no',JSON.stringify(res.data.data.out_trade_no))
							let d = res.data.data
							uni.requestPayment({
								provider:'wxpay',
								timeStamp: d.timeStamp,
								nonceStr: d.nonceStr,
								package: d.package,
								signType:d.signType,
								paySign:d.paySign,
								success: (success) => {
									console.log('支付成功',success)
									
								},
								fail: (fail) => {
									console.log('支付失败',fail)
								}
							})
							
						}
					},
					fail: (err) => {
						console.log('支付 err',err)
					}
				})
			},
			getMpOrderInfo() {
				let out_trade_no = uni.getStorageSync('out_trade_no')
				
				out_trade_no = JSON.parse(out_trade_no)
				
				let url  = `http://localhost:7001/api/mp/getMpOrderInfo`
				
				uni.request({
					url,
					method:'GET',
					data: {
						orderId: out_trade_no
					},
					success: (res) => {
						console.log('订单查询 ok',res)
					},
					fail: (err) => {
						console.log('订单查询 err',err)
					}
				})
			}
		}
	}
</script>

<style scoped>
	.m-t {
		margin-top: 20rpx;
	}
</style>
