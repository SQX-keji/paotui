<template>
	<view class="order">
		<view style="position: fixed;top: 0;width: 100%;z-index: 999;">
			<u-tabs :list="list" :is-scroll="true" active-color="#FF7F00" inactive-color=" #666666" :bold="bold"
				:current="current" @change="change" :show-bar="false"></u-tabs>
		</view>
		<!-- #ifdef MP-WEIXIN -->
		<view style="padding-top: 86rpx;">
		<!-- #endif -->
		<!-- #ifdef H5 -->
		<view style="padding-top: 0;">
		<!-- #endif -->
		<!-- #ifdef APP -->
		<view style="padding-top: 86rpx;">
		<!-- #endif -->
			<view class="tabs_box" :class="{dis:current == 0}">
				<view>
					<view class="order_box" v-for="(item,index) in order" :key="index"
						@click="bindorderDetail(item.indentNumber)">
						<view class="order_success">
							<view class="order_name" v-if="item.indentState == 0">待付款</view>
							<view class="order_name"
								v-if="item.indentState == 1||item.indentState == 8||item.indentState == 9||item.indentState == 10">
								已取消</view>
							<view class="order_name" v-if="item.indentState == 2">待接单</view>
							<view class="order_name" v-if="item.indentState == 5">待确认</view>
							<view class="order_name" v-if="item.indentState == 3">已接单</view>
							<view class="order_name" v-if="item.indentState == 4">派送中</view>
							<view class="order_name" v-if="item.indentState ==6||item.indentState ==7">
								已完成</view>
							<view class="order_name" v-if="item.indentState == 10">
								已取消</view>
							<view class="order_data">{{item.createTime}}</view>
						</view>
						<u-line color="#E6E6E6" />
						<view class="order_city">
							<view class="city_type">
								<view class="type_name" v-if="item.indentType == 1">帮我送</view>
								<view class="type_name" v-if="item.indentType == 2">帮我取</view>
								<view class="type_name" v-if="item.indentType == 3">同城帮买</view>
								<view class="type_name" v-if="item.indentType == 4">同城服务</view>
								<view class="city_text" v-if="item.indentType == 1||item.indentType == 2">
									{{item.itemType}}
								</view>
								<view class="city_text" v-if="item.indentType == 3&&item.buyType == 0">骑手购买</view>
								<view class="city_text" v-if="item.indentType == 3&&item.buyType == 1">指定购买</view>
								<view class="city_text" v-if="item.indentType == 4">{{item.serviceType}}</view>

							</view>
							<view class="city_address">
								<view class="fh_box"
									v-if="item.indentType == 1||item.indentType == 2||item.indentType == 3">
									<view class="fh_image">
										<image src="../../static/image/mai.png" v-if="item.indentType == 3"></image>
										<image src="../../static/image/icon_f.png" v-else></image>
									</view>
									<view class="box">
										<view class="fh_name">{{item.shipAddressDetail}}</view>
										<view class="fh_type" v-if="item.indentType != 3">{{item.shipUserName}}
											<text>{{item.shipUserPhone}}</text>
										</view>
									</view>
								</view>
								<view class="sh_box">
									<view class="sh_image">
										<image src="../../static/image/icon_s.png"></image>
									</view>
									<view class="box">
										<view class="sh_name">{{item.deilveryAddressDetail}}</view>
										<view class="sh_type">{{item.deliveryUserName}}
											<text>{{item.deliveryUserPhone}}</text>
										</view>
									</view>
								</view>
							</view>
						</view>
						<u-line color="#E6E6E6" />
						<view class="order_btn">
							<view class="btn1"
								v-if="item.indentState == 7||item.indentState == 6 &&!item.evaluateMessage"
								@tap.stop="bindcomment(item)">
								去评价
							</view>
							<view class="btn1" v-if="item.indentState == 0||item.indentState == 2"
								@tap.stop="bindorderOff(item)">取消订单</view>
							<view class="btn1" @tap.stop="bindconfirm(item)" v-if="item.indentState == 5">确认订单</view>
							<view class="btn2" @tap.stop="bindorder(item)" v-if="item.indentState == 1||item.indentState == 3||item.indentState ==8||
						item.indentState ==9||item.indentState ==10||item.indentState == 4||item.indentState == 6">再来一单</view>
							<view class="btn2" v-if="item.indentState == 0" @tap.stop="bindorderpay(item)">立即付款</view>
						</view>
					</view>
				</view>
			</view>
			<view class="tabs_box" :class="{dis:current == 1}">
				<view class="empty" v-if="order.length == 0">
					<view
						style="display: block; width: 90%; margin: 0 auto; position: fixed;top: 35%;left: 0rpx;right: 0rpx;text-align: center;">
						<image src="../../static/image/emety.png" style="width: 300rpx;height: 300rpx;"></image>
						<view style="color: #CCCCCC;">暂无内容</view>
					</view>
				</view>
				<view v-else>
					<view class="order_box" v-for="(item,index) in order" :key="index"
						@click="bindorderDetail(item.indentNumber)">
						<view class="order_success">
							<view class="order_name" v-if="item.indentState == 0">待付款</view>
							<view class="order_name"
								v-if="item.indentState == 1||item.indentState == 8||item.indentState == 9||item.indentState == 10">
								已取消</view>
							<view class="order_name" v-if="item.indentState == 2">待接单</view>
							<view class="order_name" v-if="item.indentState == 5">待确认</view>
							<view class="order_name" v-if="item.indentState == 3">已接单</view>
							<view class="order_name" v-if="item.indentState == 4">派送中</view>
							<view class="order_name" v-if="item.indentState ==6||item.indentState ==7">
								已完成</view>
							<view class="order_data">{{item.createTime}}</view>
						</view>
						<u-line color="#E6E6E6" />
						<view class="order_city">
							<view class="city_type">
								<view class="type_name" v-if="item.indentType == 1">帮我送</view>
								<view class="type_name" v-if="item.indentType == 2">帮我取</view>
								<view class="type_name" v-if="item.indentType == 3">同城帮买</view>
								<view class="type_name" v-if="item.indentType == 4">同城服务</view>
								<view class="city_text" v-if="item.indentType == 1||item.indentType == 2">
									{{item.itemType}}
								</view>
								<view class="city_text" v-if="item.indentType == 3&&item.buyType == 0">骑手购买</view>
								<view class="city_text" v-if="item.indentType == 3&&item.buyType == 1">指定购买</view>
								<view class="city_text" v-if="item.indentType == 4">{{item.serviceType}}</view>

							</view>
							<view class="city_address">
								<view class="fh_box"
									v-if="item.indentType == 1||item.indentType == 2||item.indentType == 3">
									<view class="fh_image">
										<image src="../../static/image/mai.png" v-if="item.indentType == 3"></image>
										<image src="../../static/image/icon_f.png" v-else></image>
									</view>
									<view class="box">
										<view class="fh_name">{{item.shipAddressDetail}}</view>
										<view class="fh_type" v-if="item.indentType != 3">{{item.shipUserName}}
											<text>{{item.shipUserPhone}}</text>
										</view>
									</view>
								</view>
								<view class="sh_box">
									<view class="sh_image">
										<image src="../../static/image/icon_s.png"></image>
									</view>
									<view class="box">
										<view class="sh_name">{{item.deilveryAddressDetail}}</view>
										<view class="sh_type">{{item.deliveryUserName}}
											<text>{{item.deliveryUserPhone}}</text>
										</view>
									</view>
								</view>
							</view>
						</view>
						<u-line color="#E6E6E6" />
						<view class="order_btn">
							<view class="btn1" v-if="item.indentState ==7||item.indentState == 6&&!item.evaluateMessage"
								@tap.stop="bindcomment(item)">
								去评价
							</view>
							<view class="btn1" v-if="item.indentState == 0||item.indentState == 2"
								@tap.stop="bindorderOff(item)">取消订单</view>
							<view class="btn1" @tap.stop="bindconfirm(item)" v-if="item.indentState == 5">确认订单</view>
							<view class="btn2" @tap.stop="bindorder(item)" v-if="item.indentState == 1||item.indentState == 3||item.indentState ==8||
						item.indentState ==9||item.indentState ==10||item.indentState == 4||item.indentState == 6">再来一单</view>
							<view class="btn2" v-if="item.indentState == 0" @tap.stop="bindorderpay(item)">立即付款</view>
						</view>
					</view>
				</view>
			</view>
			<view class="tabs_box" :class="{dis:current == 2}">
				<view class="empty" v-if="order.length == 0">
					<view
						style="display: block; width: 90%; margin: 0 auto; position: fixed;top: 35%;left: 0rpx;right: 0rpx;text-align: center;">
						<image src="../../static/image/emety.png" style="width: 300rpx;height: 300rpx;"></image>
						<view style="color: #CCCCCC;">暂无内容</view>
					</view>
				</view>
				<view v-else>
					<view class="order_box" v-for="(item,index) in order" :key="index"
						@click="bindorderDetail(item.indentNumber)">
						<view class="order_success">
							<view class="order_name" v-if="item.indentState == 0">待付款</view>
							<view class="order_name"
								v-if="item.indentState == 1||item.indentState == 8||item.indentState == 9||item.indentState == 10">
								已取消</view>
							<view class="order_name" v-if="item.indentState == 2">待接单</view>
							<view class="order_name" v-if="item.indentState == 5">待确认</view>
							<view class="order_name" v-if="item.indentState == 3">已接单</view>
							<view class="order_name" v-if="item.indentState == 4">派送中</view>
							<view class="order_name" v-if="item.indentState ==6||item.indentState ==7">
								已完成</view>
							<view class="order_data">{{item.createTime}}</view>
						</view>
						<u-line color="#E6E6E6" />
						<view class="order_city">
							<view class="city_type">
								<view class="type_name" v-if="item.indentType == 1">帮我送</view>
								<view class="type_name" v-if="item.indentType == 2">帮我取</view>
								<view class="type_name" v-if="item.indentType == 3">同城帮买</view>
								<view class="type_name" v-if="item.indentType == 4">同城服务</view>
								<view class="city_text" v-if="item.indentType == 1||item.indentType == 2">
									{{item.itemType}}
								</view>
								<view class="city_text" v-if="item.indentType == 3&&item.buyType == 0">骑手购买</view>
								<view class="city_text" v-if="item.indentType == 3&&item.buyType == 1">指定购买</view>
								<view class="city_text" v-if="item.indentType == 4">{{item.serviceType}}</view>

							</view>
							<view class="city_address">
								<view class="fh_box"
									v-if="item.indentType == 1||item.indentType == 2||item.indentType == 3">
									<view class="fh_image">
										<image src="../../static/image/mai.png" v-if="item.indentType == 3"></image>
										<image src="../../static/image/icon_f.png" v-else></image>
									</view>
									<view class="box">
										<view class="fh_name">{{item.shipAddressDetail}}</view>
										<view class="fh_type" v-if="item.indentType != 3">{{item.shipUserName}}
											<text>{{item.shipUserPhone}}</text>
										</view>
									</view>
								</view>
								<view class="sh_box">
									<view class="sh_image">
										<image src="../../static/image/icon_s.png"></image>
									</view>
									<view class="box">
										<view class="sh_name">{{item.deilveryAddressDetail}}</view>
										<view class="sh_type">{{item.deliveryUserName}}
											<text>{{item.deliveryUserPhone}}</text>
										</view>
									</view>
								</view>
							</view>
						</view>
						<u-line color="#E6E6E6" />
						<view class="order_btn">
							<view class="btn1" v-if="item.indentState ==7||item.indentState == 6&&!item.evaluateMessage"
								@tap.stop="bindcomment(item)">
								去评价
							</view>
							<view class="btn1" v-if="item.indentState == 0||item.indentState == 2"
								@tap.stop="bindorderOff(item)">取消订单</view>
							<view class="btn1" @tap.stop="bindconfirm(item)" v-if="item.indentState == 5">确认订单</view>
							<view class="btn2" @tap.stop="bindorder(item)" v-if="item.indentState == 1||item.indentState == 3||item.indentState ==8||
						item.indentState ==9||item.indentState ==10||item.indentState == 4||item.indentState == 6">再来一单</view>
							<view class="btn2" v-if="item.indentState == 0" @tap.stop="bindorderpay(item)">立即付款</view>
						</view>
					</view>
				</view>

			</view>
			<view class="tabs_box" :class="{dis:current == 3}">
				<view class="empty" v-if="order.length < 0">
					<view
						style="display: block; width: 90%; margin: 0 auto; position: fixed;top: 35%;left: 0rpx;right: 0rpx;text-align: center;">
						<image src="../../static/image/emety.png" style="width: 300rpx;height: 300rpx;"></image>
						<view style="color: #CCCCCC;">暂无内容</view>
					</view>
				</view>
				<view v-else>
					<view class="order_box" v-for="(item,index) in order" :key="index"
						@click="bindorderDetail(item.indentNumber)">
						<view class="order_success">
							<view class="order_name" v-if="item.indentState == 0">待付款</view>
							<view class="order_name"
								v-if="item.indentState == 1||item.indentState == 8||item.indentState == 9||item.indentState == 10">
								已取消</view>
							<view class="order_name" v-if="item.indentState == 2">待接单</view>
							<view class="order_name" v-if="item.indentState == 5">待确认</view>
							<view class="order_name" v-if="item.indentState == 3">已接单</view>
							<view class="order_name" v-if="item.indentState == 4">派送中</view>
							<view class="order_name" v-if="item.indentState ==6||item.indentState ==7">
								已完成</view>
							<view class="order_data">{{item.createTime}}</view>
						</view>
						<u-line color="#E6E6E6" />
						<view class="order_city">
							<view class="city_type">
								<view class="type_name" v-if="item.indentType == 1">帮我送</view>
								<view class="type_name" v-if="item.indentType == 2">帮我取</view>
								<view class="type_name" v-if="item.indentType == 3">同城帮买</view>
								<view class="type_name" v-if="item.indentType == 4">同城服务</view>
								<view class="city_text" v-if="item.indentType == 1||item.indentType == 2">
									{{item.itemType}}
								</view>
								<view class="city_text" v-if="item.indentType == 3&&item.buyType == 0">骑手购买</view>
								<view class="city_text" v-if="item.indentType == 3&&item.buyType == 1">指定购买</view>
								<view class="city_text" v-if="item.indentType == 4">{{item.serviceType}}</view>

							</view>
							<view class="city_address">
								<view class="fh_box"
									v-if="item.indentType == 1||item.indentType == 2||item.indentType == 3">
									<view class="fh_image">
										<image src="../../static/image/mai.png" v-if="item.indentType == 3"></image>
										<image src="../../static/image/icon_f.png" v-else></image>
									</view>
									<view class="box">
										<view class="fh_name">{{item.shipAddressDetail}}</view>
										<view class="fh_type" v-if="item.indentType != 3">{{item.shipUserName}}
											<text>{{item.shipUserPhone}}</text>
										</view>
									</view>
								</view>
								<view class="sh_box">
									<view class="sh_image">
										<image src="../../static/image/icon_s.png"></image>
									</view>
									<view class="box">
										<view class="sh_name">{{item.deilveryAddressDetail}}</view>
										<view class="sh_type">{{item.deliveryUserName}}
											<text>{{item.deliveryUserPhone}}</text>
										</view>
									</view>
								</view>
							</view>
						</view>
						<u-line color="#E6E6E6" />
						<view class="order_btn">
							<view class="btn1" v-if="item.indentState ==7||item.indentState == 6&&!item.evaluateMessage"
								@tap.stop="bindcomment(item)">
								去评价
							</view>
							<view class="btn1" v-if="item.indentState == 0||item.indentState == 2"
								@tap.stop="bindorderOff(item)">取消订单</view>
							<view class="btn1" @tap.stop="bindconfirm(item)" v-if="item.indentState == 5">确认订单</view>
							<view class="btn2" @tap.stop="bindorder(item)" v-if="item.indentState == 1||item.indentState == 3||item.indentState ==8||
						item.indentState ==9||item.indentState ==10||item.indentState == 4||item.indentState == 6">再来一单</view>
							<view class="btn2" v-if="item.indentState == 0" @tap.stop="bindorderpay(item)">立即付款</view>
						</view>
					</view>
				</view>

			</view>
			<view class="tabs_box" :class="{dis:current == 4}">
				<view class="empty" v-if="order.length < 0">
					<view
						style="display: block; width: 90%; margin: 0 auto; position: fixed;top: 35%;left: 0rpx;right: 0rpx;text-align: center;">
						<image src="../../static/image/emety.png" style="width: 300rpx;height: 300rpx;"></image>
						<view style="color: #CCCCCC;">暂无内容</view>
					</view>
				</view>
				<view v-else>
					<view class="order_box" v-for="(item,index) in order" :key="index"
						@click="bindorderDetail(item.indentNumber)">
						<view class="order_success">
							<view class="order_name" v-if="item.indentState == 0">待付款</view>
							<view class="order_name"
								v-if="item.indentState == 1||item.indentState == 8||item.indentState == 9||item.indentState == 10">
								已取消</view>
							<view class="order_name" v-if="item.indentState == 2">待接单</view>
							<view class="order_name" v-if="item.indentState == 5">待确认</view>
							<view class="order_name" v-if="item.indentState == 3">已接单</view>
							<view class="order_name" v-if="item.indentState == 4">派送中</view>
							<view class="order_name" v-if="item.indentState ==6||item.indentState ==7">
								已完成</view>
							<view class="order_data">{{item.createTime}}</view>
						</view>
						<u-line color="#E6E6E6" />
						<view class="order_city">
							<view class="city_type">
								<view class="type_name" v-if="item.indentType == 1">帮我送</view>
								<view class="type_name" v-if="item.indentType == 2">帮我取</view>
								<view class="type_name" v-if="item.indentType == 3">同城帮买</view>
								<view class="type_name" v-if="item.indentType == 4">同城服务</view>
								<view class="city_text" v-if="item.indentType == 1||item.indentType == 2">
									{{item.itemType}}
								</view>
								<view class="city_text" v-if="item.indentType == 3&&item.buyType == 0">骑手购买</view>
								<view class="city_text" v-if="item.indentType == 3&&item.buyType == 1">指定购买</view>
								<view class="city_text" v-if="item.indentType == 4">{{item.serviceType}}</view>

							</view>
							<view class="city_address">
								<view class="fh_box"
									v-if="item.indentType == 1||item.indentType == 2||item.indentType == 3">
									<view class="fh_image">
										<image src="../../static/image/mai.png" v-if="item.indentType == 3"></image>
										<image src="../../static/image/icon_f.png" v-else></image>
									</view>
									<view class="box">
										<view class="fh_name">{{item.shipAddressDetail}}</view>
										<view class="fh_type" v-if="item.indentType != 3">{{item.shipUserName}}
											<text>{{item.shipUserPhone}}</text>
										</view>
									</view>
								</view>
								<view class="sh_box">
									<view class="sh_image">
										<image src="../../static/image/icon_s.png"></image>
									</view>
									<view class="box">
										<view class="sh_name">{{item.deilveryAddressDetail}}</view>
										<view class="sh_type">{{item.deliveryUserName}}
											<text>{{item.deliveryUserPhone}}</text>
										</view>
									</view>
								</view>
							</view>
						</view>
						<u-line color="#E6E6E6" />
						<view class="order_btn">
							<view class="btn1" v-if="item.indentState ==7||item.indentState == 6&&!item.evaluateMessage"
								@tap.stop="bindcomment(item)">
								去评价
							</view>
							<view class="btn1" v-if="item.indentState == 0||item.indentState == 2"
								@tap.stop="bindorderOff(item)">取消订单</view>
							<view class="btn1" @tap.stop="bindconfirm(item)" v-if="item.indentState == 5">确认订单</view>
							<view class="btn2" @tap.stop="bindorder(item)" v-if="item.indentState == 1||item.indentState == 3||item.indentState ==8||
						item.indentState ==9||item.indentState ==10||item.indentState == 4||item.indentState == 6">再来一单</view>
							<view class="btn2" v-if="item.indentState == 0" @tap.stop="bindorderpay(item)">立即付款</view>
						</view>
					</view>
				</view>
			</view>
			<view class="tabs_box" :class="{dis:current == 5}">
				<view class="empty" v-if="order.length < 0">
					<view
						style="display: block; width: 90%; margin: 0 auto; position: fixed;top: 35%;left: 0rpx;right: 0rpx;text-align: center;">
						<image src="../../static/image/emety.png" style="width: 300rpx;height: 300rpx;"></image>
						<view style="color: #CCCCCC;">暂无内容</view>
					</view>
				</view>
				<view v-else>
					<view class="order_box" v-for="(item,index) in order" :key="index"
						@click="bindorderDetail(item.indentNumber)">
						<view class="order_success">
							<view class="order_name" v-if="item.indentState == 0">待付款</view>
							<view class="order_name"
								v-if="item.indentState == 1||item.indentState == 8||item.indentState == 9||item.indentState == 10">
								已取消</view>
							<view class="order_name" v-if="item.indentState == 2">待接单</view>
							<view class="order_name" v-if="item.indentState == 5">待确认</view>
							<view class="order_name" v-if="item.indentState == 3">已接单</view>
							<view class="order_name" v-if="item.indentState == 4">派送中</view>
							<view class="order_name" v-if="item.indentState ==6||item.indentState ==7">
								已完成</view>
							<view class="order_data">{{item.createTime}}</view>
						</view>
						<u-line color="#E6E6E6" />
						<view class="order_city">
							<view class="city_type">
								<view class="type_name" v-if="item.indentType == 1">帮我送</view>
								<view class="type_name" v-if="item.indentType == 2">帮我取</view>
								<view class="type_name" v-if="item.indentType == 3">同城帮买</view>
								<view class="type_name" v-if="item.indentType == 4">同城服务</view>
								<view class="city_text" v-if="item.indentType == 1||item.indentType == 2">
									{{item.itemType}}
								</view>
								<view class="city_text" v-if="item.indentType == 3&&item.buyType == 0">骑手购买</view>
								<view class="city_text" v-if="item.indentType == 3&&item.buyType == 1">指定购买</view>
								<view class="city_text" v-if="item.indentType == 4">{{item.serviceType}}</view>

							</view>
							<view class="city_address">
								<view class="fh_box"
									v-if="item.indentType == 1||item.indentType == 2||item.indentType == 3">
									<view class="fh_image">
										<image src="../../static/image/mai.png" v-if="item.indentType == 3"></image>
										<image src="../../static/image/icon_f.png" v-else></image>
									</view>
									<view class="box">
										<view class="fh_name">{{item.shipAddressDetail}}</view>
										<view class="fh_type" v-if="item.indentType != 3">{{item.shipUserName}}
											<text>{{item.shipUserPhone}}</text>
										</view>
									</view>
								</view>
								<view class="sh_box">
									<view class="sh_image">
										<image src="../../static/image/icon_s.png"></image>
									</view>
									<view class="box">
										<view class="sh_name">{{item.deilveryAddressDetail}}</view>
										<view class="sh_type">{{item.deliveryUserName}}
											<text>{{item.deliveryUserPhone}}</text>
										</view>
									</view>
								</view>
							</view>
						</view>
						<u-line color="#E6E6E6" />
						<view class="order_btn">
							<view class="btn1" v-if="item.indentState ==7||item.indentState == 6&&!item.evaluateMessage"
								@tap.stop="bindcomment(item)">
								去评价
							</view>
							<view class="btn1" v-if="item.indentState == 0||item.indentState == 2"
								@tap.stop="bindorderOff(item)">取消订单</view>
							<view class="btn1" @tap.stop="bindconfirm(item)" v-if="item.indentState == 5">确认订单</view>
							<view class="btn2" @tap.stop="bindorder(item)" v-if="item.indentState == 1||item.indentState == 3||item.indentState ==8||
						item.indentState ==9||item.indentState ==10||item.indentState == 4||item.indentState == 6">再来一单</view>
							<view class="btn2" v-if="item.indentState == 0" @tap.stop="bindorderpay(item)">立即付款</view>
						</view>
					</view>
				</view>
			</view>
			<view class="tabs_box" :class="{dis:current == 6}">
				<view class="empty" v-if="order.length < 0">
					<view
						style="display: block; width: 90%; margin: 0 auto; position: fixed;top: 35%;left: 0rpx;right: 0rpx;text-align: center;">
						<image src="../../static/image/emety.png" style="width: 300rpx;height: 300rpx;"></image>
						<view style="color: #CCCCCC;">暂无内容</view>
					</view>
				</view>
				<view v-else>
					<view class="order_box" v-for="(item,index) in order" :key="index"
						@click="bindorderDetail(item.indentNumber)">
						<view class="order_success">
							<view class="order_name" v-if="item.indentState == 0">待付款</view>
							<view class="order_name"
								v-if="item.indentState == 1||item.indentState == 8||item.indentState == 9||item.indentState == 10">
								已取消</view>
							<view class="order_name" v-if="item.indentState == 2">待接单</view>
							<view class="order_name" v-if="item.indentState == 5">待确认</view>
							<view class="order_name" v-if="item.indentState == 3">已接单</view>
							<view class="order_name" v-if="item.indentState == 4">派送中</view>
							<view class="order_name" v-if="item.indentState ==6||item.indentState ==7">
								已完成</view>
							<view class="order_data">{{item.createTime}}</view>
						</view>
						<u-line color="#E6E6E6" />
						<view class="order_city">
							<view class="city_type">
								<view class="type_name" v-if="item.indentType == 1">帮我送</view>
								<view class="type_name" v-if="item.indentType == 2">帮我取</view>
								<view class="type_name" v-if="item.indentType == 3">同城帮买</view>
								<view class="type_name" v-if="item.indentType == 4">同城服务</view>
								<view class="city_text" v-if="item.indentType == 1||item.indentType == 2">
									{{item.itemType}}
								</view>
								<view class="city_text" v-if="item.indentType == 3&&item.buyType == 0">骑手购买</view>
								<view class="city_text" v-if="item.indentType == 3&&item.buyType == 1">指定购买</view>
								<view class="city_text" v-if="item.indentType == 4">{{item.serviceType}}</view>

							</view>
							<view class="city_address">
								<view class="fh_box"
									v-if="item.indentType == 1||item.indentType == 2||item.indentType == 3">
									<view class="fh_image">
										<image src="../../static/image/mai.png" v-if="item.indentType == 3"></image>
										<image src="../../static/image/icon_f.png" v-else></image>
									</view>
									<view class="box">
										<view class="fh_name">{{item.shipAddressDetail}}</view>
										<view class="fh_type" v-if="item.indentType != 3">{{item.shipUserName}}
											<text>{{item.shipUserPhone}}</text>
										</view>
									</view>
								</view>
								<view class="sh_box">
									<view class="sh_image">
										<image src="../../static/image/icon_s.png"></image>
									</view>
									<view class="box">
										<view class="sh_name">{{item.deilveryAddressDetail}}</view>
										<view class="sh_type">{{item.deliveryUserName}}
											<text>{{item.deliveryUserPhone}}</text>
										</view>
									</view>
								</view>
							</view>
						</view>
						<u-line color="#E6E6E6" />
						<view class="order_btn">
							<view class="btn1" v-if="item.indentState ==7||item.indentState == 6&&!item.evaluateMessage"
								@tap.stop="bindcomment(item)">
								去评价
							</view>
							<view class="btn1" v-if="item.indentState == 0||item.indentState == 2"
								@tap.stop="bindorderOff(item)">取消订单</view>
							<view class="btn1" @tap.stop="bindconfirm(item)" v-if="item.indentState == 5">确认订单</view>
							<view class="btn2" @tap.stop="bindorder(item)" v-if="item.indentState == 1||item.indentState == 3||item.indentState ==8||
						item.indentState ==9||item.indentState ==10||item.indentState == 4||item.indentState == 6">再来一单</view>
							<view class="btn2" v-if="item.indentState == 0" @tap.stop="bindorderpay(item)">立即付款</view>
						</view>
					</view>
				</view>
			</view>
			<view class="tabs_box" :class="{dis:current == 7}">
				<view class="empty" v-if="order.length < 0">
					<view
						style="display: block; width: 90%; margin: 0 auto; position: fixed;top: 35%;left: 0rpx;right: 0rpx;text-align: center;">
						<image src="../../static/image/emety.png" style="width: 300rpx;height: 300rpx;"></image>
						<view style="color: #CCCCCC;">暂无内容</view>
					</view>
				</view>
				<view v-else>
					<view class="order_box" v-for="(item,index) in order" :key="index"
						@click="bindorderDetail(item.indentNumber)">
						<view class="order_success">
							<view class="order_name" v-if="item.indentState == 0">待付款</view>
							<view class="order_name"
								v-if="item.indentState == 1||item.indentState == 8||item.indentState == 9||item.indentState == 10">
								已取消</view>
							<view class="order_name" v-if="item.indentState == 2">待接单</view>
							<view class="order_name" v-if="item.indentState == 5">待确认</view>
							<view class="order_name" v-if="item.indentState == 3">已接单</view>
							<view class="order_name" v-if="item.indentState == 4">派送中</view>
							<view class="order_name" v-if="item.indentState ==6||item.indentState ==7">
								已完成</view>
							<view class="order_data">{{item.createTime}}</view>
						</view>
						<u-line color="#E6E6E6" />
						<view class="order_city">
							<view class="city_type">
								<view class="type_name" v-if="item.indentType == 1">帮我送</view>
								<view class="type_name" v-if="item.indentType == 2">帮我取</view>
								<view class="type_name" v-if="item.indentType == 3">同城帮买</view>
								<view class="type_name" v-if="item.indentType == 4">同城服务</view>
								<view class="city_text" v-if="item.indentType == 1||item.indentType == 2">
									{{item.itemType}}
								</view>
								<view class="city_text" v-if="item.indentType == 3&&item.buyType == 0">骑手购买</view>
								<view class="city_text" v-if="item.indentType == 3&&item.buyType == 1">指定购买</view>
								<view class="city_text" v-if="item.indentType == 4">{{item.serviceType}}</view>

							</view>
							<view class="city_address">
								<view class="fh_box"
									v-if="item.indentType == 1||item.indentType == 2||item.indentType == 3">
									<view class="fh_image">
										<image src="../../static/image/mai.png" v-if="item.indentType == 3"></image>
										<image src="../../static/image/icon_f.png" v-else></image>
									</view>
									<view class="box">
										<view class="fh_name">{{item.shipAddressDetail}}</view>
										<view class="fh_type" v-if="item.indentType != 3">{{item.shipUserName}}
											<text>{{item.shipUserPhone}}</text>
										</view>
									</view>
								</view>
								<view class="sh_box">
									<view class="sh_image">
										<image src="../../static/image/icon_s.png"></image>
									</view>
									<view class="box">
										<view class="sh_name">{{item.deilveryAddressDetail}}</view>
										<view class="sh_type">{{item.deliveryUserName}}
											<text>{{item.deliveryUserPhone}}</text>
										</view>
									</view>
								</view>
							</view>
						</view>
						<u-line color="#E6E6E6" />
						<view class="order_btn">
							<view class="btn1" v-if="item.indentState ==7||item.indentState == 6&&!item.evaluateMessage"
								@tap.stop="bindcomment(item)">
								去评价
							</view>
							<view class="btn1" v-if="item.indentState == 0||item.indentState == 2"
								@tap.stop="bindorderOff(item)">取消订单</view>
							<view class="btn1" @tap.stop="bindconfirm(item)" v-if="item.indentState == 5">确认订单</view>
							<view class="btn2" @tap.stop="bindorder(item)" v-if="item.indentState == 1||item.indentState == 3||item.indentState ==8||
						item.indentState ==9||item.indentState ==10||item.indentState == 4||item.indentState == 6">再来一单</view>
							<view class="btn2" v-if="item.indentState == 0" @tap.stop="bindorderpay(item)">立即付款</view>
						</view>
					</view>
				</view>
			</view>
			<view class="tabs_box" :class="{dis:current == 8}">
				<view class="empty" v-if="order.length < 0">
					<view
						style="display: block; width: 90%; margin: 0 auto; position: fixed;top: 35%;left: 0rpx;right: 0rpx;text-align: center;">
						<image src="../../static/image/emety.png" style="width: 300rpx;height: 300rpx;"></image>
						<view style="color: #CCCCCC;">暂无内容</view>
					</view>
				</view>
				<view v-else>
					<view class="order_box" v-for="(item,index) in order" :key="index"
						@click="bindorderDetail(item.indentNumber)">
						<view class="order_success">
							<view class="order_name" v-if="item.indentState == 0">待付款</view>
							<view class="order_name"
								v-if="item.indentState == 1||item.indentState == 8||item.indentState == 9||item.indentState == 10">
								已取消</view>
							<view class="order_name" v-if="item.indentState == 2">待接单</view>
							<view class="order_name" v-if="item.indentState == 5">待确认</view>
							<view class="order_name" v-if="item.indentState == 3">已接单</view>
							<view class="order_name" v-if="item.indentState == 4">派送中</view>
							<view class="order_name" v-if="item.indentState ==6||item.indentState ==7">
								已完成</view>
							<view class="order_data">{{item.createTime}}</view>
						</view>
						<u-line color="#E6E6E6" />
						<view class="order_city">
							<view class="city_type">
								<view class="type_name" v-if="item.indentType == 1">帮我送</view>
								<view class="type_name" v-if="item.indentType == 2">帮我取</view>
								<view class="type_name" v-if="item.indentType == 3">同城帮买</view>
								<view class="type_name" v-if="item.indentType == 4">同城服务</view>
								<view class="city_text" v-if="item.indentType == 1||item.indentType == 2">
									{{item.itemType}}
								</view>
								<view class="city_text" v-if="item.indentType == 3&&item.buyType == 0">骑手购买</view>
								<view class="city_text" v-if="item.indentType == 3&&item.buyType == 1">指定购买</view>
								<view class="city_text" v-if="item.indentType == 4">{{item.serviceType}}</view>

							</view>
							<view class="city_address">
								<view class="fh_box"
									v-if="item.indentType == 1||item.indentType == 2||item.indentType == 3">
									<view class="fh_image">
										<image src="../../static/image/mai.png" v-if="item.indentType == 3"></image>
										<image src="../../static/image/icon_f.png" v-else></image>
									</view>
									<view class="box">
										<view class="fh_name">{{item.shipAddressDetail}}</view>
										<view class="fh_type" v-if="item.indentType != 3">{{item.shipUserName}}
											<text>{{item.shipUserPhone}}</text>
										</view>
									</view>
								</view>
								<view class="sh_box">
									<view class="sh_image">
										<image src="../../static/image/icon_s.png"></image>
									</view>
									<view class="box">
										<view class="sh_name">{{item.deilveryAddressDetail}}</view>
										<view class="sh_type">{{item.deliveryUserName}}
											<text>{{item.deliveryUserPhone}}</text>
										</view>
									</view>
								</view>
							</view>
						</view>
						<u-line color="#E6E6E6" />
						<view class="order_btn">
							<view class="btn1" v-if="item.indentState ==7||item.indentState == 6&&!item.evaluateMessage"
								@tap.stop="bindcomment(item)">
								去评价
							</view>
							<view class="btn1" v-if="item.indentState == 0||item.indentState == 2"
								@tap.stop="bindorderOff(item)">取消订单</view>
							<view class="btn1" @tap.stop="bindconfirm(item)" v-if="item.indentState == 5">确认订单</view>
							<view class="btn2" @tap.stop="bindorder(item)" v-if="item.indentState == 1||item.indentState == 3||item.indentState ==8||
						item.indentState ==9||item.indentState ==10||item.indentState == 4||item.indentState == 6">再来一单</view>
							<view class="btn2" v-if="item.indentState == 0" @tap.stop="bindorderpay(item)">立即付款</view>
						</view>
					</view>
				</view>
			</view>
			<view class="empty" v-if="order.length == 0">
				<view
					style="display: block; width: 90%; margin: 0 auto; position: fixed;top: 35%;left: 0rpx;right: 0rpx;text-align: center;">
					<image src="../../static/image/emety.png" style="width: 300rpx;height: 300rpx;"></image>
					<view style="color: #CCCCCC;">暂无内容</view>
				</view>
			</view>
		</view>

	</view>
