<template>
	<view class="home-page">
		<!-- 顶部logo -->
		<u-sticky style="top: 0 !important;">
			<view class="toplog">
				<view class="logo">
					<img v-if="nowlang==2" src="../../static/img/top_logo_zh.png" alt="" />
					<img v-if="nowlang==1" src="../../static/img/top_logo_en.png" alt=""  />
				</view>
				<view style="position: absolute; top: -9rpx;right: 20rpx;">
					<img src="../../static/img/qiu.png" class="langecl" @click="showLang = true"
						style="width: 60rpx;height: 60rpx;" />
				</view>
			</view>
		</u-sticky>
		<!-- 轮播图 -->
		<view class="wrap">
			<swiper class="swiper-box" :autoplay="true" :circular="true" :interval="3000" indicator-dots="true"
				selectedBackgroundColor="#fff">
				<swiper-item v-for="item in bannerList">
					<view class="swiper-item">
						<image :src="item" mode=""></image>
					</view>
				</swiper-item>
			</swiper>
		</view>
		<!-- 工具栏 -->
		<view class="home-tool">
			<view class="base" @click="choosePageEvent(1)">
				<!-- 充值 -->
				<view>
					<img src="../../static/home-tool/pay.png" alt="" />
				</view>
				<view class="">
					<text>{{$t('home.cz')}}</text>
				</view>
			</view>
			<view class="base" @click="choosePageEvent(2)">
				<!-- 提现 -->
				<view class="">
					<img src="../../static/home-tool/with.png" alt="" />
				</view>
				<view class="">
					<text>{{$t('home.cash')}}</text>
				</view>
			</view>
			<view class="base" @click="choosePageEvent(3)">
				<!-- 佣金 -->
				<view class="">
					<img src="../../static/home-tool/money.png" alt="" />
				</view>
				<view class="">
					<text>{{$t('home.fund')}}</text>
				</view>
			</view>
			<view class="base" @click="choosePageEvent(4)">
				<!-- 余额 -->
				<view class="">
					<img src="../../static/home-tool/account.png" alt="" />
				</view>
				<view class="">
					<text>{{$t('home.balance')}}</text>
				</view>
			</view>
		</view>
		<!-- 系统消息通知栏 -->
		<view class="system-notice">
			<image style="width: 30rpx;height: 30rpx;" src="../../static/img/notice.png"></image>
			<view class="notice-container">
				<view class="notice-wrapper" :style="{ transform: 'translateY(' + translateY + 'px)' }">
					<view v-for="(notice, index) in noticeList" :key="index" class="notice-item">
						{{ notice.content }}
					</view>
				</view>
			</view>
		</view>
		<!-- 选项卡 -->
		<view class="tab_choose">
			<!-- 红包接龙 -->
			<view class="jl_cl"  v-if="itemProxtList.length>0" :class="chooseIndex==1?' choose_active_left':''" @click="chooseEvent(1)">
				<text>{{$t('home.redJieLong')}}</text>
			</view>
			<!-- 我的红包 -->
			<view  v-if="itemProxtList.length>0" class="me_cl" :class="chooseIndex==2?'   choose_active_right':''" @click="chooseEvent(2)">
				<text>{{$t('home.myRed')}}</text>
			</view>
			<!--当没有代理的时候显示这个-->
			<view class="jl_cl choose_active_left"  v-if="itemProxtList.length==0" >
				<text style="text-align: center;">{{$t('home.redJieLong')}}</text>
			</view>
		</view>
		<!-- 列表 -->
		<!-- 红包接龙 -->
		<scroll-view class="list" scroll-y v-if="chooseIndex==1">
			<view class="" style="height: 25rpx;" v-if="itemList.length>0">

			</view>
			<view v-for="(item, index) in itemList" :key="index" class="list-item"
				:style="'background-image: url('+convertHomeImge(item.groupType)+')'">
				<view class="content">
					<view></view>
					<view class="info">
						<img v-if='nowlang==2' src="../../static/chathome/InRoom_zh.png" @click="enterClick"
							:data-id="item.id" :data-group-type="item.groupType" />
						<img v-if='nowlang==1' src="../../static/chathome/InRoom_en.png" @click="enterClick"
							:data-id="item.id" :data-group-type="item.groupType" />
					</view>
				</view>
			</view>
		</scroll-view>
		<!-- 我的红包 -->
		<scroll-view class="list" scroll-y v-if="chooseIndex==2">
			<view class="list-item" style="background-image: url('../../static/chathome/createRoombg.png')">
				<view class="content">
					<view></view>
					<view class="infos">
						<img v-if="nowlang==2" src="../../static/chathome/createRoom_zh.png" @click="CreateRoomClick" />
						<img v-if="nowlang==1" src="../../static/chathome/createRoom_en.png" @click="CreateRoomClick" />
					</view>
				</view>
			</view>
			<view v-for="(item, index) in itemProxtList" :key="index" class="list-item"
				:style="'background-image: url('+convertHomeImge(item.groupType)+')'">
				<view class="content">
					<view></view>
					<view class="info">
						<img v-if='nowlang==2' src="../../static/chathome/InRoom_zh.png" @click="enterClick"
							:data-id="item.id" :data-group-type="item.groupType" />
						<img v-if='nowlang==1' src="../../static/chathome/InRoom_en.png" @click="enterClick"
							:data-id="item.id" :data-group-type="item.groupType" />
					</view>
				</view>
			</view>
		</scroll-view>
		<!--语言切换 -->
		<u-picker :show="showLang" :columns="columns" keyName="label" :cancelText="$t('comm.cancel')"
			:confirmText="$t('comm.confirm')" @confirm="changeLang" @cancel='showLang = false'
			:defaultIndex='pickerDefaultIndex'>
		</u-picker>

	</view>
