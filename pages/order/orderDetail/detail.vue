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
				<view class="rider_order" v-if="orderDetails.indentState == 5">待确认</view>
				<view class="rider_order" v-if="orderDetails.indentState == 3">已接单</view>
				<view class="rider_order" v-if="orderDetails.indentState ==4">派送中</view>
				<view class="rider_order" v-if="orderDetails.indentState ==6||orderDetails.indentState ==7">已完成
				</view>
				<!-- 订单提示状态 -->
				<view class="rider_tit" v-if="orderDetails.indentState == 2">请耐心等待骑手接单...</view>
				<view class="rider_tit" v-if="orderDetails.indentState == 0">请及时支付订单，否则将自动取消</view>
				<view class="rider_tit"
					v-if="orderDetails.indentState ==5||orderDetails.indentState ==6||orderDetails.indentState ==7">

					<text v-if="orderDetails.evaluateMessage">评价内容：{{orderDetails.evaluateMessage}}</text>
					<text v-else>写下您的评价感受吧！</text>
				</view>
				<view class="rider_tit"
					v-if="orderDetails.indentState == 1||orderDetails.indentState ==8||orderDetails.indentState ==9||orderDetails.indentState ==10">
					订单已被您取消</view>
				<view class="rider_tit" v-if="orderDetails.indentState == 3||orderDetails.indentState ==4">骑手已接单，尽快为您派送
				</view>

				<!-- 订单按钮状态 -->
				<view style="display: flex;justify-content: flex-end;margin-top: 16upx;margin-right: 20rpx;">
					<view class="btn1" @tap.stop="bindcomment(orderDetails)"
						v-if="orderDetails.indentState ==7||orderDetails.indentState == 6&&!orderDetails.evaluateMessage">
						去评价
					</view>
					<view class="btn1" v-if="orderDetails.indentState == 0||orderDetails.indentState == 2"
						@tap.stop="bindorderOff(orderDetails)">取消订单</view>
					<view class="btn1" @tap.stop="bindconfirm(orderDetails)" v-if="orderDetails.indentState == 5">确认订单
					</view>
					<view class="btn2" @tap.stop="bindorder(orderDetails)"
						v-if="orderDetails.indentState == 1||orderDetails.indentState == 3||orderDetails.indentState ==8||
					orderDetails.indentState ==9||orderDetails.indentState ==10||orderDetails.indentState == 4||orderDetails.indentState == 6">再来一单</view>
					<view class="btn2" v-if="orderDetails.indentState == 0" @tap.stop="bindorderpay(orderDetails)">立即付款
					</view>
				</view>
			</view>
		</view>
		<!-- 购买时间 -->
		<view class="five_box" style="padding: 30rpx 30rpx;font-size: 30upx;">
			<text>预约时间</text>
			<text style="font-weight: bold;">{{orderDetails.sendOutTime}}</text>
		</view>
		
		<map v-if="orderDetails.indentState == 4 && latitude && longitude" id="map" @tap="goMap" style="width: 95%; height: 300rpx;margin: 20rpx auto 0;" :markers="markers" :latitude="latitude" :longitude="longitude"></map>
		
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
					<image src="../../../static/image/mai.png" v-if="orderDetails.indentType == 3"></image>
					<image src="../../../static/image/icon_f.png" v-else></image>
				</view>
				<view class="box_addres">
					<view class="add">
						{{orderDetails.shipAddress?orderDetails.shipAddress:''}}{{orderDetails.shipAddressDetail}}
					</view>
					<view class="num">
						<view class="name" v-if="orderDetails.indentType !=3">
							{{orderDetails.shipUserName}} <text>{{orderDetails.shipUserPhone}}</text>
							<!-- <view class="phone_bd" @click="bindphone(0)">拨打</view> -->
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
					<view class="add">{{orderDetails.deliveryAddress}}{{orderDetails.deilveryAddressDetail}}</view>
					<view class="num">
						<view class="name">
							{{orderDetails.deliveryUserName}}<text>{{orderDetails.deliveryUserPhone}}</text>
							<!-- <view class="phone_bd" @click="bindphone()">联系TA</view> -->
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
					<text v-if="orderDetails.distance">{{orderDetails.distance/1000}}公里</text>
					<text>￥{{orderDetails.indentBasicsMoney}}</text>
				</view>
			</view>
		</view>
		<!-- 联系骑手 -->
		<view class="rider_box"
			v-if="orderDetails.indentState != 0&&orderDetails.indentState != 1&&orderDetails.indentState != 8&&orderDetails.indentState != 2&&orderDetails.indentState != 9&&orderDetails.indentState != 10">
			<view class="flex justify-between align-center padding">
				<view style="font-size: 31rpx;color: black;">联系骑手...</view>
				<image @click="complaint" src="../../../static/image/order/tousu.png"
					style="width: 43rpx;height: 39rpx;" mode=""></image>
			</view>
			<view>
				<u-line color="#F2F2F2" />
			</view>
			<view style="padding: 20rpx 25rpx;display: flex;justify-content: space-between;align-items: center;">
				<view style="display: flex;align-items: center;">
					<view class="rider_left">
						<image :src="orderDetails.avatar"></image>
					</view>
					<view style="margin-left: 10rpx;color: #333333;">
						<view>{{orderDetails.riderNickName}}</view>
						<!-- <view>{{orderDetails.phone1}}</view> -->
					</view>
				</view>
				<view class="phone" @click="bindphone()">联系TA</view>
			</view>
		</view>

		<!-- 订单信息 -->
		<view class="part_six">
			<view class="order_info">
				订单信息
			</view>

			<!-- 备注 -->

			<view class="order_list">
				<view class="order_name">订单编号</view>
				<view class="order_nums flex align-center" @click="copyClick(orderDetails.indentNumber)">{{orderDetails.indentNumber}}
				<image src="../../../static/image/copy.png" style="width: 30upx;height: 30upx;"></image>
				</view>
			</view>
			<view class="order_list">
				<view class="order_name">下单时间</view>
				<view class="order_nums">{{orderDetails.createTime}}</view>
			</view>
			<view class="order_list">
				<view class="order_name">支付方式</view>
				<view class="order_nums">在线支付</view>
			</view>
			<view class="order_list" v-if="orderDetails.itemCode">

				<view class="order_name" style="color: #FF6A04;">收货码</view>
				<view class="order_nums" style="color: #FF6A04;">{{orderDetails.itemCode}}</view>
			</view>
			<view class="order_list"
				v-if="orderDetails.indentType != 4&&orderDetails.indentType != 3&&orderDetails.remarks">
				<view class="order_name">备注</view>
				<view class="order_nums">{{orderDetails.remarks}}</view>
			</view>

		</view>
		<!-- 订单下单详情 -->
		<view class="part_sevens">
			<view class="order_info">
				费用说明
			</view>
			<view class="order_list">
				<view class="order_name">预估跑腿费</view>
				<view class="order_nums">￥{{orderDetails.indentBasicsMoney}}</view>
			</view>
			<view class="order_list" v-if="orderDetails.tip">
				<view class="order_name">小费</view>
				<view class="order_nums">￥{{orderDetails.tip}}</view>
			</view>
			<view class="order_list" v-if="orderDetails.prepayMoney">
				<view class="order_name">预付价格</view>
				<view class="order_nums">￥{{orderDetails.prepayMoney}}</view>
			</view>
			<view class="order_list" v-if="orderDetails.redPacketAmount">
				<view class="order_name">红包</view>
				<view class="order_nums">-￥{{orderDetails.redPacketAmount}}</view>
			</view>
			<view class="order_list" v-if="orderDetails.cargoInsuranceFlag == 0">
				<view class="order_name">物品保价</view>
				<view class="order_nums">￥{{orderDetails.cargoInsurance}}</view>
			</view>
			<view class="order_list">
				<view class="order_name" style="color: black;font-size: 28rpx;">合计</view>
				<view class="order_nums" style="color: red;font-size: 28rpx;">￥{{orderDetails.indentMoney}}</view>
			</view>
		</view>
	</view>
