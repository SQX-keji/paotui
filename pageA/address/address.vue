<template>
	<view style="padding-bottom: 150upx;">
		<!-- #ifdef MP-WEIXIN -->
		<view style="height: max-content;background-color: #FFFFFF;margin-top: 10upx;padding: 30upx 30upx 10upx 40upx;"
			v-for="(item,index) in list" :key='index'>
			<view style="display: flex;" @tap='goBackByAddress(item)'>
				<view style="font-size: 28rpx;color: #333333;">{{item.userName}}</view>
				<view style="font-size: 28rpx;color: #333333;margin-left: 20upx;text-align: right;">{{item.userPhone}}
				</view>
			</view>
			<view style="display: flex;margin-top: 28rpx;">
				<view style="font-size: 28rpx;color: #333333;width: 90%;">
					{{item.address}}{{item.addressDetail}}
				</view>
			</view>
			<view style="margin-top: 15upx;height: 1upx;background-color: #E3E4E5;margin-bottom: 10upx;"></view>
			<view style="display: flex;padding: 10rpx 2rpx 10rpx 0rpx;font-size: 28rpx;">
				<radio-group name="openWay" style="text-align: left;width: 70%;">
					<label class="tui-radio" v-if="item.addressDefault == '1'">
						<radio :checked="item.addressDefault == 1 ? true : false" color="#FF7F00" disabled='true'
							style="transform:scale(0.8);" />
						<text style="font-size: 28rpx;margin-left: 10upx;">默认地址</text>
					</label>
				</radio-group>
				<view style="display: flex;margin-left: 80upx;margin-top: 5upx;width: 40%;text-align: right;">
					<view style="font-size: 28rpx;color: #999999;width: 50%;" @tap='deleteAddressList(item)'>
						删除</view>
					<view style="font-size: 28rpx;color: #999999;width: 50%;" @tap='goAddress(item.addressId)'>编辑</view>
				</view>
			</view>
		</view>
		<view style="position: fixed;bottom: 0rpx;left: 0;right: 0;background: #FFFFFF;height:150rpx;">
			<button style="background: #FF7F00;color: #FFFFFF;margin: 28rpx;position: fixed;bottom: 0upx;width: 90%;"
				@tap="goAddress('')">
				+新建地址
			</button>
		</view>
		<!-- #endif -->
		<!-- #ifndef MP-WEIXIN -->
		<view style="height: max-content;background-color: #FFFFFF;margin-top: 10upx;padding: 30upx 30upx 10upx 40upx;"
			v-for="(item,index) in list" :key='index'>
			<view style="display: flex;" @tap='goBackByAddress(item)'>
				<view style="font-size: 30upx;color: #333333;">{{item.userName}}</view>
				<view style="font-size: 30upx;color: #333333;margin-left: 20upx;text-align: right;">{{item.userPhone}}
				</view>
			</view>
			<view style="display: flex;margin-top: 30upx;height: 70upx;">
				<view style="font-size: 30upx;color: #333333;width: 90%;">
					{{item.address}}{{item.addressDetail}}
				</view>
			</view>
			<view style="margin-top: 30rpx;height: 1upx;background-color: #E3E4E5;margin-bottom: 10upx;"></view>
			<view style="display: flex;padding: 15upx 5upx 15upx 0upx;font-size: 30upx;">
				<radio-group name="openWay" style="text-align: left;width: 70%;">
					<label class="tui-radio" v-if="item.addressDefault == '1'">
						<radio :checked="item.addressDefault == 1 ? true : false" color="#FF7F00" disabled='true'
							style="transform:scale(0.8);" />
						<text style="font-size: 30upx;margin-left: 10upx;">默认地址</text>
					</label>
				</radio-group>
				<view style="display: flex;margin-left: 80upx;margin-top: 5upx;width: 40%;text-align: right;">
					<view style="font-size: 30upx;color: #999999;width: 50%;" @tap='deleteAddressList(item)'>
						删除</view>
					<view style="font-size: 30upx;color: #999999;width: 50%;" @tap='goAddress(item.addressId)'>编辑</view>
				</view>
			</view>
		</view>
		<view style="position: fixed;bottom: 0rpx;left: 0;right: 0;background: #FFFFFF;height:150rpx;">
			<button style="background: #FF7F00;color: #FFFFFF;margin: 30upx;position: fixed;bottom: 0upx;width: 90%;"
				@tap="goAddress('')">
				+新建地址
			</button>
		</view>
		<!-- #endif -->
		<!-- 悬浮上拉 -->
		<view class="scroll_top" @tap="topScrollTap" v-bind:class="[scrollTop ? 'active' : '', '']"
			style="bottom: 56px;"><text class="iconfont icon-shangla"></text></view>
	</view>
