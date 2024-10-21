<template>
	<view class="order_box">
		<view class="success_box" v-for="(item,index) in list" :key="index" 
		@click="bindsuccess">
			<view class="order_success">
				<view class="order_name">{{item.name}}</view>
				<view class="order_data">{{item.data}}</view>
			</view>
			<u-line color="#E6E6E6" />
			<view class="order_city">
				<view class="city_type">
					<view class="type_name">{{item.city_type}}</view>
					<view class="city_text">{{item.num}}</view>
				</view>
				<view class="city_address">
					<view class="fh_box">
						<view class="fh_image">
							<image :src="item.F_img"></image>
						</view>
						<view class="box">
							<view class="fh_name">{{item.F_address}}</view>
							<view class="fh_type">{{item.F_name}}
								<text>{{item.F_number}}</text>
							</view>
						</view>
					</view>
					<view class="sh_box">
						<view class="sh_image">
							<image :src="item.S_img"></image>
						</view>
						<view class="box">
							<view class="sh_name">{{item.S_address}}</view>
							<view class="sh_type">{{item.S_name}}
								<text>{{item.S_number}}</text>
							</view>
						</view>
					</view>
				</view>
			</view>
			<u-line color="#E6E6E6" />
			<view class="order_btn">
				<view class="btn1">去评价</view>
				<view class="btn2">再来一单</view>
			</view>
		</view>

	</view>
</template>

<script>
	export default {
		data() {
			return {
				list: [{
					id: 1,
					name: '已完成',
					data: '2021-06-12',
					city_type: '同城服务',
					num: '数码',
					F_address: '西安智能大厦',
					F_img: '../static/image/icon_f.png',
					F_name: '翠花',
					F_number: '18758765782',
					S_address: '城市空间',
					S_img: '../static/image/icon_s.png',
					S_name: '熊大',
					S_number: '18758765782',
				}]
			};
		},
		methods:{
			bindsuccess(){
				uni.navigateTo({
					url:'/pages/order/order_success/order_success'
				})
			}
		}
	}
</script>

<style>
	.success_box {
		width: 100%;
	}

	.order_box {
		width: 90%;
		margin: 0 auto;
		height: 420rpx;
		background: #FFFFFF;
		margin-top: 20rpx;
		border-radius: 16px;
	}

	.order_success {
		width: 90%;
		margin: 0 auto;
		display: flex;
		height: 80upx;
	}

	.order_name {
		flex: 1;
		display: flex;
		justify-content: left;
		align-items: center;
		font-weight: bold;
		font-size: 31rpx;
		letter-spacing: 1upx;
	}

	.order_data {
		flex: 1;
		color: #999999;
		display: flex;
		justify-content: flex-end;
		align-items: center;
		font-size: 24rpx;
	}

	.city_type {
		width: 90%;
		margin: 0 auto;
		height: 60upx;
		line-height: 60upx;
		display: flex;
		justify-content: left;
		align-items: center;
	}

	.type_name {
		font-size: 27rpx;
	}

	.city_text {
		width: 70rpx;
		line-height: 36rpx;
		color: #49A5FF;
		background: #c4e2ff;
		text-align: center;
		font-size: 19rpx;
		margin-left: 20rpx;
	}

	.city_address {
		width: 85%;
		margin: 0 auto;
		margin-top: -10rpx;
	}

	/* 发货地址 */
	.fh_box {
		display: flex;
		height: 80upx;
		margin-top: 20upx;
	}

	.fh_image {
		flex: 1;
	}

	.box {
		flex: 11;
	}

	.fh_name {
		font-size: 31rpx;
		font-weight: 600;
		letter-spacing: 2upx;
	}

	.fh_type {
		color: #999999;
		font-size: 22upx;
	}

	.fh_type text {
		margin-left: 20upx;
	}

	/* 送货地址 */
	.sh_box {
		display: flex;
		margin-bottom: 14upx;
	}

	.sh_image {
		flex: 1;
	}

	.sh_name {
		font-size: 31rpx;
		font-weight: 600;
		letter-spacing: 2upx;
	}

	.sh_type {
		color: #999999;
		font-size: 22upx;
	}

	.sh_type text {
		margin-left: 20upx;
	}

	.fh_image image {
		width: 30upx;
		height: 30upx;
	}

	.sh_image image {
		width: 30upx;
		height: 30upx;
	}

	.order_btn {
		display: flex;
		justify-content: flex-end;
		align-items: center;
		height: 80upx;
		margin-top: 8upx;
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
		width: 170upx;
		line-height: 60upx;
		color: white;
		background: #FF6A04;
		font-size: 22upx;
		text-align: center;
		margin-right: 30upx;
		border-radius: 20upx;
	}
</style>
