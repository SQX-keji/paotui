<template>
	<view class="approve">
		<view class="approve_from">
			<u-form :model="form" ref="uForm" label-position="top">
				<u-form-item label="真实姓名">
					<u-input v-model="form.name" placeholder="请输入真实姓名" :clearable="clearable" />
				</u-form-item>
				<u-form-item label="证件号码">
					<u-input v-model="form.number" placeholder="请输入证件号码" :clearable="clearable" />
				</u-form-item>
				<!-- #ifndef MP-WEIXIN -->
				<u-form-item label="手机号码">
					<u-input v-model="form.phone" placeholder="请输入手机号码" :clearable="clearable" />
				</u-form-item>
				<!-- #endif -->
			</u-form>
		</view>
		<!-- #ifdef MP-WEIXIN -->
		<view class="tui-line-cell">
			<view class="tui-title">电话号码</view>
			<button style="font-size: 28upx;width: 95%;text-align: center;" open-type="getPhoneNumber"
				@getphonenumber="getPhoneNumber" :hair-line="false" v-if="phone">
				{{phone}}
			</button>
			<button style="font-size: 28upx;width: 95%;text-align: right;background: #FFFFFF;"
				open-type="getPhoneNumber" @getphonenumber="getPhoneNumber" :hair-line="false" v-else>
				获取手机号码
			</button>
		</view>
		<!-- #endif -->
		<view class="approve_submit">
			<view class="btn" @click="bindapp">提交认证</view>
		</view>
	</view>
</template>

<script>
	export default {
		data() {
			return {
				form: {
					name: '',
					number: '',
					// #ifndef MP-WEIXIN
					phone: ''
					// #endif

				},
				phone: '',
				clearable: false,
				userId: '',
				token: ''
			}
		},
		onLoad() {
			// this.bindapp()
			this.userId = this.$queue.getData('userId');
			console.log(this.userId)
		},
		methods: {
			bindapp() {
				let regX = /^[1-9]\d{5}(18|19|([23]\d))\d{2}((0[1-9])|(10|11|12))(([0-2][1-9])|10|20|30|31)\d{3}[0-9Xx]$/;
				let nameReg = /^([\u4e00-\u9fa5]{2,20}|[a-zA-Z.\s]{2,20})$/;
				if (this.form.name == '') {
					uni.showToast({
						title: '请输入真实姓名',
						icon: 'none',
						duration: 2000
					});
					return
				}
				if (!nameReg.test(this.form.name)) {
					uni.showToast({
						title: '请输入真实姓名',
						icon: 'none',
						duration: 2000
					});
					return
				}
				if (this.form.number == '') {
					uni.showToast({
						title: '请输入身份证号码',
						icon: 'none',
						duration: 2000
					});
					return
				}
				if (!regX.test(this.form.number)) {
					uni.showToast({
						title: '请输入正确的身份证号',
						icon: 'none',
						duration: 1000
					})
					return
				}
				// #ifndef MP-WEIXIN
				if (this.form.phone.length != 11) {
					uni.showToast({
						title: '请输入正确手机号码',
						icon: 'none',
						duration: 2000
					});
					return
				} else {
					this.phone = this.form.phone;
				}
				// #endif

				// #ifdef MP-WEIXIN
				if (this.phone == '') {
					uni.showToast({
						title: '请获取手机号',
						icon: 'none',
						duration: 1000
					})
					return
				}
				// #endif

				// if (!this.token) {
				let data = {
					userId: this.userId,
					userName: this.form.name,
					identityCardNumber: this.form.number,
					phone: this.phone
				}
				this.$Request.postJson("/app/userinfo/certification", data).then(res => {
					console.log(res)
					if (res.code == 0) {
						// this.$queue.setData("token", res.token);
						uni.setStorageSync("token",res.token)
						uni.showToast({
							title: '实名认证成功'
						});
						setTimeout(function() {
							uni.navigateBack();
						}, 1000);
					} else {
						uni.hideLoading();
						// uni.showToast({
						// 	title: '认证失败,请输入正确信息',
						// 	content: res.msg
						// });
						uni.showModal({
							showCancel: false,
							title: '实名认证失败',
							content: res.msg
						});
					}
				});
				// } else {
				// 	this.$Request.postJson("/app/userinfo/certification").then(res => {
				// 		console.log(res)
				// 		if (res.code == 0) {
				// 			// this.token = res.token


				// 		} else {

				// 		}
				// 	});
				// }


			},
			//小程序微信登录后获取手机号
			getPhoneNumber: function(e) {
				console.log(e);
				if (e.detail.errMsg == 'getPhoneNumber:fail user deny') {
					console.log('用户拒绝提供手机号');
					return
				} else {
					console.log('用户同意提供手机号');
					console.log(e.detail)
					this.setPhoneByInsert(e.detail.encryptedData, e.detail.iv);
				}
			},
			//小程序微信登录后获取手机号
			setPhoneByInsert(decryptData, iv) {
				let sessionkey = this.$queue.getData('unionid');
				if (!sessionkey) {
					this.$queue.logout();
					uni.navigateTo({
						url: '/pages/public/login'
					});
					return;
				}
				this.$queue.showLoading('授权中...');
				let data = {
					decryptData: decryptData,
					key: sessionkey,
					iv: iv
				};
				this.$Request.postJson('/app/Login/selectPhone', data).then(res => {
					if (res.code == 0) {
						uni.hideLoading();
						this.phone = res.data.phoneNumber;
						// if (this.myphone === '') {
						// 	this.updataPhone();
						// }
					} else {
						uni.hideLoading();
						this.$queue.showToast(res.msg);
						// this.$queue.logout();
						// uni.navigateTo({
						// 	url: '../register'
						// });
					}
				});
			},
			goLogin() {
				uni.navigateTo({
					url: '/pages/public/login'
				});
			},
		}
	}
</script>

<style>
	body {
		background: #F5F5F5;
	}

	button::after {
		border: none;
	}


	.tui-line-cell {
		width: 83%;
		margin: 0 auto;
		display: flex;
		margin-top: 20rpx;
		align-items: center;
		background: #ffffff;
		padding-top: 35rpx;
		/* width: 100%;
		box-sizing: border-box;
		display: flex;
		align-items: center;
		justify-content: space-between;
		border-bottom: 2upx solid #414157;
		padding: 0 0 16upx 0; */
	}

	.tui-title {
		width: 30%;
		font-weight: bold !important;
		letter-spacing: 3rpx !important;
		text-indent: 30rpx !;
		font-size: 31rpx;
		/* line-height: 32rpx;
		min-width: 120rpx;
		flex-shrink: 0; */
	}

	.approve {
		width: 100%;
		background: #FFFFFF;
	}

	.approve_from {
		width: 90%;
		margin: 0 auto;
		background-color: #ffffff;
		/* margin-top: 30rpx; */
	}

	.u-form-item--left__content__label {
		/* font-size: 27rpx !important; */
		font-weight: bold !important;
		letter-spacing: 3rpx !important;
		text-indent: 30rpx !important;
	}

	.u-form-item {
		padding: 10rpx 0 !important;
	}

	.u-input__input {
		margin-left: 30rpx !important;
		font-size: 25rpx !important;
		margin-top: -28rpx !important;
	}

	.btn {
		background: #FF6A04;
		color: white;
		width: 90%;
		margin: 0 auto;
		margin-top: 60rpx;
		height: 85rpx;
		border-radius: 18rpx;
		line-height: 85rpx;
		text-align: center;
		font-size: 33rpx;
		letter-spacing: 2rpx;
	}
</style>
