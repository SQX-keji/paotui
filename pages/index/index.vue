<!-- 开源省钱兄同城跑腿源码，目前只开源用户端V2版本部分核心模块源码提供学习研究，使用uniapp技术，提供学习使用不可商业，商业使用请联系13895585204(同微信)授权
适配支持公众号+APP+H5+小程序，使用Hbuilder导入即可运行 -->
<template>
	<view>
		<view class="margin-lr" v-if="modelSwt == '否'">
			<view class="">
				<swiper class="swiper" autoplay="1500" :indicator-dots="true" :circular='true'
					indicator-active-color="#ffffff" indicator-color="#cccccc">
					<swiper-item class="swiper-wrap" v-for="(item,index) in bannerList" :key='index'
						style="height: 300upx;">
						<image :src="item.imageUrl" style="width: 100%;border-radius: 8upx;height: 300upx;"
							@click="goWeb(item.url)" mode="scaleToFill"></image>
					</swiper-item>
				</swiper>
			</view>

			<view class="flex justify-between align-center bg-white margin-tb padding-lr-sm radius"
				style="width: 100%;height: 100rpx;">
				<image src="../../static/image/index/xinxi.png" style="width: 126rpx;height: 30rpx;" mode=""></image>
				<view class="flex-sub margin-left-sm">
					<swiper class="swiper" autoplay="1500" :vertical='true' style="height: 40rpx;overflow: hidden;">
						<swiper-item class="" v-for="(item,index) in noticeList" :key='index' @click="goWeb1(item.url)">
							<text>{{item.title}}</text>
						</swiper-item>
					</swiper>
				</view>
			</view>
			<view>
				<view class="text-black text-xl text-bold margin-bottom">帮帮跑腿</view>
				<view class="flex justify-between flex-wrap">
					<view class="bg-video margin-tb-xs" v-for="(item,index) in classifyLsit" :key='index'
						@click="goNav(item.url)">
						<image :src="item.imageUrl" mode="" style="width: 340rpx;height: 200rpx;"></image>
						<view class="flex flex-direction justify-end"
							style="position: absolute;top: 0;padding: 30rpx;width: 100%;height: 100%;">
							<view class="text-white text-lg text-bold">{{item.name}}</view>
						</view>
					</view>
				</view>
			</view>

			<!-- 红包 -->
			<view class="hongbao" v-if="HBShow">
				<view style="width: 52%;margin: 0 auto;position: relative;">
					<view @click="HBShow=false"
						style="position: absolute;right: -10rpx;top: -10rpx;font-size: 32rpx;font-weight: bold;">X
					</view>
					<image src="../../static/image/hb_bg.png" class="hb_img"></image>
					<image src="../../static/image/hb_btn.png" class="hb_btn" @click="takemoney()"></image>
				</view>
			</view>
		</view>

		<view class="content" v-if="modelSwt == '是'">
			<!-- #ifndef MP-WEIXIN  -->
			<view class="page-body">
				<view class="page-section page-section-gap">
					<map id="map" style="width: 100%; height: 700px;" :latitude="latitude" :longitude="longitude"
						:markers="covers" :show-location="true">
						<cover-view style="position: fixed;top: 345px;right: 30px;" @click="onToGetLocation()">
							<cover-image class="img-map1" src="../../static/image/dw.png">
							</cover-image>
						</cover-view>
					</map>
				</view>
			</view>

			<!-- #endif -->

			<!-- #ifdef MP-WEIXIN -->
			<view class="page-body">
				<view class="page-section page-section-gap">
					<map id="map" style="width: 100%; height: 700px;" :latitude="latitude" :longitude="longitude"
						:markers="covers" :show-location="true" :customCallout="customCallout">
						<cover-view style="position: fixed;bottom: 430rpx;right: 60rpx;" @click="onToGetLocation()">
							<cover-image class="img-map1" src="../../static/image/dw.png">
							</cover-image>
						</cover-view>
					</map>
				</view>
			</view>
			<!-- #endif -->


			<cover-view class="controls-title">
				<cover-view class="controls-tabs">
					<cover-view v-for="(item,index) in classifyLsit" :key="index" v-if="index<4"
						@tap="change(index,item)" class="atabs_ds">
						<cover-view>{{item.name}}</cover-view>
						<cover-view :class="{btna:count == index}"></cover-view>
					</cover-view>
				</cover-view>

				<!-- 同城服务 -->
				<cover-view class="tabs_box">
					<cover-view class="pay_tit">
						<cover-view class="pay_name">请写明{{modelDet.name }}的内容</cover-view>
						<cover-view class="pay_set">请填写您的具体要求等</cover-view>
						<cover-view class="pay_radius" @click="bindcity(4)">下单</cover-view>
					</cover-view>
				</cover-view>
			</cover-view>

			<cover-view class="hongbao" v-if="HBShow">
				<cover-view style="width: 52%;margin: 0 auto;position: relative;">
					<cover-view @click="HBShow=false"
						style="position: absolute;right: -10rpx;top: -10rpx;font-size: 32rpx;font-weight: bold;">X
					</cover-view>
					<cover-image src="../../static/image/hb_bg.png" class="hb_img"></cover-image>
					<cover-image src="../../static/image/hb_btn.png" class="hb_btn" @click="takemoney()"></cover-image>
				</cover-view>
			</cover-view>
			<cover-view v-else></cover-view>
		</view>
	</view>