</template>

<script>
	export default {

		data() {
			return {
				currentIndex: '',
				list: [{
					name: '全部',
					id: ''
				}, {
					name: '待付款',
					id: 0
				}, {
					name: '待接单',
					id: 2
				}, {
					name: '已接单',
					id: 3
				}, {
					name: '派送中',
					id: 4
				}, {
					name: '已完成',
					id: 6
				}, {
					name: '已取消',
					id: 1
				}],
				current: 0,
				bold: false,
				page: 1,
				limit: 10,
				order: [],
				totalPage: 0,
				arr: [],
				showModal: true
			}
		},
		onLoad() {
			// 接单成功通知 268
			//  订单完成通知 269
			let that = this;
			that.$Request.getT('/app/common/type/268').then(res => { //接单成功通知
				if (res.code == 0) {
					if (res.data && res.data.value) {
						that.arr.push(res.data.value)
					}
				}
			})
			that.$Request.getT('/app/common/type/269').then(res => { //订单完成通知
				if (res.code == 0) {
					if (res.data && res.data.value) {
						that.arr.push(res.data.value)
					}
				}
			})
		},
		onShow() {
			this.order = []
			
			let token = this.$queue.getData('token')
			if (token) {
				let current1 = uni.getStorageSync('current')
				if(current1) {
					this.change(current1)
					uni.removeStorageSync('current')
				} else {
					this.page = 1;
					this.orderList()
				}
				
				
				// #ifdef MP-WEIXIN
				//订阅
				if (!uni.getStorageSync('sendorderMsg')) {
					this.openMsg()
				}
				// #endif
			} else {
				uni.navigateTo({
					url:'pages/my/register'
				})
			}
		},
		onUnload() {
			uni.$off('send')
			uni.$off('current')
		},
		methods: {
			// 开启订阅消息
			openMsg() {
				var that = this
				wx.getSetting({
					withSubscriptions: true, //是否获取用户订阅消息的订阅状态，默认false不返回
					success(ret) {
						if (ret.subscriptionsSetting.itemSettings) {
							uni.setStorageSync('sendorderMsg', true)
							uni.openSetting({ // 打开设置页 
								success(rea) {
									console.log(rea.authSetting)
								}
							});
						} else { // 用户没有点击“总是保持以上，不再询问”则每次都会调起订阅消息
							uni.setStorageSync('sendorderMsg', false)
							uni.showModal({
								title: '提示',
								content: '为了更好的体验,请绑定消息推送',
								confirmText: '确定',
								cancelText: '取消',
								success: function(res) {
									if (res.confirm) {
										uni.requestSubscribeMessage({
											tmplIds: that.arr,
											success(re) {
												var datas = JSON.stringify(re);
												if (datas.indexOf("accept") != -1) {
													console.log(re)
												}
											},
											fail: (res) => {
												console.log(res)
											}
										})
										that.showModal = false
									} else if (res.cancel) {
										that.showModal = true
									}
								}
							})
						}
					}
				})
			},
			change(index) {
				console.log(index)
				this.current = index;
				this.currentIndex = this.list[index].id
				this.page = 1;
				this.orderList()
			},
			// 订单获取
			orderList() {
				uni.showLoading({
					title: '加载中...',
					mask: false
				});
				this.$Request.getT('/app/indent/findUserIndent?page=' + this.page + '&limit=' + this.limit +
					'&indentState=' + this.currentIndex).then(res => {
					console.log(res)
					var that = this
					if (res.code === 0) {
						if (that.page == 1) {
							that.order = res.data.list
							that.totalPage = res.data.totalPage
						}
						if (that.page > 1) {
							if (res.data.list.length > 0) {
								that.order = that.order.concat(res.data.list)

							}
						}
					}
					uni.hideLoading()
					
				});
			},
			// 订单详情
			bindorderDetail(indentNumber) {
				console.log(indentNumber)
				uni.navigateTo({
					url: '/pages/order/orderDetail/detail?indentNumber=' + indentNumber
				})
			},
			// 再来一单
			bindorder(e) {
				console.log(e)
				let index = e.indentType
				let current = e.buyType
				if (e.indentType == 1 || e.indentType == 2) {
					uni.navigateTo({
						url: '/pages/Helpsend/Helpsend?indentNumber=' + e.indentNumber + '&index=' + index
					})
				} else if (e.indentType == 3) {
					uni.navigateTo({
						url: '/pages/Helppay/Helppay?indentNumber=' + e.indentNumber + '&index=' + index +
							'&current=' + current
					})
				} else if (e.indentType == 4) {
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
				console.log(e)
				let indentNumber = e.indentNumber
				console.log(indentNumber)
				uni.showModal({
					title: '温馨提示',
					// content: '取消订单平台将扣除您'+e.userFine+'元?',
					content: '确定取消订单？',
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
										this.page = 1;
										this.orderList()
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
						this.page = 1;
						this.orderList()
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

				uni.navigateTo({
					url: '/pages/order/pay/pay?indentNumber=' + e.indentNumber
				})
			}
		},
		onReachBottom: function() {
			if (this.page < this.totalPage) {
				this.page = this.page + 1;
				this.orderList();
			}

		},
		onPullDownRefresh: function() {
			this.page = 1;
			this.orderList();
		},
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

	.empty {
		width: 100%;
		background: #ffffff;
		height: 100vh;
	}

	.tabs_box {
		display: none;
	}

	.dis {
		display: block;
		width: 100%;
	}

	.u-tab-item {
		font-size: 30rpx !important;
		/* color: #666666 !important; */
	}

	.success_box {
		width: 100%;
	}

	.order_box {
		width: 95%;
		margin: 0 auto;
		/* height: 420rpx; */
		background: #FFFFFF;
		margin-top: 20rpx;
		border-radius: 10px;
		padding: 20rpx 0rpx;
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
		font-size: 27rpx;
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
		line-height: 36rpx;
		color: #49A5FF;
		background: #c4e2ff;
		text-align: center;
		font-size: 25rpx;
		margin-left: 20rpx;
		padding: 2rpx 10rpx;
	}

	.city_address {
		width: 90%;
		margin: 0 auto;
		/* margin-top: -10rpx; */
	}

	/* 发货地址 */
	.fh_box {
		display: flex;
		/* height: 80upx; */
		margin-top: 25rpx;
	}

	.fh_image {
		/* flex: 1; */
		width: 10%;
	}

	.box {
		/* flex: 11; */
		width: 85%;
		    overflow: hidden;
	}

	.fh_name {
		font-size: 31rpx;
		font-weight: 600;
		letter-spacing: 2upx;
	}

	.fh_type {
		color: #999999;
		font-size: 28rpx;
		margin-top: 10rpx;
	}

	.fh_type text {
		margin-left: 20upx;
	}

	/* 送货地址 */
	.sh_box {
		display: flex;
		margin-bottom: 14upx;
		margin-top: 25rpx;
	}

	.sh_image {
		/* flex: 1; */
		width:10%;
	}

	.sh_name {
		font-size: 31rpx;
		font-weight: 600;
		letter-spacing: 2upx;
	}

	.sh_type {
		color: #999999;
		font-size: 28rpx;
		margin-top: 10rpx;
	}

	.sh_type text {
		margin-left: 20upx;
	}

	.fh_image image {
		width: 45upx;
		height: 45upx;
	}

	.sh_image image {
		width: 45upx;
		height: 45upx;
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
		font-size: 26rpx;
		line-height: 60upx;
		text-align: center;
		border: 3upx solid #9C9C9C;
		border-radius: 20upx;
		color: #9C9C9C;
		margin-right: 30upx;
	}

	.btn2 {
		width: 170upx;
		line-height: 60upx;
		color: white;
		background: #FF6A04;
		font-size: 27rpx;
		text-align: center;
		margin-right: 30upx;
		border-radius: 20upx;
	}
</style>
