<template>
	<view class="content">
		<!-- #ifndef MP-WEIXIN  -->
		<view class="head">
			<view class="head_image" @tap.stop="binduser">
				<image :src="image_url?image_url:'../../static/image/logo.png'"></image>
			</view>
			<view style="flex: 4;margin-top: 60rpx;" @click="bindlogin">
				<view class="name">
					<view>{{ mobile ? mobile : '游客' }}</view>
				</view>
			</view>
		</view>
		<!-- #endif -->
		<!-- #ifdef MP-WEIXIN -->
		<view class="head">
			<view class="head_image">
				<image :src="image_url?image_url:'../../static/image/logo.png'" style="border-radius: 10rpx"></image>
			</view>
			<view class="head_name" @click="bindlogin">
				<view class="name">{{ mobile ? mobile : '游客' }}</view>
			</view>
		</view>
		<!-- #endif -->
		<!-- 列表 -->
		<view class="margin padding-lr-sm padding-tb bg-white radius">
			<view class="flex justify-between align-center">
				<view class="text-lg text-bold text-black">我的订单</view>
				<view class="flex text-gray" @click="goSwt(0)">
					<text>立即查看</text>
					<u-icon name="arrow-right" width='11rpx' height='18rpx'></u-icon>
				</view>
			</view>
			<view class="flex justify-around margin-top">
				<view class="text-center" @click="goSwt(1)" style="position: relative;">
					<image src="../../static/image/my/1.png" style="width: 55rpx;height: 55rpx;" mode=""></image>
					<view class="text-sm">待付款</view>
					<view class="weinumber" v-if="order1">{{order1}}</view>
				</view>
				<view class="text-center" @click="goSwt(2)" style="position: relative;">
					<image src="../../static/image/my/2.png" style="width: 55rpx;height: 55rpx;" mode=""></image>
					<view class="text-sm">待接单</view>
					<view class="weinumber" v-if="order2">{{order2}}</view>
				</view>
				<view class="text-center" @click="goSwt(4)" style="position: relative;">
					<image src="../../static/image/my/3.png" style="width: 55rpx;height: 55rpx;" mode=""></image>
					<view class="text-sm">派送中</view>
					<view class="weinumber" v-if="order3">{{order3}}</view>
				</view>
				<view class="text-center" @click="goSwt(5)">
					<image src="../../static/image/my/4.png" style="width: 55rpx;height: 55rpx;" mode=""></image>
					<view class="text-sm">已完成</view>
				</view>
			</view>
		</view>

		<view class="margin padding-lr-sm padding-tb bg-white radius">
			<view class="flex justify-between align-center">
				<view class="text-lg text-bold text-black">推荐工具</view>
			</view>
			<view class="flex flex-wrap">
				<view class="text-center margin-tb-sm" style="width: 25%;"
					@click="goNav('/pages/my/hongbao/hongbao')">
					<image src="../../static/image/my/5.png" style="width: 55rpx;height: 55rpx;" mode=""></image>
					<view class="text-sm">我的红包</view>
				</view>
				<view class="text-center margin-tb-sm" style="width: 25%;"
					@click="goNav('/pages/my/invitationUser')">
					<image src="../../static/image/my/12.png" style="width: 55rpx;height:45rpx;" mode=""></image>
					<view class="text-sm margin-top-xs">分享好友</view>
				</view>
				<!-- #ifdef MP-WEIXIN -->
				<view class="text-center margin-tb-sm" style="width: 25%;"
					@click="goRider()">
					<image src="../../static/image/my/6.png" style="width: 55rpx;height: 55rpx;" mode=""></image>
					<view class="text-sm">接单赚钱</view>
				</view>
				<!-- #endif -->
				
				<view class="text-center margin-tb-sm" style="width: 25%;"
					@click="goNav('/pageA/feedback/feedback')">
					<image src="../../static/image/my/4.png" style="width: 55rpx;height: 55rpx;" mode=""></image>
					<view class="text-sm">意见反馈</view>
				</view>
				
				<view class="text-center margin-tb-sm" style="width: 25%;"
					@click="goNav('/pageA/userXieyi/trainingList')">
					<image src="../../static/image/my/22.png" style="width: 55rpx;height: 55rpx;" mode=""></image>
					<view class="text-sm">用户指南</view>
				</view>
				
				<view class="text-center margin-tb-sm" style="width: 25%;"
					@click="goNav('/pageA/kefu/kefu')">
					<image src="../../static/image/my/8.png" style="width: 55rpx;height: 55rpx;" mode=""></image>
					<view class="text-sm">联系客服</view>
				</view>
				<view class="text-center margin-tb-sm" style="width: 25%;"
					@click="goNav('/pageA/address/address')">
					<image src="../../static/image/my/9.png" style="width: 55rpx;height: 55rpx;" mode=""></image>
					<view class="text-sm">地址管理</view>
				</view>
				<view class="text-center margin-tb-sm" style="width: 25%;"
					@click="goNav('/pages/my/set/set')">
					<image src="../../static/image/my/set.png" style="width: 55rpx;height: 55rpx;" mode=""></image>
					<view class="text-sm">系统设置</view>
				</view>
				
			</view>
		</view>
		
		
	</view>
