<template>
	<view style="width: 100%;">
		<!-- 列表 -->
		<view class="use_list">
			<view class="list_box" v-for="(item,index) in mylist" :key="item.id" @click="bindTo(item.name)">
				<view class="box_left">
					<!-- <view class="use_image">
						<image :src="item.image"></image>
					</view> -->
					<view class="use_name">{{item.name}}</view>
				</view>
				<view class="box_right">
					<u-icon name="arrow-right"></u-icon>
				</view>
			</view>
		</view>
		<view class="login_btn" @click="bindOut()">退出登录</view>
	</view>
</template>

<script>
	export default {
		data() {
			return {
				mylist: [{
					id: 1,
					name: '隐私政策'
				}, {
					id: 2,
					name: '用户协议'
				}],
			}
		},
		methods: {
			bindTo(name) {
				console.log(name)
				let token = this.$queue.getData("token");
				if (token) {
					if (name == '用户协议') {
						uni.navigateTo({
							url:'/pageA/xieyi/xieyi'
						})
					} else if (name == '隐私政策') {
						uni.navigateTo({
							url:'/pageA/mimi/mimi'
						})
					}
				}

			},
			bindOut() {
				uni.showModal({
					title: '退出登录',
					content: '是否退出登录',
					success: function(res) {
						if (res.confirm) {
							uni.clearStorage();
							uni.removeStorageSync("image_url")
							uni.removeStorageSync("userId")
							uni.removeStorageSync("status")
							uni.removeStorageSync("nickName")
							uni.removeStorageSync("token")
							uni.removeStorageSync("mobile")
							// uni.showModal({
							// 	showCancel: false,
							// 	title: '退出登录',
							// 	content: res.msg,
							// });
							uni.navigateBack()
							// uni.navigateBack({
							// 	success: () => {
							// 		let page = getCurrentPages().pop(); //跳转页面成功之后
							// 		if (page) {
							// 			let e = {};
							// 			page.onShow(); //执行上个页面的方法
							// 		};
							// 	}
							// })
							// console.log('用户点击确定');
						} else if (res.cancel) {
							console.log('用户点击取消');
							uni.showModal({
								showCancel: false,
								title: '取消退出登录',
								content: res.msg,
							});
						}
					}
				});
			}
		}
	}
</script>

<style>
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
	}

	.use_image image {
		width: 60rpx;
		height: 60rpx;
	}

	.login_btn {
		width: 90%;
		margin: 0 auto;
		text-align: center;
		background: #FF7F00;
		height: 80rpx;
		border-radius: 16rpx;
		color: #ffffff;
		line-height: 80rpx;
		margin-top: 20rpx;
	}
</style>