</template>

<script>
	export default {
		data() {
			return {
				orderDetails: '',
				latitude: '',
				longitude: '',
				markers:[], //标记点
			}
		},
		onLoad(data) {
			let that = this
			console.log(data)
			that.indentNumber = data.indentNumber
			if (!data) {
				// that.orderDetails = JSON.stringify(data.data)
			}
			// console.log(that.orderDetails)
			that.userList()

		},
		methods: {
			copyClick(copy) {
				uni.setClipboardData({
					data: copy,
					success: function(res) {
						uni.getClipboardData({
							success: function(res) {
								uni.showToast({
									title: "复制成功",
									icon: 'none',
								});
							},
						});
					},
				});
			},
			getLocation(e) {
				let data = {
					riderUserId: e,
					lat: this.orderDetails.deliveryAddressLatitude,
					lng: this.orderDetails.deliveryAddressLongitude
				}
				this.$Request.getT('/timedtask/selectRiderLocation', data).then(res => {
					if (res.code === 0) {
						
						console.log(res.data,'经纬度')
						this.latitude =  res.data.riderLocation.lat;
						this.longitude = res.data.riderLocation.lng;
						
						this.markers = [{
							id: 1,
							latitude: res.data.riderLocation.lat,
							longitude: res.data.riderLocation.lng,
							iconPath: '../../../static/image/rider.png',
							width: '30',
							height: '30'
						}]
					}
				});
			},
			goMap() {
				uni.navigateTo({
					url: '/pages/order/orderDetail/orderMap?indentNumber=' + this.indentNumber
				})
			},
			// 拨打电话
			bindphone() {
				uni.makePhoneCall({
					phoneNumber: this.orderDetails.phone
				});
			},
			userList() {
				this.$Request.postT('/app/indent/userIndentMessage?indentNumber=' + this.indentNumber).then(res => {
					console.log(res)
					if (res.code == 0) {
						this.orderDetails = res.data
						// this.orderDetails.phone1 = this.orderDetails.phone.substr(0,3) + "****" + this.orderDetails.phone.substr(7)
						this.getLocation(this.orderDetails.riderUserId) 
					}
				});
			},
			// 再来一单
			bindorder(e) {
				console.log(e)
				if (e.indentType == 1 || e.indentType == 2) {
					let index = e.indentType
					uni.navigateTo({
						url: '/pages/Helpsend/Helpsend?indentNumber=' + e.indentNumber + '&index=' + index
					})
				} else if (e.indentType == 3) {
					let index = e.indentType
					let current = e.buyType
					uni.navigateTo({
						url: '/pages/Helppay/Helppay?indentNumber=' + e.indentNumber + '&index=' + index +
							'&current=' + current
					})
				} else if (e.indentType == 4) {
					let index = e.indentType
					uni.navigateTo({
						url: '/pages/Cityservice/Cityservice?indentNumber=' + e.indentNumber + '&index=' + index
					})
				}

			},
			// 去评价
			bindcomment(e) {
				console.log(e)
				uni.navigateTo({
					url: '/pages/order/comments/comments?indentNumber=' + e.indentNumber + '&riderUserId=' + e
						.riderUserId
				})
			},
			// 取消订单
			bindorderOff(e) {
				// console.log(e)this.orderDetails.userFine
				let indentNumber = e.indentNumber
				console.log(indentNumber)
				uni.showModal({
					title: '温馨提示',
					content: '确定取消订单?',
					showCancel: true,
					cancelText: '取消',
					confirmText: '确认',
					success: res => {
						if (res.confirm) {
							this.$Request.postT('/app/indent/userCancleIndent?indentNumber=' + indentNumber)
								.then(res => {
									// console.log(res)
									if (res.code == 0) {
										uni.showToast({
											title: '订单取消成功'
										});
										this.userList()
										// setTimeout(function() {
										// 	uni.navigateBack();
										// }, 1000);
									} else {
										uni.hideLoading();
										uni.showModal({
											showCancel: false,
											title: '订单失败',
											content: res.msg
										});
									}
								});
						}
					}
				});
			},
			//确认订单
			bindconfirm(e) {
				// console.log(e)
				let indentNumber = e.indentNumber
				console.log(indentNumber)
				this.$Request.postT('/app/indent/userDelivery?indentNumber=' + indentNumber).then(res => {
					console.log(res)
					if (res.code == 0) {
						uni.showToast({
							title: '订单确认成功'
						});
						this.userList()
						// setTimeout(function() {
						// 	uni.navigateBack();
						// }, 1000);
					} else {
						uni.hideLoading();
						uni.showModal({
							showCancel: false,
							title: '订单确认失败',
							content: res.msg
						});
					}
				});
			},
			// 立即付款
			bindorderpay(e) {
				console.log(e)
				let indentNumber = e.indentNumber
				uni.navigateTo({
					url: '/pages/order/pay/pay?indentNumber=' + indentNumber
				})
			},
			complaint() {
				uni.navigateTo({
					url: '/pages/order/complaint/complaint?indentNumber=' + this.orderDetails.indentNumber
				})
			}

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
		height: 230rpx;
		background: #ffffff;
		border-radius: 10rpx;
		margin-top: 30rpx;
	}

	.city_box {
		width: 110rpx;
		line-height: 42rpx;
		background: #c4e2ff;
		color: #49A5FF;
		text-align: center;
		font-size: 30rpx;
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
		font-size: 30rpx;
		letter-spacing: 2rpx;
		margin-left: 15rpx;
	}

	.rider_order {
		width: 90%;
		margin: 0 auto;
		font-size: 36rpx;
		font-weight: bold;
		padding-top: 20rpx;
		letter-spacing: 2rpx;
	}

	.rider_tit {
		width: 90%;
		margin: 0 auto;
		color: #999999;
		font-size: 30upx;
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
		border-radius: 10rpx;
		padding: 15rpx 15rpx;

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
		/* flex: 1; */
		width: 10%;
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
		/* flex: 5; */
		width: 85%;
		font-size: 31rpx;
	}

	.add {
		/* color: #333333;
		font-size: 31rpx;
		letter-spacing: 2upx;
		font-weight: bold;
		margin-top: 20upx; */
	}

	.name {
		display: inline;
		font-size: 30rpx;
		color: #999999;
	}

	.name text {
		color: #999999;
		font-size: 30rpx;
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
		font-size: 30rpx;
		letter-spacing: 1rpx;
	}

	.runing_distance {
		/* flex: 1; */
		color: #999999;
		font-size: 30rpx;
		letter-spacing: 1rpx;
		text-align: end;
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
		border-radius: 10rpx;
		font-size: 35rpx;

		display: flex;
		justify-content: space-between;
	}

	.part_five {
		/* height: 85rpx; */
		display: flex;
		justify-content: space-between;
		padding: 20rpx 20rpx;
		border-radius: 10rpx;
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
		/* height: 275rpx; */
		background: #ffffff;
		margin-top: 20rpx;
		border-radius: 10rpx;
		/* margin-bottom: 100rpx; */

	}

	.order_info {
		width: 90%;
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
		background: #FFFFFF;
		border-radius: 10rpx;
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
		font-size: 28rpx;
		line-height: 60upx;
		text-align: center;
		border: 1upx solid #9C9C9C;
		border-radius: 20upx;
		color: #9C9C9C;
		margin-right: 30upx;
	}

	.btn2 {
		width: 170upx;
		line-height: 60upx;
		color: white;
		background: #FF6A04;
		font-size: 22upx;
		text-align: center;
		margin-right: 10rpx;
		border-radius: 20upx;
	}

	.phone_bd {
		display: inline-block;
		background: #FF6A04;
		color: #ffffff;
		padding: 0rpx 15rpx;
		margin-left: 14rpx;
		border-radius: 10rpx;
	}

	.part_seven {
		padding: 20rpx;
		display: flex;
		justify-content: space-between;
		align-items: center;
	}

	.part_sevens {
		width: 95%;
		margin: 0 auto;
		/* height: 275rpx; */
		background: #ffffff;
		margin-top: 20rpx;
		padding-bottom: 10upx;
		border-radius: 10rpx;
		margin-bottom: 100rpx;
	}

	/* 联系骑手 */
	.rider_box {
		width: 95%;
		margin: 0 auto;
		background: #ffffff;
		margin-top: 20rpx;
		border-radius: 10rpx;

	}

	.rider_left image {
		width: 80rpx;
		height: 85rpx;
		border-radius: 60%;
	}

	.phone {
		background: #FD6416;
		color: #ffffff;
		padding: 8rpx 15rpx;
		border-radius: 13rpx;
		font-size: 24rpx;
	}
</style>
