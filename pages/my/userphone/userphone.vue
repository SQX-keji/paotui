<template>
	<view class="container">
		<view class="wrapper">
			<view class="input-content">
				<view class="cu-form-group" style="display: flex;margin-bottom: 20px;border-radius: 30px">
					<view class="title">用户名：</view>
					<input type="number" :value="phone" placeholder="请输入需要修改的昵称" maxlength="11" data-key="phone"
						@input="inputChange" />
				</view>
				<view class="cu-form-group" style="display: flex;margin-bottom: 20px;border-radius: 30px">
					<view class="title">头像：</view>
					<!-- <u-upload :action="action" :file-list="fileList"></u-upload> -->
					<!-- <view class="img_box" @click="addImages"></view> -->
					<image src="../../../static/my/pict.png" class="img_box" @click="addImages"></image>
				</view>
				<!-- <view class="cu-form-group" style="display: flex;margin-bottom: 20px;border-radius: 30px">
					<view class="title">手机号</view>
					<input type="number" :value="phone" placeholder="请输入新手机号" maxlength="11" data-key="phone" @input="inputChange" />
				</view> -->
				<!-- <view class="cu-form-group" style="display: flex;margin-bottom: 20px;border-radius: 30px">
					<text class="title">验证码</text>
					<input type="number" :value="code" placeholder="请输入验证码" maxlength="6" data-key="code" @input="inputChange"
					 @confirm="toLogin" />
					<button class="send-msg" @click="sendMsg" :disabled="sending">{{sendTime}}</button>
				</view> -->
			</view>
			<button class="confirm-btn" @click="toLogin">保存</button>
		</view>
	</view>
</template>

<script>
	import configdata from '@/common/config.js';
	export default {
		data() {
			return {
				code: '',
				phone: '',
				password: '',
				sending: false,
				sendTime: '获取验证码',
				count: 60,
				logining: false,
				// action: 'http://www.example.com/upload',
				fileList: [{
					url: '',
				}],
				homepageImg:''
			}
		},

		methods: {
			// submit() {
			// 	this.$refs.uUpload.upload();
			// },
			config: function(name) {
				var info = null;
				if (name) {
					var name2 = name.split("."); //字符分割
					if (name2.length > 1) {
						info = configdata[name2[0]][name2[1]] || null;
					} else {
						info = configdata[name] || null;
					}
					if (info == null) {
						let web_config = cache.get("web_config");
						if (web_config) {
							if (name2.length > 1) {
								info = web_config[name2[0]][name2[1]] || null;
							} else {
								info = web_config[name] || null;
							}
						}
					}
				}
				return info;
			},
			// 图片上传
			addImages() {
				uni.chooseImage({
					count: 1,
					sourceType: ['album', 'camera'],
					success: res => {
						for (let i = 0; i < 1; i++) {
							this.$queue.showLoading("上传中...");
							uni.uploadFile({ // 上传接口
								url: this.config("APIHOST1") + '/alioss/upload', //真实的接口地址
								filePath: res.tempFilePaths[i],
								name: 'file',
								success: (uploadFileRes) => {
									this.homepageImg = JSON.parse(uploadFileRes.data).data
									uni.hideLoading();
								}
							});
						}
					}
				})
			},
			sendMsg() {
				const {
					phone
				} = this;
				if (!phone) {
					this.$queue.showToast("请输入手机号");
				} else if (phone.length !== 11) {
					this.$queue.showToast("请输入正确的手机号");
				} else {
					this.$queue.showLoading("正在发送验证码...");
					this.$Request.getT("/msg/sendMsg/" + phone + "/bind").then(res => {
						if (res.status === 0) {
							this.sending = true;
							this.$queue.showToast('验证码发送成功请注意查收');
							this.countDown();
							uni.hideLoading();
						} else {
							uni.hideLoading();
							uni.showModal({
								showCancel: false,
								title: '短信发送失败',
								content: res.msg ? res.msg : '请一分钟后再获取验证码'
							});
						}
					});
				}
			},
			countDown() {
				const {
					count
				} = this;
				if (count === 1) {
					this.count = 60;
					this.sending = false;
					this.sendTime = '获取验证码'
				} else {
					this.count = count - 1;
					this.sending = true;
					this.sendTime = count - 1 + '秒后重新获取';
					setTimeout(this.countDown.bind(this), 1000);
				}
			},
			inputChange(e) {
				const key = e.currentTarget.dataset.key;
				this[key] = e.detail.value;
			},
			navBack() {
				uni.navigateBack();
			},


			navTo(url) {
				uni.navigateTo({
					url
				})
			},
			toLogin() {
				if (this.code == '') {
					this.$queue.showToast("请输入验证码");
				} else {
					let userId = this.$queue.getData("userId");
					const {
						phone,
						password,
						code
					} = this;
					if (!phone) {
						this.$queue.showToast("请输入手机号");
					} else {
						this.logining = true;
						this.$queue.showLoading("加载中...");
						this.$Request.getT("/user/changePhone", {
							userId: userId,
							phone: phone,
							msg: code
						}).then(res => {
							uni.hideLoading();
							if (res.status === 0) {
								uni.navigateTo({
									url: '/pages/my/userstatus'
								});
							} else {
								uni.showModal({
									showCancel: false,
									title: '绑定手机号失败',
									content: res.msg,
								});
							}
						});
					}
				}
			},
		},

	}
</script>

<style lang='scss'>
	page {
		/* background: #1c1b20; */
	}

	.img_box {
		width: 100rpx;
		height: 100rpx;
		background: #ccc;
	}

	.send-msg {
		border-radius: 30px;
		color: white;
		height: 30px;
		font-size: 14px;
		line-height: 30px;
		background: #FF6A04;
	}

	.container {
		padding-top: 32upx;
		position: relative;
		width: 100%;
		height: 100%;
		overflow: hidden;
		/* background: #1c1b20; */
	}

	.wrapper {
		position: relative;
		z-index: 90;
		/* background: #1c1b20; */
		padding-bottom: 20px;
	}


	.input-content {
		padding: 32upx 80upx;
	}


	.confirm-btn {
		width: 600upx;
		height: 80upx;
		line-height: 80upx;
		border-radius: 60upx;
		margin-top: 32upx;
		background: #FF6A04;
		color: #fff;
		font-size: 32upx;

		&:after {
			border-radius: 60px;
		}
	}
</style>