</template>

<script>
	export default {
		data() {
			return {
				openWay: 0,
				list: [],
				loadingType: 0,
				type: 1,
				scrollTop: false
			}
		},
		onShow() {
			let userId = this.$queue.getData('userId');
			if (userId) {
				this.getAddressList('');
			}
		},
		onLoad() {
			let userId = this.$queue.getData('userId');
			if (userId) {
				this.getAddressList('');
			}
		},
		methods: {
			goBackByAddress(item) {

				this.$queue.setData('EditAddress', item);
				uni.navigateBack();
			},
			deleteAddressList(e) {
				console.log(e)
				let data = {
					userName: e.userName,
					userPhone: e.userPhone,
					address: e.address,
					addressDetail: e.addressDetail,
					addressLatitude: e.addressLatitude,
					addressLongitude: e.addressLongitude,
					userId: e.userId,
					addressId: e.addressId,
					addressDefault: e.addressDefault
				}
				uni.showModal({
					title: '温馨提示',
					content: '您确定要删除此地址信息吗?',
					showCancel: true,
					cancelText: '取消',
					confirmText: '确认',
					success: res => {
						if (res.confirm) {
							this.$Request.getT('/app/indent/delUserAddress', data).then(res => {
								console.log(res)
								if (res.code == 0) {
									this.$queue.showToast("删除成功！");
									this.getAddressList();
								} else {
									this.$queue.showToast(res.msg);
								}
							});
						}
					}
				});
			},
			getAddressList(type) {
				this.loadingType = 1;
				uni.showLoading({
					title: '加载中...'
				});
				let userId = this.$queue.getData('userId');
				this.$Request.getT('/app/indent/findUserAddress').then(res => {
					console.log(res)
					if (res.code == 0) {
						this.list = []
						res.data.forEach(d => {
							this.list.push(d);
						});
						this.loadingType = 0;
					} else {
						this.loadingType = 2;
					}
					uni.hideLoading();
					if (type === 'Refresh') {
						uni.stopPullDownRefresh(); // 停止刷新
					}
				});
			},
			goAddress(id) {
				uni.navigateTo({
					// url: './EditAddress?id=' + id
					url: '/pageA/address/Endaddress?id=' + id
				});
			},
			tabSlect(item) {
				this.tabIndex = item.id;
			},
			selectWay(id) {
				this.openWay = id;
			},
			topScrollTap: function() {
				uni.pageScrollTo({
					scrollTop: 0,
					duration: 300
				});
			}
		}
	}
</script>

<style lang="less">
	// @import '../../../static/less/index.less';
	// @import '../../../static/css/index.css';

	page {
		background: #F2F2F2;
	}

	.tui-line-cell {
		width: 100%;
		box-sizing: border-box;
		display: flex;
		align-items: center;
		justify-content: space-between;
		border-bottom: 2upx solid #f2f2f2;
		padding: 0 0 16upx 0;
	}

	.tui-title {
		line-height: 32rpx;
		min-width: 120rpx;
		flex-shrink: 0;
	}

	.tui-input {
		font-size: 32rpx;
		color: #333;
		padding-left: 20rpx;
		flex: 1;
	}
</style>
