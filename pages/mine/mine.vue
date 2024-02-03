<template>
	<view>
		<view class="top" style="height: 32vh;padding-bottom: 20rpx;">
			<view class="action">
				<view> </view>
				<image @click="settingClick" src="../../static/image/setting.png"></image>
			</view>
			<view class="avatar">
				<!-- userInfo.avatar -->
				<view class="" style="margin: auto;width: 100rpx;height: 100rpx;">
					<image src="../../static/avatar/1.jpg" style="border-radius: 10rpx;"></image>
				</view>
				<view class="info" style="text-align: center;padding-top: 35rpx;">
					<view style="font-size: 34rpx;">
						{{userInfo.nickName}}
					</view>
					<view>
						Lv.1
					</view>

				</view>
			</view>
			 
			<view class="blance" style="text-align: center;margin-top:30rpx;"> {{userInfo.balance}} </view>
			<view class="hint" style="font-size: 23rpx;">
				<u-row>
					<u-col span="7.5" text-align="right">
						{{$t('mine.walletBalance')}}
					</u-col>
					<u-col span="1">
						<image @click="refreshClick" style="margin-left: 24rpx;" src="../../static/img/refresh-white.png">
						</image>
					</u-col>
					<u-col span="4" text-align="right" style="padding-right: 35rpx;">
							{{$t('mine.inviteCode')}}: {{userInfo.code}}
					</u-col>
				</u-row> 
			</view>
		</view>

		<view class="" style="height: 59vh;background-color: #EFEFEF; ">
			<view style="height: 40rpx;background-color: #EFEFEF;padding: 0rpx;margin: 0rpx;"></view>
			<view class="wallet" style="border-radius: 10rpx;margin-top: 20rpx;">
				<view @click="rechargeClick" style="cursor: pointer;">
					<image src="../../static/mine/recharge.png"></image>
					<text> {{$t('mine.recharge')}}</text>
				</view>
				<view @click="withdrawalClick" style="cursor: pointer;">
					<image src="../../static/mine/withdrawl.png"></image> 
					<text>
						{{$t('mine.withdrawal')}}
					</text>
				</view>
			</view>

			<view style="height: 40rpx;background-color: #EFEFEF;padding: 0rpx;margin: 0rpx;"></view>

			<view style="background-color: #fff; height: 33vh;margin-left: 30rpx;margin-right: 30rpx;border-radius: 10rpx;font-size: 28rpx; ">
				<view class="item" @click="itemClick" data-index="2" style="width: 43%;margin-top: 20rpx;cursor: pointer;">
					<image src="../../static/mine/billing.png"></image>
					<view>
						<text> {{$t('mine.balanceHistory')}} </text>
						<label> {{$t('mine.balanceHistoryView')}} </label>
					</view>
				</view>


				<view class="item" @click="itemClick" data-index="3"  style="width: 43%;;margin-top: 20rpx;">
					<image src="../../static/mine/money-funds.png"></image>
					<view>
						<text> {{$t('mine.fundHistory')}} </text>
						<label> {{$t('mine.fundHistoryView')}} </label>
					</view>
				</view>

				<view class="item" @click="itemClick" data-index="4"  style="width: 43%;">
					<image src="../../static/mine/redlogo.png"></image>
					<view>
						<text> {{$t('mine.redRecord')}} </text>
						<label> {{$t('mine.redRecordView')}} </label>
					</view>
				</view>

				<view class="item" @click="settingClick" data-index="5"  style="width: 43%;">
					<image src="../../static/mine/safe.png"></image>
					<view>
						<text> {{$t('mine.security')}} </text>
						<label> {{$t('mine.securityView')}} </label>
					</view>
				</view>

				<view class="item" @click="itemClick" data-index="6"  style="width: 43%;">
					<image src="../../static/mine/share.png"></image>
					<view>
						<text> {{$t('mine.sharing')}} </text>
						<label> {{$t('mine.sharingView')}} </label>
					</view>
				</view>

				<view class="item" @click="itemClick" data-index="8"  style="width: 43%;">
					<image src="../../static/mine/service.png"></image>
					<view>
						<text> {{$t('mine.service')}} </text>
						<label> {{$t('mine.serviceView')}} </label>
					</view>
				</view>

			</view>

		</view>
	</view>