</template>

<script>
	export default {
		data() {
			return {
				image_url: '../../static/image/logo.png',
				mobile: '',
				checkCertification: -1,
				arr: [],
				showModal: true,
				xcxSelect: '是',

				tuiguang: '',
				tuiguangImg: '',
				appID:'',
				bgImg:'',
				bgurl:'',
				order1:'',
				order2:'',
				order3:'',
				timer:''
			}
		},
		onLoad() {
			this.xcxSelect = uni.getStorageSync('xcxSelect')
			this.$Request.getT('/app/common/type/248').then(res => { //跑腿师傅端微信小程序APPID 248
				if (res.code === 0) {
					this.appID = res.data.value;
				}
			});
			this.$Request.getT('/app/common/type/310').then(res => { // 我的页面活动图片 310
				if (res.code === 0) {
					this.bgImg = res.data.value;
				}
			});
			this.$Request.getT('/app/common/type/311').then(res => { // 我的页面活动跳转地址 311
				if (res.code === 0) {
					this.bgurl = res.data.value;
				}
			});
			
			let token = this.$queue.getData("token");
			if(token){
				this.getZiZhi()
				
				this.timer = setInterval(() => {
				    this.getorder1()
				    this.getorder2()
				    this.getorder3()
				}, 3000);
			}
			
			
		},
		onHide() {
			clearInterval(this.timer)
		},
		onShow() {
			
			let token = this.$queue.getData("token");
			if (token) {
				this.getUserInfo();
				this.getorder1()
				this.getorder2()
				this.getorder3()
			}else{
				this.image_url = '';
				this.mobile =  '';
				this.order1 = ''
				this.order2 = ''
				this.order3 = ''
			}
		},
		onShareAppMessage(res) { //发送给朋友
			return {
				title: this.tuiguang,
				path: '/pages/index/index',
				imageUrl: this.tuiguangImg,
			}
		},
		onShareTimeline(res) { //分享到朋友圈
			return {
				title: this.tuiguang,
				path: '/pages/index/index',
				imageUrl: this.tuiguangImg,
			}
		},
		methods: {
			getorder1(){
				let data={
					page:1,
					limit:1,
					indentState:0
				}
				this.$Request.getT('/app/indent/findUserIndent',data).then(res => {
					if (res.code === 0) {
						this.order1 = res.data.totalCount
					}
					uni.hideLoading()
				});
			},
			getorder2(){
				let data={
					page:1,
					limit:1,
					indentState:2
				}
				this.$Request.getT('/app/indent/findUserIndent',data).then(res => {
					if (res.code === 0) {
						this.order2= res.data.totalCount
					}
					uni.hideLoading()
				});
			},
			getorder3(){
				let data={
					page:1,
					limit:1,
					indentState:4
				}
				this.$Request.getT('/app/indent/findUserIndent',data).then(res => {
					if (res.code === 0) {
						this.order3 = res.data.totalCount
					}
					uni.hideLoading()
				});
			},
			// 分享文案和图片
			getZiZhi() {
				this.$Request.getT('/app/common/type/276').then(res => {
					if (res.code === 0) {
						this.tuiguang = res.data.value;
					}
				});
				this.$Request.getT('/app/common/type/277').then(res => {
					if (res.code === 0) {
						this.tuiguangImg = res.data.value;
					}
				});
			},
			goSwt(e) {
				uni.setStorageSync('current', e)
				setTimeout(function() {
					uni.switchTab({
						url: '/pages/order/order',
					})
				}, 10)
			},
			goRider(){
				let token = this.$queue.getData("token");
				if (token) {
					let that = this
					uni.navigateToMiniProgram({
						appId: that.appID,
						path: '/pages/login/login',
						extraData: {
							'data1': 'test'
						},
						success(res) {
							// 打开成功
							console.log("打开成功")
						}
					})
				} else {
					this.bindlogin();
				}
			},
			goNav(url) {
				
				let token = this.$queue.getData("token");
				if (token) {
					uni.navigateTo({
						url:url
					})
				} else {
					this.bindlogin();
				}
			},
			getUserInfo() {
				this.$Request.getT('/app/userinfo/findUserInfoById').then(res => {
					console.log(res)
					if (res.code == 0) {
						if (parseInt(res.data.checkCertification)) {
							this.checkCertification = parseInt(res.data.checkCertification)
						} else {
							this.checkCertification = -1;
						}
						this.$queue.setData("image_url", res.data.avatar ? res.data.avatar :
							'../../static/image/logo.png');
						this.$queue.setData("userId", res.data.userId);
						this.$queue.setData("invitationCode", res.data.invitationCode?res.data.invitationCode:'0');
						this.$queue.setData("status", res.data.status);
						this.$queue.setData("nickName", res.data.nickName ? res.data.nickName : res
							.data.userName);
						this.image_url = res.data.avatar ? res.data.avatar : '../../static/image/logo.png';
						this.mobile = res.data.nickName ? res.data.nickName : res.data.userName
					}
				});
			},
			bindlogin() {
				let token = this.$queue.getData("token");
				if (!token) {
					uni.navigateTo({
						url: './register'
					})
				}
			},
			bindapprove() {
				let token = this.$queue.getData("token");
				if (token) {
					uni.navigateTo({
						url: '/pages/my/approve/approve'
					})
				} else {
					this.bindlogin();
				}
			},
			binduser() {
				let token = this.$queue.getData("token");
				if (token) {
					// uni.navigateTo({
					// 	url: '/pages/my/userphone/userphone'
					// })
				} else {
					this.bindlogin();
				}
			}
		}
	}