</template>

<script>
	const request = require("../../utils/request.js")
	export default {
		data() {
			return {
				showLang: false,
				columns: [
					[{
						label: 'English',
						key: 'en'

					}, {
						label: '中文',
						key: 'zh'
					}]
				],
				pickerDefaultIndex: [],
				langs: 'English',
				lang_key: 'en',
				bannerList: [
					'../../static/banner/1.jpg',
					'../../static/banner/2.jpg',
				],
				noticeList: [],
				translateY: 0, // 初始化偏移量
				noticeHeight: 30, // 单个通知的高度
				intervalId: null,
				itemList: [],//平台放房间
				itemProxtList: [],//代理房间
				logoUrl: {},
				chooseIndex: 1, //1代币红包接龙 2代表我的红包
				nowlang: 1, //1默认应为 2中文
			};
		},
		onShow() {
			this.nowlang = uni.getLocale() == 'en' ? 1 : 2;
			this.getLanguage();
			this.getMainData()
		},
		mounted() {
			// 开始通知滚动
			// this.startNoticeScroll();
		},
		destroyed() {
			// 清除定时器
			clearInterval(this.intervalId);
		},
		methods: {
			//快捷工具事件
			choosePageEvent(e) {
				if (e == 1) {
					uni.switchTab({
						url: '/pages/recharge/recharge'
					})

				}
				if (e == 2) {
					uni.navigateTo({
						url: "/pages/withdraw/withdraw"
					})
				}
				if (e == 3) {

					uni.switchTab({
						url: '/pages/commission/commission'
					})
				}
				if (e == 4) {
					uni.navigateTo({
						url: '/pages/assetsRecord/assetsRecord'
					})
				}
			},
			// 获取当前语言
			getLanguage() {
				this.pickerDefaultIndex = []
				const nowLan = uni.getLocale()
				this.lang_key = nowLan
				switch (nowLan) {
					case "zh":
						this.pickerDefaultIndex = [0]
						this.langs = "中文"
						break;
					case "en":
						this.pickerDefaultIndex = [1]
						this.langs = "English"
						break;
				}
			},
			// 语言切换
			changeLang(a) {
				this.showLang = false
				this.langs = a.value[0].label
				this.lang_key = a.value[0].key
				uni.setLocale(this.lang_key)
				this.$i18n.locale = this.lang_key
				this.nowlang = uni.getLocale() == 'en' ? 1 : 2

			},
			//根据U的值来进行返回图片
			convertHomeImge(e) {
				if (e == 10) {
					return '../../static/chathome/10USDT.png'
				}
				if (e == 50) {
					return '../../static/chathome/50USDT.png'
				}
				if (e == 100) {
					return '../../static/chathome/100USDT.png'
				}
			},
			chooseEvent(e) {
				this.chooseIndex = e
			},
			startNoticeScroll() {
				// 设置定时器，每隔一段时间滚动通知
				this.intervalId = setInterval(() => {
					// 计算偏移量
					this.translateY -= this.noticeHeight;
					// 当到达最后一个通知时，回到起始位置
					if (Math.abs(this.translateY) >= this.noticeHeight * this.noticeList.length) {
						this.translateY = 0;
					}
				}, 2000); // 间隔时间可根据需要调整
			},

			getMainData() {
				var that = this
				var userInfo = uni.getStorageSync('userInfo')
				var data = {
					uid: userInfo.id
				}
				request.tokenRequest({
					url: '/api/v1/main/index',
					method: 'get',
					dataType: 'json',
					data: data,
					ok: function(res) {
						if (res.data.code == '200') {
							if (res.data.data.images != undefined && res.data.data.images.length > 0) {
								// that.bannerList = res.data.data.images
							}
							if (res.data.data.notifies != undefined && res.data.data.notifies.length > 0) {
								that.noticeList = res.data.data.notifies
								that.startNoticeScroll();
							}
							if (res.data.data.roomInfo.platformRooms != undefined && res.data.data.roomInfo.platformRooms.length > 0) {
								that.itemList = res.data.data.roomInfo.platformRooms
							}
							if (res.data.data.roomInfo.proxtRooms != undefined && res.data.data.roomInfo.proxtRooms.length > 0) {
								that.itemProxtList = res.data.data.roomInfo.proxtRooms
							}
							if (res.data.data.logoUrl != undefined) { //官方logo
								that.logoUrl = res.data.data.logoUrl
								uni.setStorageSync('logoUrl', res.data.data.logoUrl)
							}
							if (res.data.data.userInfo != undefined) { //用户信息
								uni.setStorageSync('userInfo', res.data.data.userInfo)
							}

						}
					}
				})
			},
			enterClick(res) {
				var groupId = res.currentTarget.dataset.id;
				var groupType = res.currentTarget.dataset.groupType;
				uni.navigateTo({
					url: '/pages/chat/chat?groupId=' + groupId + '&groupType=' + groupType
				});
			}
		}
	};
