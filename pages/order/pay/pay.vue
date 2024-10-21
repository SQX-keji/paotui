<template>
	<view class="order_details">
		<!-- 待支付 -->
		<view class="part_one">
			<view>
				<!-- 订单状态 -->
				<view class="rider_order" v-if="orderDetails.indentState == 0">待付款</view>
				<view class="rider_order"
					v-if="orderDetails.indentState == 1||orderDetails.indentState ==8||orderDetails.indentState ==9||orderDetails.indentState ==10">
					已取消</view>
				<view class="rider_order" v-if="orderDetails.indentState == 2">待接单</view>
				<view class="rider_order" v-if="orderDetails.indentState == 3||orderDetails.indentState ==4">派送中</view>
				<view class="rider_order"
					v-if="orderDetails.indentState ==5||orderDetails.indentState ==6||orderDetails.indentState ==7">已完成
				</view>
				<!-- 订单提示状态 -->
				<view class="rider_tit" v-if="orderDetails.indentState == 2">请耐心等待骑手接单...</view>
				<view class="rider_tit" v-if="orderDetails.indentState == 0">请及时支付订单，否则将自动取消</view>
				<view class="rider_tit"
					v-if="orderDetails.indentState ==5||orderDetails.indentState ==6||orderDetails.indentState ==7">
					写下您的评价感受吧！</view>
				<view class="rider_tit"
					v-if="orderDetails.indentState == 1||orderDetails.indentState ==8||orderDetails.indentState ==9||orderDetails.indentState ==10">
					订单已被您取消</view>
				<view class="rider_tit" v-if="orderDetails.indentState == 3||orderDetails.indentState ==4">骑手已接单，尽快为您派送
				</view>

				<!-- 订单按钮状态 -->
				<!-- <view style="display: flex;justify-content: flex-end;margin-top: 30rpx;margin-right: 20rpx;">
					<view class="btn1" @tap.stop="bindcomment(item)"
						v-if="orderDetails.indentState == 5||orderDetails.indentState ==7">
						去评价
					</view>
					<view class="btn1" v-if="orderDetails.indentState == 0||orderDetails.indentState == 2"
						@tap.stop="bindorderOff(orderDetails.indentNumber)">取消订单</view>
					<view class="btn1" @tap.stop="bindconfirm(item)" v-if="orderDetails.indentState == 6">确认订单</view>
					<view class="btn2" @tap.stop="bindorder(item)" v-if="orderDetails.indentState == 1||orderDetails.indentState == 3||orderDetails.indentState == 5||orderDetails.indentState ==8||
					orderDetails.indentState ==9||orderDetails.indentState ==10||orderDetails.indentState == 4">再来一单</view>
					<view class="btn2" v-if="orderDetails.indentState == 0" @tap.stop="bindorder(item)">立即付款</view>
				</view> -->
			</view>

		</view>
		<!-- 购买时间 -->
		<view class="five_box" style="padding: 30rpx 30rpx;">
			<text>预约时间</text>
			<text style="font-weight: bold;">{{orderDetails.sendOutTime}}</text>
		</view>

		<!-- 同城购买-->
		<view class="part_three" v-if="orderDetails.indentType == 3">
			<view class="city_pay">
				<view class="city_box">同城购买</view>
				<text v-if="orderDetails.buyType == 0">就近购买</text>
				<text v-else>指定购买</text>
			</view>
			<view class="pay_tit">{{orderDetails.productDetails}}</view>
		</view>
		<!-- 同城服务-->
		<view class="part_three" v-if="orderDetails.indentType == 4">
			<view class="city_pay">
				<view class="city_box">同城服务</view>
				<text>{{orderDetails.serviceType}}</text>
			</view>
			<view class="pay_tit">{{orderDetails.serviceDetails}}</view>
		</view>
		<!-- 骑手商家地址 -->
		<view class="part_four">
			<view class="city_pay" v-if="orderDetails.indentType !=3&&orderDetails.indentType !=4">
				<view class="city_box" v-if="orderDetails.indentType == 1">帮我送</view>
				<view class="city_box" v-if="orderDetails.indentType == 2">帮我取</view>
				<text>{{orderDetails.itemType}}</text>
			</view>
			<view v-if="orderDetails.indentType !=3&&orderDetails.indentType != 4">
				<u-line color="#F2F2F2" />
			</view>
			<!-- 发货地址 -->
			<view class="one_box" v-if="orderDetails.indentType != 4" style="margin-top: 30rpx;">
				<view class="box_dian">
					<image src="../../../static/image/icon_f.png"></image>
				</view>
				<view class="box_addres">
					<view class="add">{{orderDetails.shipAddressDetail}}</view>
					<view class="num">
						<view class="name" v-if="orderDetails.indentType !=3">
							{{orderDetails.shipUserName}} <text>{{orderDetails.shipUserPhone}}</text>
						</view>
						<view class="name" v-else>暂无信息</view>
					</view>
				</view>
			</view>
			<!-- 收获地址 -->
			<view class="one_box" style="margin-top: 20rpx;margin-bottom: 30rpx;">
				<view class="box_dian">
					<image src="../../../static/image/icon_s.png"></image>
				</view>
				<view class="box_addres">
					<view class="add">{{orderDetails.deilveryAddressDetail}}</view>
					<view class="num">
						<view class="name">
							{{orderDetails.deliveryUserName}}<text>{{orderDetails.deliveryUserPhone}}</text>
						</view>

					</view>
				</view>
			</view>
			<u-line color="#F2F2F2" />
			<view class="address_pay">
				<view class="runing_pay">
					预估跑腿费
				</view>
				<view class="runing_distance">
					<!-- <text v-if="orderDetails.distance">{{orderDetails.distance/1000}}公里</text> -->
					<text>￥{{orderDetails.indentBasicsMoney}}</text>
				</view>
			</view>
		</view>
		<!-- 收货码 -->
		<!-- <view class="five_box"> -->
		<view class="part_five" v-if="orderDetails.itemCode">
			<text>收货码</text>
			<text>{{orderDetails.itemCode}}</text>
		</view>
		<!-- 备注 -->
		<view class="part_five" v-if="orderDetails.remarks">
			<text>备注</text>
			<text>{{orderDetails.remarks}}</text>
		</view>
		<!-- </view> -->

		<!-- 订单信息 -->
		<view class="part_six">
			<view class="order_info">
				订单信息
			</view>
			<view class="order_list">
				<view class="order_name">订单号码</view>
				<view class="order_nums">{{orderDetails.indentNumber}}</view>
			</view>
			<view class="order_list">
				<view class="order_name">下单时间</view>
				<view class="order_nums">{{orderDetails.createTime}}</view>
			</view>
			<view class="order_list">
				<view class="order_name">支付方式</view>
				<view class="order_nums">在线支付</view>
			</view>
		</view>

		<!-- 订单下单详情 -->
		<view class="part_seven">
			<view class="order_info">
				费用明细
			</view>
			<view class="order_list">
				<view class="order_name">预估跑腿费</view>
				<view class="order_nums">¥{{orderDetails.indentBasicsMoney}}</view>
			</view>
			<view class="order_list" v-if="orderDetails.tip">
				<view class="order_name">小费</view>
				<view class="order_nums">¥{{orderDetails.tip}}</view>
			</view>
			<view class="order_list" v-if="orderDetails.redPacketAmount">
				<view class="order_name">红包</view>
				<view class="order_nums">-¥{{orderDetails.redPacketAmount}}</view>
			</view>
			<view class="order_list" v-if="orderDetails.prepayMoney">
				<view class="order_name">预付价格</view>
				<view class="order_nums">¥{{orderDetails.prepayMoney}}</view>
			</view>
			<view class="order_list" v-if="orderDetails.cargoInsurance">
				<view class="order_name">物品保价</view>
				<view class="order_nums">¥{{orderDetails.cargoInsurance}}</view>
			</view>
			<view class="order_list">
				<view class="order_name" style="color: black;font-size: 28rpx;">合计</view>
				<view class="order_nums" style="color: red;font-size: 28rpx;">{{orderDetails.indentMoney}}</view>
			</view>
		</view>

		<!-- <view class="pay_bt">
			<view style="font-size: 32rpx;">价格:￥<text>{{orderDetails.}}</text></view>
			<view class="btn2" @click="pay()">支付</view>
		</view> -->
		<view class="tabbar">
			<view class="tabbar_price">支付:
				<text>￥{{orderDetails.indentMoney}}</text>
			</view>
			<view class="tabbar_btn">
				<view class="but" @click="submit()">提交并支付</view>
			</view>
		</view>
		<u-popup v-model="showpay" mode="bottom" close-icon="close-circle" close-icon-pos="top-right"
			close-icon-color="#8f9298" close-icon-size="50">
			<view class="popup_pay">
				<view class="bg margin-top padding-lr radius">
					<view style="padding: 0 20upx;margin-top: 36rpx;">
						<view
							style="display: flex;height: 100upx;align-items: center;padding: 20upx 0;justify-content: center;"
							v-for="(item,index) in openLists" :key='index'>
							<image :src="item.image" style="width: 55upx;height: 55upx;border-radius: 50upx;">
							</image>
							<view style="font-size: 30upx;margin-left: 20upx;width: 70%;">
								{{item.text}}
							</view>
							<radio-group name="openWay" style="margin-left: 45rpx;" @tap='selectWay(item)'>
								<label class="tui-radio">
									<radio color="#FF7F00" :checked="openWay === item.id ? true : false" />
								</label>
							</radio-group>
						</view>
					</view>
				</view>
				<view class="pay_btns" @click="pay()">确认支付</view>
			</view>
		</u-popup>
	</view>
