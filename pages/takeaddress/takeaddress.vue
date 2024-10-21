<template>
	<view class="takeaddress">
		<view class="part1">
			<view class="name">
				<u-field v-model="name" placeholder="联系人姓名" icon="account-fill" icon-color="#979799" label-align="center">
				</u-field>
			</view>
			<view class="mobile">
				<u-field v-model="mobile" type="number" placeholder="联系电话" icon-color="#979799" icon="phone-fill" label-align="center">
				</u-field>
			</view>
			<view class="address" @click="bindmap">
				<u-field v-model="cityaddress" placeholder="选择地址" :disabled="true" icon-color="#979799" icon="map-fill" label-align="center">
				</u-field>
			</view>
			<view class="detailaddress">
				<u-field v-model="detailaddress" placeholder="详细地址" icon="map-fill" icon-color="#979799" type="textarea"
					label-align="center">
				</u-field>
			</view>
			<view class="btn" @click="bindtake">确定</view>
		</view>
		<view class="part2">
			<view v-if="oldlist.length>0">
				<view class="address1">常用地址</view>
				<view class="address_box" v-for="(item,index) in oldlist" :key="index" @click="goBack(item.addressId)">
					<view class="address_left">
						<u-icon name="map-fill" color="#979799"></u-icon>
					</view>
					<view class="address_right">
						<view class="add">
							{{item.address}}{{item.addressDetail}}
						</view>
						<view class="num">
							<view class="name">{{item.userName}}</view>
							<view class="number">{{item.userPhone}}</view>
						</view>
					</view>
					<view @tap.stop='deleteAddressList(item)' class="dete">删除</view>
				</view>
			</view>

			<!-- <view v-else>
				<view style="width: 50%;margin: 0 auto;">
					<image src="../../static/image/emety.png" style="width: 100%;height: 390rpx;"></image>
				</view>
			</view> -->
		</view>
	</view>
</template>