</script>

<style scoped>
	/* 默认样式，适用于大屏幕设备 */
	body {
		font-size: 16px;
	}

	/* 在小屏幕上应用不同的样式 */
	@media screen and (max-width: 768px) {
		body {
			font-size: 14px;
		}
	}

	/* 在更小的屏幕上应用不同的样式 */
	@media screen and (max-width: 480px) {
		body {
			font-size: 12px;
		}
	}

	/* 样式可根据实际需求进行调整 */
	page {

		background-image: url('../../static/img/bg.jpg');
		background-repeat: no-repeat;
		background-position: center;
		background-size: cover;

	}

	.home-page {
		padding-bottom: 20rpx;
	}

	.toplog {
		height: 130rpx;
		background-color: #f34b19;

		.logo {
			width: 300rpx;
			margin: auto;

		}

		img {
			width: 300rpx;
			margin-top: 40rpx;
		}

	}

	.swiper-item image {
		margin-left: 30rpx;
		margin-right: 30rpx;
		width: -webkit-fill-available;
		height: 160px;
		border-radius: 15rpx;
	}

	.swiper-box {
		height: 170px;
		margin: 9px auto 0 auto;

	}

	.swiper-item {
		/* #ifndef APP-NVUE */
		display: flex;
		/* #endif */
		flex-direction: column;
		justify-content: center;
		align-items: center;
		color: #fff;
		margin-top: 10rpx;

	}

	.home-tool {
		background-color: #EE4028;
		height: 100rpx;
		margin: 30rpx;
		border-radius: 15rpx;
		display: flex;

		.base {
			width: 25%;
			margin: auto;
			display: flex;
			line-height: 100rpx;
			margin-left: 20rpx;
			margin-right: 20rpx;
			text-align: center;
			cursor: pointer;

			img {
				width: 40rpx;
				height: 40rpx;
				margin-top: 28rpx;
			}

			text {
				padding-left: 10rpx;
				color: white;

			}
		}
	}




	.tab_choose {
		margin-left: 30rpx;
		margin-right: 30rpx;
		display: flex;
		align-items: center;
		font-size: 35rpx;
		font-weight: bolder;
		background-color: #FFFAE2;
		border-top-left-radius: 20rpx;
		border-top-right-radius: 20rpx;
		color: #6B6A6A;
		height: 100rpx;
		margin-top: 20rpx;

		.jl_cl {
			width: 50%;
			text-align: center;
		}

		.me_cl {
			width: 50%;
			text-align: center;
		}
	}

	.choose_no_active {
		color: #6B6A6A;
	}

	.choose_active_left {
		border-top-left-radius: 20rpx;
		height: 100rpx;
		line-height: 100rpx;
		background-color: #FFEB92;
		color: #FD664D;
	}

	.choose_active_right {
		border-top-right-radius: 20rpx;
		height: 100rpx;
		line-height: 100rpx;
		background-color: #FFEB92;
		color: #FD664D;
	}

	.system-notice {
		margin-left: 30rpx;
		margin-right: 30rpx;
		margin-top: 10rpx;
		overflow: hidden;
		display: flex;
		align-items: center;
	}

	.fixed-buttons {
		position: fixed;
		top: 20px;
		right: 20px;
		display: flex;
		flex-direction: column;
	}

	.fixed-buttons button {
		margin-bottom: 10px;
		padding: 8px 16px;
		border-radius: 5px;
	}

	.notice-container {
		height: 30px;
		/* 单个通知的高度 */
		overflow: hidden;
	}

	.notice-wrapper {
		transition: transform 0.5s ease;
		/* 可以调整动画持续时间和效果 */
	}

	.notice-item {
		height: 30px;
		/* 单个通知的高度 */
		line-height: 30px;
		/* 垂直居中 */
		color: #fff;
		text-indent: 15rpx;
	}

	.list {
		width: 92vw;
		margin: auto;
		padding-bottom: 20rpx;
		background-color: #D44F3C;
		border-bottom-left-radius: 20rpx;
		border-bottom-right-radius: 20rpx;
	}

	.list-item {
		border-radius: 12rpx;
		background-size: 85vw 230rpx;
		background-position: center;
		background-repeat: no-repeat;
		height: 260rpx;
		margin-top: 5rpx;
		box-shadow: 0px 10px 10px 2px #D44F3C;

	}

	.content {
		display: flex;
		justify-content: space-between;

	}

	.info {
		display: flex;
		flex-direction: column;
	}



	.info img {
		width: 200rpx;
		margin-right: 80rpx;
		margin-top: 150rpx;
	}

	.infos img {
		width: 200rpx;
		margin-right: 250rpx;
		margin-top: 100rpx;
	}
</style>