</template>

<script>
	const request = require("../../utils/request.js")
	export default {
		data() {
			return {
				userInfo: {}
			}
		},
		onShow() {
			this.userInfo = uni.getStorageSync('userInfo')
		},
		methods: {
			//设置
			settingClick() {
				uni.navigateTo({
					url: "/pages/modify/modify"
				})
			},
			//刷新
			refreshClick() {
				var data = {
					uid: '',
				}
				var that = this
				uni.showLoading({
					title: 'Refreshing'
				})
				request.tokenRequest({
					url: '/api/v1/main/getUserInfo',
					method: 'get',
					dataType: 'json',
					data: data,
					ok: function(res) {
						uni.hideLoading()
						if (res.data.code == '200') {
							that.userInfo = res.data.data
							uni.setStorageSync('userInfo', res.data.data)
							setTimeout(function() {
								uni.showToast({
									title: 'Update successfully'
								})
							}, 1000);
						}
					}
				})
			},
			//充值
			rechargeClick() {

				uni.switchTab({
					url: "/pages/recharge/recharge"
				})
			},
			//提现
			withdrawalClick() {
				uni.navigateTo({
					url: "/pages/withdraw/withdraw"
				})
			},
			itemClick(e) {
				var index = e.currentTarget.dataset.index
				var url = ''
				switch (index) {
					case "1":
						url = ''
						break
					case '2':
						url = '/pages/assetsRecord/assetsRecord'
						break
					case "3":
						url = '/pages/commission/commission'
						uni.switchTab({
							url: url
						})
						return
						break
					case "4":
						url = '/pages/redpackage/redpackage'
						break
					case "5":
						url = ''
						break
					case "6":
						url = '/pages/share/share'
						uni.switchTab({
							url: url
						})
						return
						break
					case "7":
						url = ''
						break
					case "8":
						url = ''
						break

				}
				uni.navigateTo({
					url: url
				})
			}
		}
	}
</script>

<style>
	page {
		background-image: url('../../static/img/me_bg.jpg');
		background-repeat: no-repeat;
		background-position: center;
		background-size: cover;
	}

	.top {

		display: flex;
		flex-direction: column;
		color: white;
		padding: 24rpx;
	}

	.top .action {
		display: flex;
		align-items: center;
		padding-bottom: 50rpx;
		justify-content: space-between;
	}

	.action image {
		width: 40rpx;
		height: 40rpx;
	}

	.avatar {
		/* display: flex; */
		align-items: center;
	}

	.avatar image {
		height: 120rpx;
		width: 120rpx;
	}

	.avatar .info {
		display: flex;
		font-size: 28rpx;
		flex-direction: column;
		margin-left: 20px;
	}

	.avatar .info>view {
		line-height: 48rpx;
	}

	.info .tag {
		background: #fd7c26;
		border-radius: 25rpx;
		padding: 10;
		font-weight: 100;
		margin-left: 15px;
	}

	.top .blance {
		color: #FFEB94;
		font-size: 48rpx;
		/* display: flex; */
		margin-top: 12rpx;
		/* align-items: center; */
	}

	.top .blance image {
		height: 35rpx;
		width: 35rpx;
	}

	.top .hint {}

	.top .hint image {
		height: 20rpx;
		width: 20rpx;
		margin-top: 10rpx;  
	}

	.wallet {
		display: flex;
		background: #fff;
		box-shadow: 3rpx #eee;
		margin-left: 30rpx;
		margin-right: 30rpx;

	}

	.wallet>view {
		width: 50%;
		padding: 32rpx 48rpx;
		display: flex;
		align-items: center;
	}

	.wallet image {
		height: 60rpx;
		width: 60rpx;
	}
	.wallet text{
		text-indent: 12rpx;
	}
	.item {
		float: left;
		padding: 24rpx;
		width: 300rpx;
		display: flex;
	}

	.item image {
		width: 60rpx;
		height: 60rpx;
	}

.item text { 
		text-indent: 12rpx;
	}
	.item label {
		color: #999;
		font-size: 24rpx;
		text-indent: 12rpx;
	}

	.item>view {
		display: flex;
		flex-direction: column;
	}
</style>