</template>

<script>
	export default {
		data() {
			return {
				HBShow: false,
				modelSwt: "",
				modelDet: {},
				id: 0, // 使用 marker点击事件 需要填写id
				title: 'map',
				latitude: '',
				longitude: '',
				iconPath: '../../static/image/location.png',
				covers: [{
					latitude: '',
					longitude: '',
					iconPath: '../../static/image/location.png',
					width: 40,
					height: 40,
					callout: { //自定义标记点上方的气泡窗口 点击有效
						content: '当前附近骑手', //文本
						color: '#ffffff', //文字颜色
						fontSize: 10, //文本大小
						padding: 10, //附近留白
						borderRadius: 2, //边框圆角
						bgColor: '#00c16f', //背景颜色
						display: 'ALWAYS', //常显
					},
				}],
				count: "",
				list: [{
					id: 0,
					name: '帮我送'
				}, {
					id: 1,
					name: '帮我取'
				}, {
					id: 2,
					name: '同城帮买'
				}, {
					id: 3,
					name: '同城服务'
				}],
				current: 0,
				value0: '',
				value1: '',
				value2: '',
				value3: '',
				type: 'text',
				clearable: false,
				riderNumber: '',


				// 用户红包
				newUserFlag: 2,

				token: '',
				bannerList: [],
				noticeList: [],
				classifyLsit: [],

				tuiguang: '',
				tuiguangImg: '',
				arr: [],
				showModal1: true,
				invitationCode:''

			}
		},
		// 开源省钱兄同城跑腿源码，目前只开源用户端V2版本部分核心模块源码提供学习研究，使用uniapp技术，提供学习使用不可商业，商业使用请联系13895585204授权
		// 适配支持公众号+APP+H5+小程序，使用Hbuilder导入即可运行
		onLoad(option) {
			if (option.scene) {
				console.log('邀请人的码：', option.scene)
				this.$queue.setData("userByinvitationId", option.scene);
			}
			// 获取邀请码保存到本地
			if (option.invitation) {
				that.$queue.setData('userByinvitationId', option.invitation);
			}
			this.invitationCode = this.$queue.getData('invitationCode');
			this.getBannerList()
			this.getNoticeList()
			this.getZiZhi()
			let takeAddress = {
				data1: '',
				data2: '',
			}
			let closeAddress = {
				data1: '',
				data2: '',
				data3: '',
				data4: '',
				citydata: ''
			}
			uni.setStorageSync('takeAddress', takeAddress)
			uni.setStorageSync('closeAddress', closeAddress)
			if (uni.getStorageSync('disinfo')) {
				uni.removeStorageSync('disinfo')
			}
		},
		onShow() {
			// 首页是否展示地图
			this.$Request.getT('/app/common/type/275').then(res => {
				if (res.code == 0) {
					if (res.data && res.data.value) {
						this.modelSwt = res.data.value
						this.$queue.setData('modelSwt', res.data.value)
					}
				}
			});
			this.token = uni.getStorageSync('token')
			if (this.token) {
				this.getUser()
			}


			this.$Request.getT('/app/common/type/307').then(res => { //用户端骑手取消订单通知 307
				if (res.code == 0) {
					if (res.data && res.data.value) {
						this.arr.push(res.data.value)
					}
				}
			})
			if (this.showModal1) {
				// #ifdef MP-WEIXIN
				this.openMsg()
				// #endif
			}
		},
		onReady() {
			this.map = uni.createMapContext("map", this)
		},
		onShareAppMessage(res) { //发送给朋友
			return {
				title: this.tuiguang,
				path: '/pages/index/index?invitation='+this.invitationCode,
				imageUrl: this.tuiguangImg,
			}
		},
		onShareTimeline(res) { //分享到朋友圈
			return {
				title: this.tuiguang,
				path: '/pages/index/index?invitation='+this.invitationCode,
				imageUrl: this.tuiguangImg,
			}
		},
		methods: {
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
			goWeb(url) {
				console.log(url.indexOf('/pages/') !== -1)
				return
				if (url.indexOf('/pages/') !== -1) {
					uni.navigateTo({
						url
					});
				} else {
					//#ifndef H5
					uni.navigateTo({
						url: '/pages/index/webView?url=' + url
					});
					//#endif
					//#ifdef H5
					window.location.href = url;
					//#endif
				}
			},
			goWeb1(url) {
				if (url.indexOf('http') !== -1) {
					//#ifndef H5
					uni.navigateTo({
						url: '/pages/index/webView?url=' + url
					});
					//#endif
					//#ifdef H5
					window.location.href = url;
					//#endif
				}
			},
			// 跳转发布页面
			goNav(e) {
				if (this.token) {
					uni.navigateTo({
						url: e
					})
				} else {
					uni.navigateTo({
						url: '/pages/my/register'
					})
				}

			},
			// 获取轮播图
			getBannerList() {
				this.$Request.get("/app/banner/selectBannerList?classify=1&state=1").then(res => {
					if (res.code == 0) {
						this.bannerList = res.data
					}
				});
				this.$Request.get("/app/banner/selectBannerList?classify=4&state=1").then(res => {
					if (res.code == 0) {
						this.classifyLsit = res.data
						this.modelDet = this.classifyLsit[0]
					}
				});
			},
			// 获取公告
			getNoticeList() {
				let data = {
					page: 1,
					limit: 100
				}
				this.$Request.get("/app/notice/selectNoticeList", data).then(res => {
					if (res.code == 0) {
						this.noticeList = res.data.list
					}
				});
			},

			// 获取用户当前定位
			getUser() {
				console.log('执行')
				var that = this
				uni.getLocation({
					type: 'wgs84',
					success: function(res) {
						// console.log('成功')
						// console.log(res)
						console.log('当前位置的经度：' + res.longitude);
						console.log('当前位置的纬度：' + res.latitude);
						that.longitude = res.longitude
						that.latitude = res.latitude


						that.covers[0].longitude = res.longitude
						that.covers[0].latitude = res.latitude
						that.getAdd(that.longitude, that.latitude)
						that.getuserinfo()
					},
					fail: function(err) {
						console.log('授权失败', err)
						uni.showToast({
							title: "获取位置授权失败",
							icon: "none"
						})
					}
				});

			},
			//右下角定位按钮的点击事件
			onToGetLocation() {
				console.log('11')
				// this.	getUser() 
				// this.map.moveToLocation(); 
				let mapContext = uni.createMapContext('map');
				mapContext.moveToLocation(); //moveToLocation将地图中心移动到当前定位点，需要配合map组件的show-location使用


			},
			// 跑腿人位置
			getAdd(lng, lat) {
				this.$Request.getT('/app/indent/find5KmRider?lng=' + lng + '&lat=' + lat).then(res => {
					// console.log('```````````````', res)
					if (res.code == 0) {
						this.riderNumber = res.data.length
						if (res.data.length > 0) {
							var arr = []

							for (var i in res.data) {
								var obj = {}
								obj.latitude = res.data[i].stationLat
								obj.longitude = res.data[i].stationLng
								obj.iconPath = '../../static/image/rider.png'
								obj.width = 30
								obj.height = 30
								arr.push(obj)
							}
							this.covers[0].callout.content = '当前附近骑手' + res.data.length + '人'
							// this.covers = arr
							this.covers = [...this.covers, ...arr];
							// console.log('this.covers ', this.covers)

						}
					}
				});
			},
			change(index, item) {
				// console.log(index)
				this.modelDet = item
				this.current = index;
				this.count = index;
				console.log(this.count)
				// uni.removeStorageSync('takeAddress')
			},
			bindHelppay(index) {
				// console.log(index)
				let token = this.$queue.getData("token");
				if (token) {
					uni.navigateTo({
						url: '/pages/Helppay/Helppay'
					})
				} else {
					uni.navigateTo({
						url: '/pages/my/register'
					})
				}

			},
			bindcity(index) {
				// console.log(index)
				let token = this.$queue.getData("token");
				if (token) {
					uni.navigateTo({
						url: this.modelDet.url
					})
				} else {
					uni.navigateTo({
						url: '/pages/my/register'
					})
				}

			},
			bindtake(index) {
				// console.log(index)
				let token = this.$queue.getData("token");
				if (token) {
					if (this.longitude == '') {
						this.getUser()
					} else {
						uni.navigateTo({
							url: '/pages/takeaddress/takeaddress?index=' + index
						})
					}
				} else {
					uni.navigateTo({
						url: '/pages/my/register'
					})
				}


			},
			bindclose(index) {
				// console.log(index)
				let token = this.$queue.getData("token");
				if (token) {
					uni.navigateTo({
						url: '/pages/closeaddress/closeaddress?index=' + index
					})
				} else {
					uni.navigateTo({
						url: '/pages/my/register'
					})
				}

			},
			// 获取用户信息
			getuserinfo() {
				this.$Request.getT('/app/userinfo/findUserInfoById').then(res => {
					console.log(res)
					if (res.code == 0) {
						this.newUserFlag = res.data.newUserFlag
						console.log(this.newUserFlag)
						if (this.newUserFlag == 1) {
							this.HBShow = true
						} else {
							this.HBShow = false
						}
					}
				});
			},
			// 红包
			takemoney() {
				this.$Request.getT('/app/userinfo/getNewUserRedPacket').then(res => {
					console.log(res)
					if (res.code === 0) {
						this.HBShow = false
						this.getuserinfo()
						setTimeout(function() {
							uni.navigateTo({
								url: '/pages/my/hongbao/hongbao'
							})
						}, 100)
					} else {
						uni.showToast({
							title: res.msg,
							icon: "none"
						})
						this.newUserFlag = ''
					}
				});
			},
			// 开启订阅消息
			openMsg() {
				console.log('订阅消息')
				var that = this
				uni.getSetting({
					withSubscriptions: true, //是否获取用户订阅消息的订阅状态，默认false不返回
					success(ret) {
						console.log(ret.subscriptionsSetting, '------------------')
						// if (ret.subscriptionsSetting.itemSettings && Object.keys(ret.subscriptionsSetting.itemSettings).length == 2) {
						if (ret.subscriptionsSetting.itemSettings) {
							uni.setStorageSync('sendMsg', true)
							uni.openSetting({ // 打开设置页 
								success(rea) {
									console.log(rea.authSetting)
								}
							});
						} else { // 用户没有点击“总是保持以上，不再询问”则每次都会调起订阅消息
							console.log(99999)
							uni.setStorageSync('sendMsg', false)
							uni.showModal({
								title: '提示',
								content: '为了更好的体验,请绑定消息推送',
								confirmText: '确定',
								cancelText: '取消',
								success: function(res) {
									if (res.confirm) {
										console.log(that.arr)
										wx.requestSubscribeMessage({
											tmplIds: that.arr,
											success(re) {
												console.log(JSON.stringify(re),
													'++++++++++++++')
												var datas = JSON.stringify(re);
												if (datas.indexOf("accept") != -1) {
													console.log(re)
													// uni.setStorageSync('sendMsg', true)
												}
											},
											fail: (res) => {
												console.log(res)
											}
										})
										// uni.setStorageSync('sendMsg', true)
										console.log('确认')
										that.showModal1 = false
									} else if (res.cancel) {
										console.log('取消')
										// uni.setStorageSync('sendMsg', false)
										that.showModal1 = true
									}
								}
							})
						}
					}
				})
			},
		}
	}