<script>
	export default {
		data() {
			return {
				mobile: '',
				name: '',
				cityaddress: '',
				detailaddress: '',
				latitude: '',
				longitude: '',
				oldlist: [],
				type: 1,
				addressType: ''
			}
		},
		onLoad(e) {
			console.log(e)
			if (e.index) {
				this.type = e.index
			}
			if (e.addressType) {
				this.addressType = e.addressType
			}
			
		},
		onShow() {
			this.oldAddress()
		},
		methods: {
			bindtake() {
				let that = this
				let address = uni.getStorageSync('takeAddress')
				if (that.mobile.length != 11) {
					uni.showToast({
						title: '请正确输入收货人联系电话',
						icon: 'none',
						duration: 2000
					});
					return;
				}
				if (!/^1[3456789]\d{9}$/.test(that.mobile)) {
					uni.showToast({
						title: '请正确输入收货人联系电话',
						icon: 'none',
						duration: 2000
					});
					return;
				}
				if (that.name == '') {
					uni.showToast({
						title: '请输入取货人姓名',
						icon: 'none',
						duration: 2000
					});
					return;
				}
				if (that.cityaddress == '') {
					uni.showToast({
						title: '请输入取货地址',
						icon: 'none',
						duration: 2000
					});
					return;
				}
				if (that.detailaddress == '') {
					uni.showToast({
						title: '请输入详细地址',
						icon: 'none',
						duration: 2000
					});
					return;
				}
				// uni.setStorageSync('takeAddress', address)
				//取件地址
				console.log(address)
				let userId = that.$queue.getData('userId');
				let data = {
					type: that.type,
					userPhone: that.mobile,
					userName: that.name,
					address: that.cityaddress, ///注释
					addressDetail: that.detailaddress,
					addressLatitude: that.latitude,
					addressLongitude: that.longitude,
					userId: userId
				}
				that.$Request.postJson('/app/indent/addUserAddress', data).then(res => {
					if (res.code == 0) {
						that.$queue.showToast("地址保存成功!");
						// uni.navigateBack();
						setTimeout(function(){
							that.goBack(res.data)
						},500)
					} else {
						that.$queue.showToast(res.msg);
					}
				});
			},
			// 点击地址返回上一页
			goBack(e) {
				console.log(e)
				uni.setStorageSync('addressId',e) 
				uni.setStorageSync('addressType',this.addressType)
				setTimeout(function(){
					uni.navigateBack()
				},10)
			},
			// 常用地址
			bindback(e) {
				console.log(e)
				let address = uni.getStorageSync('takeAddress')
				// console.log(e)
				// this.data1 = e
				let add = {
					addressId: e.addressId,
					shipUserPhone: e.userPhone,
					shipUserName: e.userName,
					shipAddress: e.address, ///注释
					shipAddressDetail: e.addressDetail,
					userId: e.userId,
					shipAddressLatitude: e.addressLatitude,
					shipAddressLongitude: e.addressLongitude
				}
				if (this.type == 1) {
					address.data1 = add
					uni.navigateBack()
					// uni.switchTab({
					// 	url: '/pages/index/index'
					// })
				} else if (this.type == 2) {
					address.data1 = add
					uni.navigateBack()
					// uni.switchTab({
					// 	url: '/pages/index/index'
					// })
				}
				uni.setStorageSync("takeAddress", address)

				// console.log(uni.getStorageSync("takeAddress"))
				// if (this.type == 1) {
				// 	uni.switchTab({
				// 		url: '/pages/index/index'
				// 	})
				// }

			},
			bindmap() {
				var that = this
				// if (that.address == '') {
				uni.chooseLocation({
					success: function(res) {

						console.log('位置名称：' + res);
						// console.log('详细地址：' + res.address);
						// console.log('纬度：' + res.latitude);
						// console.log('经度：' + res.longitude);
						that.detailaddress = res.name
						that.latitude = res.latitude
						that.longitude = res.longitude
						that.shengcheng(res.longitude, res.latitude)
					}
				});
				// }

			},
			shengcheng(longitude, latitude) {
				this.$Request.getT('/app/Login/selectCity?lat=' + latitude + '&lng=' + longitude).then(res => {
					// console.log(res)
					if (res.code === 0) {
						this.cityaddress = res.data.province + res.data.city + res.data.district
						console.log(this.cityaddress)
					}
				});

			},
			// 获取常用地址列表
			oldAddress() {
				this.$Request.getT('/app/indent/findUserAddress').then(res => {
					console.log(res)
					if (res.code == 0) {
						this.oldlist = res.data
					}
				});
			},
			// 删除常用地址
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
									this.oldAddress();
								} else {
									this.$queue.showToast(res.msg);
								}
							});
						}
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
	
	.dete {
		/* flex: 2; */
		width: 15%;
		text-align: center;
		background: #FF7F00;
		height: 60rpx;
		line-height: 60rpx;
		color: #ffffff;
		letter-spacing: 1rpx;
		border-radius: 15rpx;
		margin-right: 20rpx;
	}
	
	.slot-wrap {
		/* display: flex; */
		/* align-items: center; */
		/* 如果您想让slot内容占满整个导航栏的宽度 */
		/* flex: 1; */
		/* 如果您想让slot内容与导航栏左右有空隙 */
		/* padding: 0 30rpx; */
		width: 100%;
		text-align: center;
		margin-left: 100rpx;
		letter-spacing: 2rpx;
		font-weight: bold;
		font-size: 30rpx;
	}
	
	.takeaddress {
		width: 100%;
	}
	
	.part1 {
		width: 100%;
		background: #ffffff;
		margin-top: 24rpx;
		padding-bottom: 40upx;
	}
	
	.btn {
		width: 90%;
		height: 80upx;
		background: #FF7F00;
		border-radius: 14upx;
		margin: 0 auto;
		color: white;
		text-align: center;
		line-height: 80upx;
		letter-spacing: 2upx;
		margin-top: 40upx;
	}
	
	.part2 {
		width: 100%;
		background: #ffffff;
		margin-top: 10upx;
	}
	
	.address1 {
		width: 90%;
		margin: 0 auto;
		font-size: 37rpx;
		font-weight: bold;
		padding-top: 30upx;
	
	}
	
	.address_box {
		display: flex;
		padding-bottom: 30upx;
		padding-top: 30upx;
		width: 100%;
	}
	
	.address_left {
		/* flex: 2; */
		width: 15%;
		display: flex;
		justify-content: center;
		align-items: center;
	}
	
	.address_right {
		/* flex: 10; */
		width: 80%;
		font-size: 31rpx;
		overflow: hidden;
	}
	
	.add {
		/* color: #333333; */
		/* font-size: 31rpx; */
		/* letter-spacing: 1upx; */
	}
	
	.name {
		display: inline;
		font-size: 34rpx;
		color: #999999;
	}
	
	.number {
		display: initial;
		color: #999999;
		font-size: 34rpx;
		margin-left: 30upx;
	}
	.u-icon__icon {
		font-size: 45rpx !important;
	}
	
	.u-field {
		padding: 35rpx 28rpx !important;
	}
	
	.u-label {
		flex: 0 0 42px !important;
	}
	.u-field__input-wrap {
	    font-size: 30rpx !important;
	}
	.u-textarea-class {
	    font-size: 30rpx !important;
	}
</style>
