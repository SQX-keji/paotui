<template>
	<view style="padding-bottom: 10upx;">
		<!-- 正文内容 -->
		<view class="contents">
			<view class="part_one">
				<!-- 发货地址 -->
				<view class="one_box" @click="bindtake(1)">
					<view class="box_dian">
						<image src="../../static/image/icon_f.png"></image>
					</view>
					<view class="box_addres" v-if="Object.keys(this.take).length>0">
						<view class="add">{{take.address}}{{take.addressDetail}}</view>
						<view class="num">
							<view class="name">{{take.userName}}</view>
							<view class="number">{{take.userPhone}}</view>
						</view>
					</view>
					<view class="box_addres1 text-df text-right" v-else>请填写发货地址</view>
					<view class="box_image">
						<u-icon name="arrow-right" class="icon_you"></u-icon>

					</view>
				</view>
				<!-- 转换 -->
				<view class="box_jh margin-tb-sm">
					<image src="../../static/image/icon_qh.png"></image>
				</view>
				<!-- 收货地址 -->
				<view class="one_box" @click="bindtake(2)" style="padding-bottom: 10rpx;">
					<view class="box_dian">
						<image src="../../static/image/icon_s.png"></image>
					</view>
					<view class="box_addres" v-if="Object.keys(close).length>0">
						<view class="add">{{close.address}}{{close.addressDetail}}</view>
						<view class="num">
							<view class="name">{{close.userName}}</view>
							<view class="number">{{close.userPhone}}</view>
						</view>
					</view>
					<view class="box_addres1 text-df text-right" v-else>请填写收货地址</view>
					<view class="box_image">
						<u-icon name="arrow-right" class="icon_you"></u-icon>
					</view>
				</view>
				<view style="text-align:right;padding: 10upx;margin-right: 32upx;font-size: 24upx;" v-if="distance">
					预估跑腿距离：
					<text v-if="distance>1000">{{parseFloat(distance/1000).toFixed(1)}}km</text>
					<text v-else>{{distance}}m</text>
				</view>
			</view>

			<view class="part_two">
				<u-form :model="forms" ref="uForm" label-position="left" :label-style="labelstyle">
					
					<u-form-item label="物品信息" right-icon="arrow-right">
						<u-input @click="onshowinfo" v-model="forms.wpinfo" :disabled="true" :clearable="clearable"
							input-align="right" placeholder="请选择物品信息" />
					</u-form-item>
					<u-form-item label="预约时间" right-icon="arrow-right">
						<u-input @click="onshowdata" :clearable="clearable" :disabled="true" v-model="forms.data"
							input-align="right" placeholder="请选择预约时间" />
					</u-form-item>
					<u-form-item label="备注">
						<u-input v-model="forms.remarks" type="textarea" input-align="right" placeholder="物品描述 送件要求"
							maxlength="65" style="margin-right: 30upx;" />
					</u-form-item>
				</u-form>
				<!-- <view class="beizhu">
					<view>备注</view>
					
				</view> -->
			</view>

			<view class="part_three">
				<u-form :model="form" ref="uForm" label-position="left" :label-style="labelstyle">
					<u-form-item label="物品保价" right-icon="arrow-right">
						<u-input v-model="form.price" :clearable="clearable" input-align="right"
							placeholder="未保价物品最高赔付5倍跑腿费" @click="showwp" :disabled="true" />
					</u-form-item>
					<u-form-item label="红包" right-icon="arrow-right">
						<u-input @click="openhongbao" v-model="form.hongbao" :disabled="true" :clearable="clearable"
							input-align="right" placeholder="请选择红包" />
					</u-form-item>
					<u-form-item label="小费" right-icon="arrow-right">
						<u-input v-model="form.tip" type="number" :clearable="clearable" input-align="right"
							placeholder="接单更快 购买更加及时" @input="onKeyInput()" />
					</u-form-item>

				</u-form>
			</view>
			<view class="part_four" style="padding-bottom: 36upx;">
				<view class="take_number">
					<view class="number_name">收货码</view>
					<view class="number_text">骑手送达时，需提供收货码，确保安全签收</view>
				</view>
				<view class="take_number_kg">
					<u-switch v-model="checked" size="40" active-color="#FF7F00" @change="codekg"></u-switch>
				</view>
			</view>
			<view class="part_four" v-if="price">
				<view class="take_number">
					<view class="number_name">预估跑腿费</view>

				</view>
				<view class="take_number_kg">
					¥{{price}}
				</view>
			</view>
			<view class="content_sure">
				<u-checkbox-group>
					<u-checkbox v-model="check" shape="circle" active-color="#FF7F00">同意并接受</u-checkbox>
				</u-checkbox-group>
				<view style="font-size: 25rpx;margin-left: -10rpx;" @click="binduserxieyi()">
					《跑腿代购服务协议》
				</view>
			</view>


			<view class="tabbar">
				<!-- <view class="huibtn"> -->
				<!-- </view> -->
				<view class="paytut">
					<view>
						<view class="tabbarprice">
							<text v-if="totalprice == ''">￥{{price || 0}}</text>
							<text v-else>￥{{totalprice}}</text>
						</view>
						<view class="text-sm" style="margin-left: 35upx;color: #999;" v-if="price != 0"
							@click="moneylist(showmoneylist)">
							费用明细</view>
					</view>
					<view class="tabbarbtn">
						<view class="buts" @click="bindpay()">去下单</view>
					</view>
				</view>
				

			</view>
			<!-- 取件时间弹框 :range="multiSelector"-->
			<u-picker mode="time" v-model="showdata" :default-selector='[0, 0]' cancel-tex="确认" :params="params"
				@confirm="confirm"></u-picker>

			<!-- 费用明细 -->
			<u-popup v-model="showmoneylist" mode="bottom" close-icon="close-circle" close-icon-pos="top-right"
				close-icon-color="#8f9298" close-icon-size="50">
				<view style="border-radius: 0upx 36upx 36upx 0upx; padding-bottom: 50rpx;" v-if="showmoneylist">
					<view style="position: relative;">
						<view class="data_title">费用明细</view>
						<view style="position: absolute;top: 30upx;left: 600upx;z-index: 999999;" @click="jifeixieyi()">
							<view style="font-size: 25rpx;color:#FF7F00;">
								《计费规则》
							</view>
						</view>
					</view>
					<view class="padding-lr">
						<view class="flex align-center justify-between padding-bottom-sm" v-if="jichuprice">
							<view>基础运费</view>
							<view>{{jichuprice}}元</view>
						</view>
						<view class="flex align-center justify-between padding-bottom-sm" v-if="wpInfoValue">
							<view>物品费</view>
							<view>{{wpInfoValue}}元</view>
						</view>
						<view class="flex align-center justify-between padding-bottom-sm" v-if="form.price">
							<view>保价费</view>
							<view>{{form.price}}</view>
						</view>
						<view class="flex align-center justify-between padding-bottom-sm" v-if="dateMoney">
							<view>特殊时段费用</view>
							<view>{{dateMoney}}元</view>
						</view>

						<view class="flex align-center justify-between padding-bottom-sm" style="color:#FF7F00;"
							v-if="form.hongbao">
							<view>优惠券优惠金额</view>
							<view>-{{form.hongbao}}元</view>
						</view>
						<view class="flex align-center justify-between padding-bottom-sm" v-if="form.tip">
							<view>小费</view>
							<view>{{form.tip}}元</view>
						</view>
						<view class="flex align-center justify-between padding-bottom-sm">
							<view>总费用</view>
							<view>
								<text v-if="totalprice == ''">{{price || 0}}元</text>
								<text v-else>{{totalprice}}元</text>
							</view>
						</view>
					</view>
				</view>
			</u-popup>


			<!--  物品信息弹框-->
			<u-popup v-model="showinfo" mode="bottom" close-icon="close-circle" close-icon-pos="top-right"
				close-icon-color="#8f9298" close-icon-size="50">
				<view class="popup_info">
					<view class="data_title">物品信息</view>
					<view class="data_items">
						<view class="item_box">
							<view class="item_name">物品类型</view>
							<view class="item_type">
								<view class="item_btn" v-for="(item,index) in info" :key="index"
									:class="selected_foods1==index?'actclass':''"
									@click="selectFoods(item,index,'物品类型')">{{item}}</view>
							</view>
						</view>
						<view class="item_box ">
							<view class="flex align-center justify-between margin-tb">
								<view class="item_name">物品重量</view>
								<view v-if="wpInfoValue==0">0-25kg,不加价</view>
								<view v-else>加价{{wpInfoValue}}元</view>
							</view>
							<view class="item_type">
								<view class="item_btn" v-for="(item,index) in info2" :key="index"
									:class="selected_foods3==index?'actclass':''"
									@click="selectFoods(item.num,index,'物品重量')">{{item.num}}</view>
							</view>
						</view>
					</view>
					<view class="data_btn">
						<view class="btn" @click="bindinfo">
							确定
						</view>
					</view>
				</view>
			</u-popup>

			<!-- 物品保价弹框 -->
			<!-- <u-select v-model="onshowwp" mode="single-column" :list="listwp" @confirm="wpconfirm"></u-select> -->

			<!-- 红包弹框  closeable="true" -->
			<u-popup v-model="showmoney" mode="bottom" :maskCloseAble="false">
				<view class="popup_money" style="position: relative;">
					<view style="position: absolute;top: 30upx;left: 680upx;z-index: 99999;" @click="closemoney(1)">
						<u-icon name="close-circle" size="48"></u-icon>
					</view>
					<view class="data_title">红包</view>
					<view class="data_select">
						<view class="money_box" v-for="(item,index) in hongbao" :key="index"
							v-if="Number(item.redPacketType)==0||Number(item.redPacketType)==indentType">
							<view class="box_tit">
								<view class="money_name">{{item.redPacketTitle}}</view>
								<view class="money_price">￥{{item.redPacketAmount}}</view>
							</view>
							<view class="money_data">有效期至{{item.expirationTime}}</view>
							<view class="money_line">
								<u-line direction="row" color="#E6E6E6" border-style="dashed" />
							</view>
							<view class="box_bottom">
								<view class="money_use">满{{item.minimumAmount}}元可使用</view>
								<view class="money_btn">
									<view class="lj_use" @click="bindhongindex(item.redPacketId,item.redPacketAmount)">
										立即使用
									</view>
								</view>
							</view>
						</view>
						<!-- 暂无红包 -->
						<view class="image_box" v-if="hongbao.length==0">
							<image src="../../static/image/emety.png" style="width:100%;height: 350rpx;"></image>
							<view style="width: 100%;text-align: center;color: #ccc;">暂无红包</view>
						</view>
					</view>

				</view>
			</u-popup>

			<!-- 物品保价弹框 -->
			<u-popup v-model="onshowwp" mode="bottom" border-radius="24" :maskCloseAble="false">
				<view class="popupwupin" style="position: relative;">
					<view style="position: absolute;top: 30upx;left: 680upx;z-index: 99999;" @click="closemoney(2)">
						<u-icon name="close-circle" size="48"></u-icon>
					</view>
					<view class="data_title1 text-center" style="border-bottom: 1rpx solid #f5f5f5;">物品保价</view>
					<view class="margin-tb text-bold" style="color: #000;">物品价值（元）</view>
					<view class="data_select ">
						<view class="padding-lr" style="background: #f5f5f5;">
							<u-input v-model="baojia" type="digit" placeholder="请输入物品金额" @input="onKeyInputbaojia()" />
						</view>
					</view>
					<view class="margin-tb text-bold" style="color: #000;">保价费（元）</view>
					<view class="data_select margin-top">
						<view class="padding-lr" style="background: #fcf7f1;">
							<u-input v-model="baojiamoney" type="digit" :disabled="true" />
						</view>
					</view>
					<view class="contentsure">
						<u-checkbox-group>
							<u-checkbox v-model="check1" shape="circle" active-color="#FF7F00">同意并接受</u-checkbox>
						</u-checkbox-group>
						<view style="font-size: 25rpx;margin-left: -10rpx;color:#FF7F00;" @click="baojiaxieyi()">
							《保价协议》
						</view>
					</view>
					<view class="surebtn" @click="baojiaNumber()">确定</view>
				</view>
			</u-popup>


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
	</view>