</script>

<style>
	.content {
		width: 100%;
		position: fixed;
		top: 0upx;
		left: 0upx;
		right: 0upx;
		bottom: 0upx;

	}

	.controls-title {
		width: 90%;
		height: 370upx;
		background: #FFFFFF;
		position: fixed;
		bottom: 0rpx;
		margin: 40upx;
		border-radius: 26upx;
		box-shadow: 0upx 30upx 40upx 0upx rgba(187, 170, 163, 0.20);
		/* #ifndef MP-WEIXIN */
		width: 90%;
		margin: 0 40rpx;
		margin-bottom: 150rpx;
		/* #endif */
	}

	/* tab选项卡 */
	.controls-tabs {
		display: flex;
		font-size: 33rpx;
		overflow-x: auto;
	}

	.atabs_ds {
		flex-grow: 1;
		text-align: center;
		height: 55rpx;
	}

	/* .tabs_box {
			display: none;
			background: #C8C7CC;
		} */

	.btna {
		background: #FF6A04;
		width: 68%;
		height: 8rpx;
		margin: 9rpx 10rpx 10rpx 29rpx;
		border-radius: 35rpx;
	}

	.tabs_box {
		/* display: none; */
	}

	.dis {
		display: block;
	}


	.controls-tabs {
		width: 100%;
		height: 90rpx;
		display: flex;
		justify-content: center;
		align-items: center;
		margin-top: 10rpx;
	}

	.box_bg {
		width: 90%;
		height: 100upx;
		background: #FFFFFF;
		margin: 0 auto;
		border-radius: 12upx;
		display: flex;
		margin-top: 20upx;
	}

	.box {
		width: 90%;
		height: 100upx;
		background: #F5F5F5;
		margin: 0 auto;
		border-radius: 12upx;
		display: flex;
		margin-top: 20upx;
	}

	.box_dian {
		flex: 1;
		display: flex;
		justify-content: center;
		align-items: center;

	}

	.box_dian cover-image {
		width: 20upx;
		height: 20upx;
	}

	.box_name {
		flex: 5;
		display: flex;
		justify-content: left;
		align-items: center;
		color: #333333;
		font-weight: 700;
		font-size: 34rpx;
	}

	.box_addres {
		flex: 5;

	}

	.add {
		color: #333333;
		font-size: 26upx;
		letter-spacing: 2upx;
		font-weight: bold;
		line-height: 50upx;
	}

	.name {
		display: inline;
		font-size: 24upx;
		color: #999999;
	}

	.number {
		display: initial;
		color: #999999;
		font-size: 24upx;
		margin-left: 30upx;
	}

	.box_image {
		flex: 1;
		display: flex;
		justify-content: center;
		align-items: center;
	}

	.icon_you {
		color: #CCCCCC !important;
	}

	/* 同城购买 */
	.pay_tit {
		width: 90%;
		margin: 0 auto;
		height: 200upx;
		position: relative;
	}

	.pay_name {
		font-size: 31rpx;
		font-weight: bold;
		width: 95%;
		margin: 0 auto;
		letter-spacing: 2upx;
	}

	.pay_set {
		color: #333333;
		font-size: 28rpx;
		width: 95%;
		margin: 0 auto;
		margin-top: 20upx;
	}

	.pay_radius {
		width: 100upx;
		height: 100upx;
		background: #FF7F00;
		color: white;
		text-align: center;
		line-height: 100upx;
		border-radius: 68upx;
		position: absolute;
		right: 20upx;

	}

	.hongbao {
		width: 100%;
		/* height: 100px; */
		/* background: #007AFF; */
		position: fixed;
		top: 24%;
		/* bottom: 50%; */
		left: 0rpx;
		right: 0rpx;
		/* display: none; */
	}

	.hb_img {
		width: 100%;
		height: 435rpx;
	}

	.hb_btn {
		width: 60%;
		height: 72rpx;
		position: absolute;
		top: 315rpx;
		left: 80rpx;
	}

	.img-map1 {
		width: 80rpx;
		height: 80rpx;
	}
</style>