</template>

<script>
	export default {
		data() {
			return {
				orderDetails: '',
				openLists: [],
				
				showpay: false,
				openWay: 0,
				openLists: [],
			}
		},
		onLoad(data) {
			// #ifdef H5
			let ua = navigator.userAgent.toLowerCase();
			if (ua.indexOf('micromessenger') !== -1) {
				this.openLists = [{
					image: '../../../static/image/icon_weixin.png',
					text: '微信',
					id: 2
				}];
				this.openWay = 2;
			} else {
				this.openLists = [{
					image: '../../../static/image/zhifubao.png',
					text: '支付宝',
					id: 1
				}];
				this.openWay = 2;
			}
			// #endif
			
			// #ifdef APP-PLUS
			this.openLists = [{
				image: '../../../static/image/zhifubao.png',
				text: '支付宝',
				id: 1
			}, {
				image: '../../../static/image/icon_weixin.png',
				text: '微信',
				id: 2
			}];
			this.openWay = 2;
			// #endif
			
			// #ifdef MP-WEIXIN
			this.openLists = [{
				image: '../../../static/image/icon_weixin.png',
				text: '微信',
				id: 2
			}];
			this.openWay = 2;
			// #endif
			console.log(data)
			this.indentNumber = data.indentNumber
			if (!data) {
				this.orderDetails = JSON.stringify(data.data)
			}
			// console.log(this.orderDetails)
			this.userList()
		},
		methods: {
			userList() {
				this.$Request.postT('/app/indent/userIndentMessage?indentNumber=' + this.indentNumber).then(res => {
					console.log(res)
					if (res.code == 0) {
						this.orderDetails = res.data
					}
				});
			},
			submit() {
				// #ifdef MP-WEIXIN
				this.openWay= 2
				this.pay()
				// #endif
				// #ifndef MP-WEIXIN
				this.showpay = true
				// #endif
			},
			selectWay: function(item) {
				this.openWay = item.id;
			},
			pay() {
				// let indentNumber = this.indentNumber
				// console.log(data)
				if (this.openWay == 0) {
					this.$queue.showToast('请选择支付方式!')
					return;
				}
			
				if (this.openWay == 2) {
					// #ifdef MP-WEIXIN
					// 微信小程序支付
					this.$Request.postJson("/app/wxPay/wxPayJsApiOrder?indentNumber=" + this.indentNumber).then(res => {
						console.log(res, '********')
						uni.showLoading({
							title: '支付中...'
						});
						if (res.code == 0) {
							console.log(this.openWay, '支付')
							uni.requestPayment({
								provider: 'wxpay',
								timeStamp: res.data.timestamp,
								nonceStr: res.data.noncestr,
								package: res.data.package,
								signType: res.data.signType,
								paySign: res.data.sign,
								success: function(suc) {
									console.log('success:' + JSON.stringify(suc));
									uni.showToast({
										title: '支付成功',
										icon: 'success'
									})
									uni.setStorageSync('current', 0)
									setTimeout(function() {
										uni.navigateBack()
									}, 10)
									// uni.navigateTo({
									// 	url: '/pages/order/orderDetail/detail?indentNumber=' +indentNumber
									// })
								
								},
								fail: function(err) {
									console.log('fail:' + JSON.stringify(err));
									uni.showToast({
										title: '支付失败',
										icon: 'none'
									})
									uni.switchTab({
										url: '/pages/order/order'
									})
								}
							});
			
						}
					})
					// #endif
					// #ifdef H5
					let ua = navigator.userAgent.toLowerCase();
					if (ua.indexOf('micromessenger') !== -1) { //微信里打开
						this.$Request.postJson('/app/wxPay/wxPayMpOrder?indentNumber=' + this.indentNumber)
							.then(res => {
								console.log(res)
								if (res.code == 0) {
									console.log()
									this.callPay(res.data);
								} else {
									uni.showToast({
										icon: 'none',
										title: '支付失败!'
									});
								}
							});
					} else { //不是微信打开
						this.$Request.postJson('/app/aliPay/payH5Order?indentNumber=' + this.indentNumber)
							.then(
								res => {
									if (res.code == 0) {
										const div = document.createElement('div')
										div.innerHTML = res.data //此处form就是后台返回接收到的数据
										document.body.appendChild(div)
										document.forms[0].submit()
									} else {
										uni.showToast({
											icon: 'none',
											title: '支付失败!'
										});
									}
								});
					}
					// #endif
					
					// #ifdef APP-PLUS
					// 微信APP支付 根据订单id获取支付信息
					console.log(this.indentNumber)
					this.$Request.postT("/app/wxPay/payAppOrder", {
						indentNumber: this.indentNumber
					}).then(ret => {
						console.log(JSON.stringify(ret),'支付信息')
						this.isCheckPay(ret.code, 'wxpay', JSON.stringify(ret.data));
					});
					// #endif
				} else {
					
					// #ifdef H5
					this.$Request.postJson('/app/aliPay/payH5Order?indentNumber=' + this.indentNumber)
						.then(
							res => {
								if (res.code == 0) {
									const div = document.createElement('div')
									div.innerHTML = res.data //此处form就是后台返回接收到的数据
									document.body.appendChild(div)
									document.forms[0].submit()
								} else {
									uni.showToast({
										icon: 'none',
										title: '支付失败!'
									});
								}
							});
					// #endif
					// APP支付宝支付
					// #ifdef APP
					this.$Request.postJson("/app/aliPay/payAppOrder?indentNumber=" + this.indentNumber).then(ret => {
						console.log(ret)
						this.isCheckPay(ret.code, 'alipay', ret.data);
					});
					// #endif
					
				}
			
			},
			callPay: function(response) {
				if (typeof WeixinJSBridge === "undefined") {
					if (document.addEventListener) {
						document.addEventListener('WeixinJSBridgeReady', this.onBridgeReady(response), false);
					} else if (document.attachEvent) {
						document.attachEvent('WeixinJSBridgeReady', this.onBridgeReady(response));
						document.attachEvent('onWeixinJSBridgeReady', this.onBridgeReady(response));
					}
				} else {
					this.onBridgeReady(response);
				}
			},
			onBridgeReady: function(response) {
				let that = this;
				if (!response.package) {
					return;
				}
				WeixinJSBridge.invoke(
					'getBrandWCPayRequest', {
						"appId": response.appid, //公众号名称，由商户传入
						"timeStamp": response.timestamp, //时间戳，自1970年以来的秒数
						"nonceStr": response.noncestr, //随机串
						"package": response.package,
						"signType": response.signType, //微信签名方式：
						"paySign": response.sign //微信签名
					},
					function(res) {
						console.log(res, '/*-/*-/*-')
						if (res.err_msg === "get_brand_wcpay_request:ok") {
							// 使用以上方式判断前端返回,微信团队郑重提示：
							//res.err_msg将在用户支付成功后返回ok，但并不保证它绝对可靠。
							uni.showLoading({
								title: '支付成功'
							});
							setTimeout(function() {
								uni.hideLoading();
								uni.navigateBack()
							}, 1000);
						} else {
							uni.hideLoading();
						}
						WeixinJSBridge.log(response.err_msg);
					}
				);
			},
			isCheckPay(status, name, order) {
				if (status == 0) {
					this.setPayment(name, order);
				} else {
					uni.hideLoading();
					uni.showToast({
						title: '支付信息有误',
						icon: 'none'
					});
				}
			},
			setPayment(name, order) {
				console.log(name,'*-*-*',order)
				uni.requestPayment({
					provider: name,
					orderInfo: order, //微信、支付宝订单数据
					success: function(res) {
						console.log(res)
						uni.hideLoading();
						uni.showLoading({
							title: '支付成功'
						});
						setTimeout(function() {
							uni.navigateBack()
						}, 1000);
					},
					fail: function(err) {
						console.log(err)
						uni.hideLoading();
					},
					complete() {
						uni.hideLoading();
					}
				});
			},
			
		}
	}