</template>

<script>
	export default {
		data() {
			return {
				jiazhiMoney: 0,
				wupinMoney: 0,
				dateMoney: 0,
				openWay: 0,
				take: {},
				close: {},
				distance: '',
				hongbao: '',
				price: '',
				wpprice: '',
				wpbjprice: '',
				checked: true,
				check: false,
				check1: false,
				mobile: '',
				code: '',
				indentType: 1,
				form: {
					price: '',
					hongbao: '',
					tip: ''
				},
				forms: {
					data: '请选择时间',
					wpinfo: this.wpinfo,
					remarks: ''
				},
				clearable: false,
				showdata: false,
				showinfo: false,
				showmoney: false,
				showpay: false,
				onshowwp: false,
				listwp: [{
					value: '0',
					label: '保价'
				}, {
					value: '1',
					label: '不保价'
				}],
				cargoInsuranceFlag: 1,
				value: '',
				type: 'text',
				autoHeight: true,
				params: {
					// year: true,
					// month: true,
					day: true,
					hour: true,
					minute: true,
					timestamp: true
				},
				info: [],
				info2: [{
					num: '0-25kg'
				}, {
					num: '26-30kg'
				}, {
					num: '31-40kg'
				}, {
					num: '41-50kg'
				}],
				selected_foods1: 0,
				selected_foods3: '',
				itemType: '',
				itemWeight: '0-25kg',
				wpinfo: [],
				labelstyle: {
					whiteSpace: 'nowrap',
					fontWeight: 'bold',
					textIndent: '20rpx'
				},
				indentNumber: '',
				openLists: [],
				totalprice: '',
				itemCodeFlag: 0, //是否打开收货码
				cargoInsurance: 0,
				addressId: '',
				addressType: '',
				price1: '',
				showmoneylist: false, //费用明细弹框
				jichuprice: '',
				wpInfoValue: '',
				baojia: '',
				baojiamoney: 0,
				baojiamoney1: '',
				dataday: '',
				datanine: ''
			}
		},
		onLoad(e) {
			console.log(e)
			this.indentType = e.index
			if (this.indentType == 1) {
				uni.setNavigationBarTitle({
					title: '帮我送'
				})
			} else if (this.indentType == 2) {
				uni.setNavigationBarTitle({
					title: '帮我取'
				})
			}

			//物品类型 334
			this.$Request.getT('/app/common/type/334').then(res => {
				if (res.code == 0) {
					if (res.data && res.data.value) {
						this.info = res.data.value.split(',')
						this.itemType = this.info[0]
					}
				}
			});
			//保价费（每100元） 333
			this.$Request.getT('/app/common/type/333').then(res => {
				if (res.code == 0) {
					if (res.data && res.data.value) {
						this.baojiamoney1 = res.data.value
					}
				}
			});
			// 特殊时间段费用（22:00~23:59）额外加价 335
			this.$Request.getT('/app/common/type/335').then(res => {
				if (res.code == 0) {
					if (res.data && res.data.value) {
						this.dataday = res.data.value
					}
				}
			});
			// 特殊时间段费用（00:00~06:59）额外加价 336
			this.$Request.getT('/app/common/type/336').then(res => {
				if (res.code == 0) {
					if (res.data && res.data.value) {
						this.datanine = res.data.value
					}
				}
			});
			// #ifdef H5
			let ua = navigator.userAgent.toLowerCase();
			if (ua.indexOf('micromessenger') !== -1) {
				this.openLists = [{
					image: '../../static/image/icon_weixin.png',
					text: '微信',
					id: 2
				}];
				this.openWay = 2;
			} else {
				this.openLists = [{
					image: '../../static/image/zhifubao.png',
					text: '支付宝',
					id: 1
				}];
				this.openWay = 2;
			}
			// #endif

			// #ifdef APP-PLUS
			this.openLists = [{
				image: '../../static/image/zhifubao.png',
				text: '支付宝',
				id: 1
			}, {
				image: '../../static/image/icon_weixin.png',
				text: '微信',
				id: 2
			}];
			this.openWay = 2;
			// #endif

			// #ifdef MP-WEIXIN
			this.openLists = [{
				image: '../../static/image/icon_weixin.png',
				text: '微信',
				id: 2
			}];
			this.openWay = 2;
			// #endif
			this.$Request.getT('/app/common/type/319').then(res => { //物品重量0-25Kg 319
				if (res.code == 0) {
					if (res.data && res.data.value) {
						this.wpInfoValue = res.data.value
					}
				}
			});
		},
		onShow() {
			this.addressId = uni.getStorageSync('addressId')
			this.addressType = uni.getStorageSync('addressType')
			if (this.addressId) {
				this.getAddress(this.addressId)
				uni.removeStorageSync('addressType')
				uni.removeStorageSync('addressId')
			}
		},
		methods: {
			baojiaNumber() {
				if (!this.baojiamoney) {
					uni.showToast({
						title: '请填写物品价值',
						icon: 'none',
						duration: 2000
					});
					return
				}
				if (this.jiazhiMoney > 10000) {
					uni.showToast({
						title: '物品价值不能大于10000元',
						icon: 'none',
						duration: 2000
					});
					return
				}
				if (this.check1 == false) {
					uni.showToast({
						title: '请同意保价协议',
						icon: 'none',
						duration: 2000
					});
					return
				}
				this.onshowwp = false
				// this.form.price = '保价';
				// 总价
				this.cargoInsuranceFlag = 0
				this.redPacketId = '';
				this.form.hongbao = '';
				let price1 = (Number(this.dateMoney) + Number(this.baojiamoney) + Number(this.wupinMoney) - Number(this
						.form.hongbao))
					.toFixed(2)

				let totalprice = (Number(price1) + Number(this.form.tip)).toFixed(2)
				if (totalprice >= 0) {
					this.totalprice = totalprice;
					this.price = this.totalprice;
				}
				this.orderjc()
				
			},
			onKeyInputbaojia() {
				var bao = Number(this.baojia);
				let baojia;
				if (bao > 10000) {
					this.$queue.showToast('物品价值最大10000元！')
					this.baojia = 0;
					this.form.price = Number(this.form.price) - Number(this.baojiamoney)
					this.baojiamoney = 0;
					return;
				}
				if (bao % 100 == 0) {
					baojia = bao / 100

				} else {
					baojia = bao / 100
					baojia = baojia + 1
				}
				baojia = Math.trunc(baojia)
				this.jiazhiMoney = Number(baojia);
				this.baojiamoney = Number(baojia) * this.baojiamoney1
				this.form.price = Number(baojia) * this.baojiamoney1 + '元'
				this.orderjc()
			},
			jifeixieyi() {
				this.showmoneylist = false;
				uni.navigateTo({
					url: '/pageA/userXieyi/jifeiXieyi'
				})
			},
			baojiaxieyi() {
				uni.navigateTo({
					url: '/pageA/xieyi/baojia'
				})
			},
			moneylist() {
				this.showmoneylist = !this.showmoneylist
			},
			closemoney(index) {
				console.log(index)
				if (index == 1) {
					this.showmoney = !this.showmoney
				}
				if (index == 2) {
					this.onshowwp = !this.onshowwp;
					this.jiazhiMoney = 0;
					this.baojia = 0;
					this.form.price = Number(this.form.price) - Number(this.baojiamoney)
					this.baojiamoney = 0;
				}
				this.orderjc()
			},
			// 查询地址
			getAddress(e) {
				let data = {
					addressId: e
				}
				this.$Request.getT('/app/indent/findAddressById', data).then(res => {
					if (res.code == 0) {
						if (this.addressType == 1) {
							this.take = res.data
						} else if (this.addressType == 2) {
							this.close = res.data
						}
						console.log(this.take)
						if (Object.keys(this.take).length && Object.keys(this.close).length) {
							this.binddistance()
						}
					}
				});
			},
			// 距离计算
			binddistance() {
				console.log('this.take', this.take, 'this.close:', this.close)
				let ol = this.take.addressLatitude
				let od = this.take.addressLongitude
				let dl = this.close.addressLatitude
				let dd = this.close.addressLongitude
				console.log(this.take)
				console.log('取货纬度:' + ol, '取货经度:' + od, '送货纬度:' + dl, '送货经度:' + dd)
				this.$Request.postT('/app/indent/distance?ol=' + ol + '&od=' + od + '&dl=' + dl + '&dd=' + dd).then(
					res => {
						// console.log(res)
						if (res.code === 0) {
							this.distance = res.data
							// this.orderjc()
							
							console.log(this.distance)
						}
					});
			},

			// 物品保价
			wpconfirm(e) {
				// console.log('报价不保价：',e)
				// return
				this.form.hongbao = ''
				this.form.tip = ''
				this.cargoInsuranceFlag = e[0].value
				this.form.price = e[0].label
				if (this.cargoInsuranceFlag == 0) {
					this.form.price = '保价';
					// 总价
					let price1 = (Number(this.price) - Number(this.form.hongbao)).toFixed(2)
					if (Number(price1) <= 0) {
						price1 = 0.01
					}
					let totalprice = (Number(price1) + Number(this.baojiamoney) + Number(this.form.tip)).toFixed(2)
					if (totalprice >= 0) {
						this.totalprice = totalprice
						this.price1 = totalprice
					} else {
						this.totalprice = 0.01
						this.price1 = 0.01
					}
				} else {
					this.form.price = '不保价';
					let price1 = (Number(this.price) - Number(this.form.hongbao)).toFixed(2)

					let totalprice = (Number(price1) + Number(this.form.tip)).toFixed(2)
					if (totalprice >= 0) {
						this.totalprice = totalprice
						this.price1 = totalprice
					} else {
						this.totalprice = 0.01
						this.price1 = 0.01
					}
				}
				if (this.totalprice <= 0) {
					this.totalprice = 0.01
				}
				this.orderjc()
			},
			// 跑腿用户协议
			binduserxieyi() {
				uni.navigateTo({
					url: '/pageA/userXieyi/userXieyi'
				})
			},
			// switch打开或者关闭时触发，值为true或者false
			// 即使不监听此事件，this.checked此时也会相应的变成true或者false
			// 开关 是否需要收货码
			codekg(e) {
				console.log(e)
				if (e == true) {
					this.itemCodeFlag = 0
				} else if (e == false) {
					this.itemCodeFlag = 1
				}
			},
			// 跳转发货地址
			bindtake(e) {
				uni.navigateTo({
					url: '/pages/takeaddress/takeaddress?addressType=' + e
				})
				this.price = 0;
				this.totalprice = 0;
				this.redPacketId = '';
				this.forms.data = '请选择时间';
				this.dateMoney = 0;
				this.wupinMoney = 0;
				this.wpInfoValue = '';
				this.forms.wpinfo = ''
				this.form.price = ''
				this.form.hongbao = ''
				this.form.tip = ''
			},

			// 跳转收货地址
			// bindtake() {
			// 	uni.navigateTo({
			// 		url: '/pages/closeaddress/closeaddress?index=1'
			// 	})
			// },

			// 选择服务时间
			statusChange(e) {
				console.log(e.month + '-' + e.day + ' ' + e.hour)
				var myDate = new Date();
				if (e.month > myDate.getMonth() + 1) {
					console.log(e.month >= myDate.getMonth() + 1, 1111, e.month)
					this.startTime = e.month + '-' + e.day + ' ' + e.hour
				} else {
					if (e.day > myDate.getDate()) {
						console.log(e.day > myDate.getDate(), 22222)
						this.startTime = e.month + '-' + e.day + ' ' + e
							.hour
					} else {
						if (e.hour - myDate.getHours() >= 1) {
							console.log(e.hour - myDate.getHours() >= 1, 3333)
							this.startTime = e.month + '-' + e.day + ' ' +
								e.hour
						} else {
							uni.showToast({
								title: '选择时间大于当前一小时',
								icon: 'none',
								duration: 1000
							})
						}
					}
				}
				this.orderjc()
				
			},

			confirm(e) {
				let curDate = new Date();
				this.year = curDate.getFullYear();
				this.month = curDate.getMonth();
				this.redPacketId = '';
				this.form.hongbao = '';
				var myDate = new Date();

			

				if (parseInt(curDate) >= parseInt(e.timestamp * 1000)) {
					uni.showToast({
						title: '请选择正确时间',
						icon: 'none'
					})
				} else {
					// this.selTime = e.timestamp
					this.buyTime = this.year + '-' + this.month + '-' + e.day + ' ' + e.hour + ':' + e.minute + ':' + '00'
					this.forms.data = e.day + '号' + e.hour + ':' + e.minute
					// let data = e.hour + ':' + e.minute
					console.log(e.hour >= '22' && e.hour <= '23')
					if (e.hour >= 22) {
						// this.dataday
						let price1 = (Number(this.dataday) + Number(this.baojiamoney) + Number(this.wupinMoney) - Number(
							this.form.hongbao)).toFixed(
							2)
						console.log(price1);
						this.dateMoney = this.dataday;
						let totalprice = (Number(price1) + Number(this.form.tip)).toFixed(2)
						if (totalprice >= 0) {
							this.totalprice = totalprice;
							this.price = this.totalprice;
						}
					} else if (e.hour < 7) {
						// this.datanine
						this.dateMoney = this.datanine;
						let price1 = (Number(this.datanine) + Number(this.baojiamoney) + Number(this.wupinMoney) - Number(
								this.form.hongbao))
							.toFixed(2)

						let totalprice = (Number(price1) + Number(this.form.tip)).toFixed(2)
						if (totalprice >= 0) {
							this.totalprice = totalprice;
							this.price = this.totalprice;
						}
					} else {
						this.dateMoney = 0;
						let price1 = (Number(this.baojiamoney) + Number(this.wupinMoney) - Number(this.form.hongbao))
							.toFixed(2)

						let totalprice = (Number(price1) + Number(this.form.tip)).toFixed(2)
						if (totalprice >= 0) {
							this.totalprice = totalprice;
							this.price = this.totalprice;
						}
					}
					
				}
				this.orderjc()
			},
			onshowdata() {
				this.showdata = true
			},
			showwp() {
				if (!this.forms.wpinfo) {
					uni.showToast({
						title: '请选取物品信息',
						icon: 'none',
						duration: 2000
					});
					return
				}
				this.onshowwp = true
			},
			binddata(e) {
				this.showdata = false
			},

			onshowinfo() {
				if (!this.take.address) {
					uni.showToast({
						title: '请选择发货地址',
						icon: 'none',
						duration: 2000
					});
					return
				}
				if (!this.close.address) {
					uni.showToast({
						title: '请选择收货地址',
						icon: 'none',
						duration: 2000
					});
					return
				}
				this.showinfo = true
			},
			bindinfo() {
				this.showinfo = false
				// this.form.price = ''
				// this.form.hongbao = ''
				// this.form.tip = ''

				let wpinfo = {
					itemType: this.itemType,
					itemWeight: this.itemWeight
				}
				uni.setStorageSync('wpinfo', wpinfo)
				this.wpinfo = uni.getStorageSync('wpinfo')
				// console.log(this.wpinfo)
				this.forms.wpinfo = wpinfo.itemType + wpinfo.itemWeight

				this.orderjc()

			},
			selectFoods(e, i, name) {
				// console.log(e,i)
				if (name == '物品类型') {
					this.selected_foods1 = i
					this.itemType = e

				}
				if (name == "物品重量") {
					this.selected_foods3 = i
					this.itemWeight = e
					console.log(e)
					if (e == '0-25kg') {
						this.$Request.getT('/app/common/type/319').then(res => { //物品重量0-25Kg 319
							if (res.code == 0) {
								if (res.data && res.data.value) {
									this.wpInfoValue = res.data.value
								}
							}
						});
					} else if (e == '26-30kg') {
						this.$Request.getT('/app/common/type/320').then(res => { //物品重量26-30Kg 320
							if (res.code == 0) {
								if (res.data && res.data.value) {
									this.wpInfoValue = res.data.value
								}
							}
						});
					} else if (e == '31-40kg') {
						this.$Request.getT('/app/common/type/321').then(res => { //物品重量31-40Kg 321
							if (res.code == 0) {
								if (res.data && res.data.value) {
									this.wpInfoValue = res.data.value
								}
							}
						});
					} else if (e == '41-50kg') {
						this.$Request.getT('/app/common/type/322').then(res => { //物品重量41-50Kg 322
							if (res.code == 0) {
								if (res.data && res.data.value) {
									this.wpInfoValue = res.data.value
								}
							}
						});
					}

				}
			},

			// 根据订单信息判断用户可用红包
			openhongbao() {
				let price1 = (Number(this.dateMoney) + Number(this.baojiamoney) + Number(this.wupinMoney)).toFixed(2)

				let totalprice = (Number(price1) + Number(this.form.tip)).toFixed(2)
				let data = {
					indentType: this.indentType, //订单类型
					indentBasicsMoney: totalprice
				}
				this.$Request.postJson('/app/indent/findRedPacket', data).then(res => {
					// console.log(res)
					if (res.code === 0) {
						this.hongbao = res.data
						this.showmoney = true
					}
				});

			},
			onshowmoney() {
				let data = {
					indentType: this.indentType, //订单类型
					indentBasicsMoney: this.price1
				}
				this.$Request.postJson('/app/indent/findRedPacket', data).then(res => {
					// console.log(res)
					if (res.code === 0) {
						this.hongbao = res.data
					}
				});
			},
			// 判断红包使用id
			bindhongindex(id, money) {
				this.redPacketId = id
				this.form.hongbao = money
				this.showmoney = false


				let price1 = (Number(this.dateMoney) + Number(this.baojiamoney) + Number(this.wupinMoney) + Number(this
					.form.tip)).toFixed(2)
				// console.log(price1,'=====',this.form.hongbao)
				let totalprice = (Number(price1) - Number(this.form.hongbao)).toFixed(2)
				console.log(totalprice, '=====', this.form.hongbao)
				if (totalprice >= 0) {
					this.totalprice = totalprice;
					this.price = this.totalprice;
				} else {
					this.totalprice = 0.01;
				}
				
				this.orderjc()

			},
		

			//计算订单基础价格
			orderjc() {
				//日期价格+保价费+物品费-红包+小费+基础配送费
				// let price1 = 
				
				
				if(!this.distance&&!this.itemWeight){
					let totalprice = (Number(this.dateMoney) + Number(this.baojiamoney) + Number(this.wupinMoney) -
							Number(this.form.hongbao))+ Number(this.form.tip)+Number(this.price)
						.toFixed(2)
						return;
				}
				
				let data = {
					indentType: this.indentType, //订单类型
					itemWeight: this.itemWeight, //物品重量：kg
					itemValue: this.wpinfo.itemValue, //物品价格：
					distance: this.distance, //距离
				}
				this.$Request.postJson('/app/indent/basicsMoney', data).then(res => {
					// console.log(res)
					if (res.code === 0) {
						this.price = res.data.indentBasicsMoney
						this.price1 = res.data.indentBasicsMoney
						this.jichuprice = res.data.indentBasicsMoney

						this.redPacketId = '';
						this.form.hongbao = '';



						this.wupinMoney = parseFloat(parseFloat(this.price) + parseFloat(this.wpInfoValue))
							.toFixed(2);
                         //日期价格+保价费+物品费-红包+小费+基础配送费
						let price1 = (Number(this.dateMoney) + Number(this.baojiamoney) + Number(this.wupinMoney) -
								Number(this.form.hongbao))+ Number(this.form.tip)+Number(this.price)
							.toFixed(2)

						let totalprice = (Number(price1) + Number(this.form.tip)).toFixed(2)
						if (totalprice >= 0) {
							this.totalprice = totalprice;
							this.price = this.totalprice;
						}

						

						if (this.price) {
							this.onshowmoney()
						}
						this.wpprice = res.data

						// this.cargoInsurance = res.data.cargoInsurance
						// console.log(this.wpprice)
						// console.log(this.price, 'mmmmm')
						this.onKeyInput();
					}

				});
			},
			// 小费计算
			onKeyInput(e) {
				
				this.redPacketId = '';
				this.form.hongbao = '';
				let price1 = (Number(this.dateMoney) + Number(this.baojiamoney) + Number(this.wupinMoney) - Number(this
						.form.hongbao))
					.toFixed(2)

				let totalprice = (Number(price1) + Number(this.form.tip)).toFixed(2)
				if (totalprice >= 0) {
					this.totalprice = totalprice;
					this.price = this.totalprice;
				}
				this.orderjc()

			},
			// 提交订单
			bindpay() {
				this.orderjc()
				
				if (!this.take.address) {
					uni.showToast({
						title: '请选择发货地址',
						icon: 'none',
						duration: 2000
					});
					return
				}
				if (!this.close.address) {
					uni.showToast({
						title: '请选择收货地址',
						icon: 'none',
						duration: 2000
					});
					return
				}

				if (this.forms.data === '请选择时间') {
					uni.showToast({
						title: '请选择时间',
						icon: 'none',
						duration: 2000
					});
					return
				}

				if (this.wpinfo == '') {
					uni.showToast({
						title: '请选取物品信息',
						icon: 'none',
						duration: 2000
					});
					return
				}
				if (this.check == false) {
					uni.showToast({
						title: '跑腿代购服务协议',
						icon: 'none',
						duration: 2000
					});
					return
				} else {
					let data = {
						itemType: this.itemType,
						indentType: this.indentType, //订单类型
						itemWeight: this.itemWeight, //物品重量：kg
						// itemValue: this.itemValue, //物品价值：
						itemMoney: this.baojia,
						distance: this.distance, //距离
						shipAddress: this.take.address,
						shipAddressDetail: this.take.addressDetail,
						shipUserPhone: this.take.userPhone,
						shipUserName: this.take.userName,
						// cargoInsurance: this.cargoInsurance, //保价费
						cargoInsuranceFlag: this.cargoInsuranceFlag,
						deliveryAddress: this.close.address,
						deilveryAddressDetail: this.close.addressDetail,
						deliveryUserPhone: this.close.userPhone,
						deliveryUserName: this.close.userName,
						tip: this.form.tip, //小费金额
						modeOfPayment: "", //支付方式
						// itemCode: true,
						itemCodeFlag: this.itemCodeFlag,
						remarks: this.forms.remarks,
						// redPacketId: '', //红包id,
						indentBasicsMoney: this.wpprice.indentBasicsMoney, //订单价格
						userFine: this.wpprice.userFine, //用户取消订单罚金
						riderFine: this.wpprice.riderFine, //骑手取消订单罚金
						errandMoney: this.wpprice.errandMoney, //配送费
						// sendOutTime: this.buyTime, //时间
						sendOutTime: this.forms.data,
						redPacketId: this.redPacketId, //红包ID
						shipAddressLongitude: this.take.addressLongitude,
						shipAddressLatitude: this.take.addressLatitude,
						deliveryAddressLongitude: this.close.addressLongitude,
						deliveryAddressLatitude: this.close.addressLatitude
					}
					// console.log(data)
					// console.log(this.wpinfo)
					this.$Request.postJson('/app/indent/addIndent', data).then(res => {
						// console.log(res)
						if (res.code == 0) {
							this.indentNumber = res.data.indentNumber
							this.showmoneylist = false
							// #ifdef MP-WEIXIN
							this.openWay = 2
							this.pay()
							// #endif
							// #ifndef MP-WEIXIN
							this.showpay = true
							// #endif
						}
					});
				}
				// this.showmoneylist = true


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
										uni.switchTab({
											url: '/pages/order/order',
										})
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
						this.$Request.postJson('/app/wxPay/wxPayMpOrder?indentNumber=' + this.indentNumber).then(
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
						console.log(JSON.stringify(ret), '支付信息')
						this.isCheckPay(ret.code, 'wxpay', JSON.stringify(ret.data));
					});
					// #endif
				} else {
					// #ifdef H5
					this.$Request.postJson('/app/aliPay/payH5Order?indentNumber=' + this.indentNumber).then(
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
								uni.switchTab({
									url: '../order/order'
								})
							}, 1000);
						} else {
							uni.hideLoading();
							uni.showToast({
								icon: 'none',
								title: '支付失败!'
							});
							setTimeout(function() {
								uni.hideLoading();
								uni.switchTab({
									url: '../order/order'
								})
							}, 1000);
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
				console.log(name, '*-*-*', order)
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
							uni.hideLoading();
							uni.switchTab({
								url: '/pages/order/order',
							})
						}, 1000);
					},
					fail: function(err) {
						console.log(err)
						uni.hideLoading();
						uni.showToast({
							icon: 'none',
							title: '支付失败!'
						});
						setTimeout(function() {
							uni.hideLoading();
							uni.switchTab({
								url: '/pages/order/order',
							})
						}, 1000);
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
		background: #F2EDED;
	}

	/* #ifndef MP-WEIXIN */
	page {
		background: #F2EDED;
	}

	/* #endif */

	/* 取件时间弹框 */
	.popup_data {
		height: 600upx;
		width: 100%;
		position: relative;
	}

	.data_title {
		width: 92%;
		margin: 0 auto;
		font-size: 32rpx;
		line-height: 100upx;
		font-weight: bold;
		letter-spacing: 3upx;
		position: relative;
		top: 0upx;
		z-index: 99991;
	}

	.data_title1 {
		width: 100%;
		margin: 0 auto;
		font-size: 32rpx;
		line-height: 100upx;
		font-weight: bold;
		letter-spacing: 3upx;
		position: relative;
		top: 0upx;
		z-index: 99991;
	}

	.data_btn {
		position: absolute;
		bottom: 28upx;
		width: 100%;
		z-index: 99992;
	}

	.btn {
		width: 90%;
		margin: 0 auto;
		height: 80upx;
		color: white;
		background: #FF7F00;
		border-radius: 20upx;
		text-align: center;
		line-height: 80upx;
		letter-spacing: 2upx;
	}

	.u-close {
		position: absolute;
		z-index: 999999 !important;
	}

	.u-picker-body {
		height: 700upx !important;
	}

	/* .u-picker-header {
		position: relative !important;
		top: 138upx;
		z-index: 99992;
	} */
	/* 物品信息弹框 */
	.popup_info {
		height: 950rpx;
		width: 100%;
		position: relative;
	}

	.data_items {
		width: 100%;
	}

	.item_box {
		width: 90%;
		margin: 0 auto;
	}

	.item_btn {
		border: 1upx solid #cccccc;
		border-radius: 70upx;
		width: 200upx;
		height: 70upx;
		text-align: center;
		line-height: 70upx;
		font-size: 27rpx;
		margin-right: 10upx;
		margin-left: 10upx;
		margin-bottom: 20upx;
	}

	.item_btn:active {
		background: #ffd9b3;
		border: 2upx solid #ff7f00;
	}

	.item_type {
		display: flex;
		flex-wrap: wrap;
		margin-top: 20upx;
	}

	/* 物品保价弹框 */
	.valua_icon {
		width: 90%;
		position: absolute;
		bottom: 120upx;
		left: 40upx;
	}

	.input {
		width: 90%;
		margin: 0 auto;
	}

	.u-input--border {
		border-radius: 12rupx;
		/* border-radius:12rupx; */
		border: 2upx solid #F2F2F2 !important;
		background: #F2F2F2 !important;
	}

	.input_bg {
		width: 90%;
		margin: 0 auto;
		position: relative;
	}

	.input_bg image {
		width: 100%;
		height: 80upx;
	}

	.input_bg text {
		position: absolute;
		top: 30upx;
		left: 24upx;
		color: #FF7F00;
		font-size: 26upx;
	}

	/* 费用明细弹框 */
	.popupmoneylist {
		/* height: 400upx; */
		width: 100%;
		height: 450rpx;
		position: relative;
		background: #fff;
		padding-bottom: 45rpx;

	}

	.paytut {
		display: flex;
		align-items: center;
		justify-content: space-between;
		background: #000;
		color: #fff;
		margin: 30upx;
		border-radius: 55upx;

	}

	.tabbarprice {
		/* flex: 1; */
		color: #fff;
		display: flex;
		justify-content: left;
		align-items: center;
		font-size: 30rpx;
		font-weight: bold;
		margin-left: 40upx;
	}

	.tabbarprice text {
		color: #fff;
		font-size: 38upx;
		font-weight: 500;
	}

	.tabbarbtn {
		display: flex;
		justify-content: flex-end;
		align-items: center;
	}

	.buts {
		background: #FF7F00;
		width: 230upx;
		height: 100upx;
		text-align: center;
		line-height: 100upx;
		border-radius: 0upx 60upx 60upx 0upx;
		color: white;
		font-size: 27rpx;
	}

	/* 红包弹框 */
	.popup_money {
		/* height: 400upx; */
		width: 100%;
		height: 630rpx;
		position: relative;
		background: #F2EDED;
		padding-bottom: 45rpx;
		/* #ifndef MP-WEIXIN */
		height: 680rpx;
		/* #endif */

	}

	.uni-scroll-view,
	.uni-scroll-view-content {
		position: relative;
		width: 100%;
		height: 100%;
		background: #F2EDED;
	}

	.image_box {
		width: 40%;
		margin: 0 auto;
		margin-bottom: 110rpx;
	}

	.u-drawer-bottom {
		background-color: #FAF7F5 !important;
	}

	.popupwupin {
		width: 100%;
		padding: 30rpx;
		margin: 0 auto;
		background: #ffffff;
		border-radius: 14upx;
		/* height: 220rpx; */
		margin-bottom: 50rpx;
	}

	.money_box {
		width: 93%;
		margin: 0 auto;
		background: #ffffff;
		border-radius: 14upx;
		height: 220rpx;
		margin-bottom: 50rpx;
	}

	.box_tit {
		width: 90%;
		margin: 0 auto;
		height: 80upx;
		display: flex;
	}

	.money_name {
		flex: 1;
		display: flex;
		justify-content: left;
		align-items: center;
		font-size: 27rpx;
		font-weight: bold;
		letter-spacing: 2upx;
	}

	.money_price {
		flex: 1;
		display: flex;
		justify-content: flex-end;
		align-items: center;
		font-size: 34upx;
		font-weight: bold;
		color: red;
	}

	.money_data {
		color: #999999;
		font-size: 24rpx;
		width: 90%;
		margin: 0 auto;
		margin-top: -8upx;
	}

	.u-line {
		width: 90% !important;
		border-bottom-width: 6upx !important;
		margin: 0 auto !important;
		margin-top: 22upx !important;
		margin-bottom: 22upx !important;
	}

	.box_bottom {
		width: 90%;
		margin: 0 auto;
		display: flex;
		height: 40upx;
	}

	.money_use {
		flex: 1;
		color: #999999;
		font-size: 24rpx;
		display: flex;
		justify-content: left;
		align-items: center;
	}

	.lj_use {
		width: 150rpx;
		border: 2rpx solid #FF7F00;
		color: #FF7F00;
		text-align: center;
		line-height: 48rpx;
		border-radius: 40rpx;
		font-size: 23rpx;
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

	.contents {
		width: 100%;
		/* #ifdef MP-WEIXIN */
		margin-top: 25rpx;
		/* #endif */

	}

	.part_one {
		width: 95%;
		margin: 0 auto;
		/* height: 240upx; */
		background: #ffffff;
		border-radius: 20upx;
		margin-top: 20upx;
		padding: 25rpx 10rpx;
	}

	.one_box {
		width: 100%;
		/* height: 80upx; */
		/* background: #F5F5F5; */
		margin: 0 auto;
		border-radius: 12upx;
		display: flex;
		/* height: 100rpx; */
		align-items: center;
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
		width: 80%;
		font-size: 32rpx;
		overflow: hidden;
	}

	.box_addres1 {
		/* flex: 5; */
		width: 80%;
		font-size: 28rpx;
		overflow: hidden;
	}

	.name {
		display: inline;
		font-size: 31rpx;
		color: #999999;
	}

	.number {
		display: initial;
		color: #999999;
		font-size: 31rpx;
		margin-left: 30upx;
	}

	.box_image {
		width: 15%;
		/* margin-right: 22rpx; */
		display: flex;
		justify-content: center;
		align-items: center;
	}

	.u-icon__icon {
		color: #CCCCCC !important;
	}

	.icon_you {
		/* position: relative;
		top: 10upx !important; */
	}

	.box_jh {
		margin-left: 19rpx;
		height: 30rpx;
		display: flex;
		justify-content: left;
		align-items: center;
	}

	.box_jh image {
		width: 30upx;
		height: 30upx;
	}

	.part_two {
		width: 95%;
		margin: 0 auto;
		/* height: 260rpx; */
		background: #ffffff;
		border-radius: 20upx;
		margin-top: 20upx;
	}

	/* .u-input {
		width: 80% !important;
		margin-left: 130rpx !important;
	} */

	.part_three {
		width: 95%;
		margin: 0 auto;
		/* height: 260rpx; */
		background: #ffffff;
		border-radius: 20upx;
		margin-top: 20upx;
	}

	.u-form-item__body {
		position: relative !important;

	}

	.u-form-item--left {
		position: absolute !important;
		top: 30upx;
		left: 10rpx;
		font-size: 31rpx;
		/* font-weight: bold; */
		letter-spacing: 2upx;
	}

	.u-form-item--right__content {
		margin-right: 20upx !important;
	}

	.u-form-item {
		line-height: 20upx !important;
		padding: 25rpx 0 !important;
	}

	.u-input__input {
		font-size: 28rpx !important;
	}

	.u-input {
		position: relative;
		display: flex;
		flex-direction: row;
		justify-content: flex-end !important;
	}

	.part_four {
		width: 95%;
		background: #ffffff;
		margin: 0 auto;
		border-radius: 20upx;
		display: flex;
		margin-top: 20upx;
	}

	.take_number {
		flex: 4;
	}

	.number_name {
		width: 90%;
		margin: 0 auto;
		font-size: 31rpx;
		font-weight: bold;
		line-height: 70rpx;

	}

	.number_text {
		width: 90%;
		margin: 0 auto;
		color: #999999;
		font-size: 25rpx;
		line-height: 20upx;
	}

	.take_number_kg {
		flex: 1;
		display: flex;
		justify-content: center;
		align-items: center;
	}

	.content_sure {
		display: flex;
		align-items: center;
		margin-bottom: 160rpx;
		margin-left: 15px;
		margin-top: 10px;
	}

	.contentsure {
		display: flex;
		align-items: center;
		margin-bottom: 50rpx;
		margin-left: 15px;
		margin-top: 10px;
	}

	.u-checkbox__label {
		font-size: 26upx !important;
		letter-spacing: 2upx;
		color: #888888 !important;
	}

	.tabbar {
		width: 100%;
		background: #ffffff;
		position: fixed;
		bottom: 0upx;
		left: 0upx;
		right: 0upx;

		/* #ifndef MP-WEIXIN */
		position: fixed;
		bottom: 0upx;
		left: 0upx;
		right: 0upx;
		/* #endif */
		z-index: 99;
	}


	.actclass {
		background-color: #FFD9B3;
		border: none
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

	.surebtn {
		background: #FF7F00;
		color: #ffffff;
		text-align: center;
		padding: 25upx 0upx;
		border-radius: 25upx;
	}

	.huibtn {

		width: 100%;
		height: 100vh;
		background: #000;
		opacity: 0.2;
		z-index: 99;
	}
</style>