</script>

<style>
	button::after {
		border: none;
		background-color: none;
	}
	
	button {
		position: relative;
		display: block;
		margin-left: auto;
		margin-right: auto;
		padding-left: 0px;
		padding-right: 0px;
		box-sizing: border-box;
		text-decoration: none;
		line-height: 1.35;
		overflow: hidden;
		color: #666666;
		background-color: #fff;
		width: 100%;
		height: 100%;
	}
	body {
		background: #F5F5F5;
	}

	/* #ifndef MP-WEIXIN */
	page {
		background: #F2EDED;
	}

	/* #endif */

	.content {
		width: 100%;
	}

	.btn {
		font-size: 28upx;
		/* width: 95%; */
		text-align: center;
		background: #FFFFFF;
		margin-top: 6rpx;
	}

	.head {
		/* width: 100%; */
		background: #ffffff;
		height: 200rpx;
		display: flex;
		margin: 30rpx;
		border-radius: 16rpx;
	}

	.head_image {
		flex: 1;
		display: flex;
		justify-content: center;
		align-items: center;
	}

	.head_image image {
		width: 90rpx;
		height: 90rpx;
	}

	.head_name {
		flex: 4;
		position: relative;
	}

	.name {
		position: absolute;
		top: 75rpx;
		font-size: 32rpx;
		font-weight: bold;
	}

	.approve {
		position: absolute;
		top: 100rpx;
		font-size: 24rpx;
		color: #999999;
	}

	/* 列表 */
	.use_list {
		width: 100%;
		background: #ffffff;
		margin-top: 20rpx;
	}

	.list_box {
		width: 90%;
		margin: 0 auto;
		display: flex;
		height: 110rpx;
	}

	.box_left {
		flex: 1;
		display: flex;
		justify-content: left;
		align-items: center;
	}

	.box_right {
		flex: 1;
		display: flex;
		justify-content: flex-end;
		align-items: center;
		color: #808080;
	}

	.use_name {
		margin-left: 30rpx;
		font-size: 32rpx;
	}

	.use_image image {
		width: 50rpx;
		height: 50rpx;
	}
	.weinumber{
		width:35upx;
		height:35upx;
		background: red;
		color: #fff;
		border-radius: 35upx;
		position: absolute;
		top: 0upx;
		right: -10upx;
		z-index: 99;
	}
</style>