</script>

<style>
	body {
		background: #F5F5F5;
	}

	/* #ifndef MP-WEIXIN */
	page {
		background: #F2EDED;
	}

	/* #endif */
	.order_details {
		width: 100%;
	}

	/* 待支付 */
	.part_one {
		width: 95%;
		margin: 0 auto;
		background: #ffffff;
		border-radius: 25rpx;
		margin-top: 30rpx;
		padding-bottom: 24rpx;
	}

	.city_box {
		width: 110rpx;
		line-height: 42rpx;
		background: #c4e2ff;
		color: #49A5FF;
		text-align: center;
		font-size: 24rpx;
		height: 42rpx;
	}

	.city_pay {
		width: 90%;
		margin: 0 auto;
		display: flex;
		height: 80rpx;
		justify-content: left;
		align-items: center;
	}

	.city_pay text {
		font-size: 24rpx;
		letter-spacing: 2rpx;
		margin-left: 15rpx;
	}

	.rider_order {
		width: 90%;
		margin: 0 auto;
		font-size: 34rpx;
		font-weight: bold;
		padding-top: 20rpx;
		letter-spacing: 2rpx;
	}

	.rider_tit {
		width: 90%;
		margin: 0 auto;
		color: #999999;
		font-size: 31rpx;
		margin-top: 10rpx;
		letter-spacing: 2rpx;
	}

	.order_btn {
		display: flex;
		margin-top: 30rpx;
	}

	.close_order {
		flex: 1;
		display: flex;
		justify-content: center;
		align-items: center;
	}

	.close_btn {
		border: 1rpx solid #CCCCCC;
		width: 245rpx;
		height: 70rpx;
		line-height: 70rpx;
		text-align: center;
		border-radius: 75rpx;
		font-size: 31rpx;
		color: #999999;
		letter-spacing: 2rpx;
	}

	.tip_order {
		flex: 1;
		display: flex;
		justify-content: center;
		align-items: center;
	}

	.close_tip {
		width: 245rpx;
		height: 70rpx;
		line-height: 70rpx;
		text-align: center;
		border-radius: 75rpx;
		font-size: 31rpx;
		color: white;
		letter-spacing: 2rpx;
		background: #FF7F00;
	}

	/* 骑手商家地址 */
	.part_four {
		width: 95%;
		margin: 0 auto;
		margin-top: 20rpx;
		background: #FFFFFF;
		/* height: 390rpx; */
		border-radius: 25rpx;
		padding-top: 10rpx;

	}

	.u-line {
		border-bottom-width: 6rpx !important;
	}

	.one_box {
		width: 100%;
		/* height: 100rpx; */
		/* background: #F5F5F5; */
		margin: 0 auto;
		border-radius: 12upx;
		display: flex;

	}

	.box_dian {
		flex: 1;
		display: flex;
		justify-content: center;
		align-items: center;

	}

	.box_dian image {
		width: 45rpx;
		height: 45rpx;
	}

	.box_name {
		flex: 5;
		display: flex;
		justify-content: left;
		align-items: center;
		color: #333333;
		font-weight: 700;
	}

	.box_addres {
		flex: 5;

	}

	.add {
		color: #333333;
		font-size: 31rpx;
		letter-spacing: 2upx;
		font-weight: bold;
		margin-top: 20upx;
	}

	.name {
		display: inline;
		font-size: 22upx;
		color: #999999;
	}

	.name text {
		color: #999999;
		font-size: 22upx;
		margin-left: 30upx;
	}

	.address_pay {
		display: flex;
		justify-content: space-between;
		width: 90%;
		margin: 0 auto;
		height: 80rpx;
		line-height: 80rpx
	}

	.runing_pay {
		/* flex: 1; */
		color: #999999;
		font-size: 24rpx;
		letter-spacing: 1rpx;
	}

	.runing_distance {
		/* flex: 1; */
		color: #999999;
		font-size: 24rpx;
		letter-spacing: 1rpx;
		text-indent: 110rpx;
	}

	.runing_distance text {
		font-size: 31rpx;
		color: black;
		margin-left: 35rpx;
	}

	/* 收货码 */
	.five_box {
		width: 95%;
		margin: 0 auto;
		background: #ffffff;
		margin-top: 20rpx;
		border-radius: 15rpx;
		display: flex;
		justify-content: space-between;
		font-size: 35rpx;
	}

	.part_five {
	display: flex;
	    justify-content: space-between;
	    padding: 20rpx 20rpx;
	    width: 95%;
	    margin: 0 auto;
	    background: #ffffff;
	    margin-top: 20rpx;
	    border-radius: 10rpx;
	    font-size: 35rpx;
	}

	.take_number {
		flex: 1;
		display: flex;
		justify-content: left;
		align-items: center;
		text-indent: 30rpx;
	}

	.numbers {
		flex: 1;
		display: flex;
		justify-content: flex-end;
		align-items: center;
		margin-right: 30rpx;
	}

	/* 订单信息 */
	.part_six {
		width: 95%;
		margin: 0 auto;
		height: 275rpx;
		background: #ffffff;
		margin-top: 20rpx;
		border-radius: 25rpx;
		/* margin-bottom: 100rpx; */
	}

	.part_seven {
		width: 95%;
		margin: 0 auto;
		/* height: 275rpx; */
		background: #ffffff;
		margin-top: 20rpx;
		border-radius: 25rpx;
		margin-bottom: 130rpx;
	}

	.order_info {
		width: 95%;
		margin: 0 auto;
		height: 80rpx;
		line-height: 80rpx;
		font-size: 31rpx;
		font-weight: bold;
		letter-spacing: 3rpx;
	}

	.order_list {
		width: 90%;
		margin: 0 auto;
		display: flex;
		height: 60rpx;

	}

	.order_name {
		flex: 1;
		color: #999999;
		font-size: 29rpx;
		letter-spacing: 2rpx;
		display: flex;
		justify-content: left;
		align-items: center;
	}

	.order_nums {
		flex: 2;
		color: #999999;
		font-size: 28rpx;
		letter-spacing: 2rpx;
		display: flex;
		justify-content: flex-end;
		align-items: center;
	}

	/* 同城服务 */
	.part_three {
		width: 95%;
		margin: 0 auto;
		margin-top: 20rpx;
		/* height: 250rpx; */
		/* min-height:200rpx; */
		background: #FFFFFF;
		border-radius: 25rpx;
		padding-bottom: 30rpx;
	}

	.city_pay {
		width: 90%;
		margin: 0 auto;
		display: flex;
		/* height: 90rpx; */
		justify-content: left;
		align-items: center;
	}

	.city_pay text {
		font-size: 28rpx;
		letter-spacing: 2rpx;
		margin-left: 30rpx;
	}

	.city_box {
		width: 110rpx;
		line-height: 42rpx;
		background: #c4e2ff;
		color: #49A5FF;
		text-align: center;
		font-size: 28rpx;
		height: 42rpx;
	}

	.pay_tit {
		width: 90%;
		margin: 0 auto;
		font-size: 27rpx;
	}

	.btn1 {
		width: 170upx;
		font-size: 22upx;
		line-height: 60upx;
		text-align: center;
		border: 1upx solid #9C9C9C;
		border-radius: 20upx;
		color: #9C9C9C;
		margin-right: 30upx;
	}

	.btn2 {
		width: 195rpx;
		line-height: 70rpx;
		color: white;
		background: #FF6A04;
		font-size: 34rpx;
		text-align: center;
		margin-right: 30rpx;
		border-radius: 20rpx;
	}

	.pay_bt {
		position: fixed;
		bottom: 0;
		left: 0;
		right: 0;
		background: #ffffff;
		height: 110rpx;
		display: flex;
		align-items: center;
		justify-content: space-between;
		padding: 0rpx 40rpx;
	}

	.tabbar {
		width: 100%;
		height: 100upx;
		background: #ffffff;
		position: fixed;
		bottom: 0upx;
		left: 0upx;
		right: 0upx;
		display: flex;
	}

	.tabbar_price {
		flex: 1;
		display: flex;
		justify-content: left;
		align-items: center;
		font-size: 28upx;
		font-weight: bold;
		margin-left: 40upx;
	}

	.tabbar_price text {
		color: #FF3333;
		font-size: 36upx;
		font-weight: 500;
	}

	.tabbar_btn {
		flex: 1;
		display: flex;
		justify-content: center;
		align-items: center;
	}

	.but {
		background: #FF7F00;
		width: 200rpx;
		height: 70rpx;
		text-align: center;
		line-height: 70rpx;
		border-radius: 60rpx;
		color: white;
		font-size: 24rpx;
	}
	
	/* 支付弹框 */
	.popup_pay {
		width: 100%;
	}
	
	.pay_btns {
		width: 90%;
		margin: 0 auto 40rpx;
		text-align: center;
		background: #FF7F00;
		height: 80rpx;
		border-radius: 16rpx;
		color: #ffffff;
		line-height: 80rpx;
		margin-top: 20rpx;
	}
</style>
