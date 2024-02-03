<template>
	<view class="share">
		<view class="info">

			<text> {{$t('share.today')}} {{shareInfo.toDayMyPushCount}} </text>
			<text> {{$t('share.yesterday')}} {{shareInfo.yesterDayMyPushCount}}</text>
			<text> {{$t('share.week')}} {{shareInfo.weekMyPushCount}} </text>
			<text> {{$t('share.month')}} {{shareInfo.monthMyPushCount}}</text>
			<text> {{$t('share.total')}} {{shareInfo.allMyPushCount}} </text>


		</view>
		<text class="title">{{$t('share.linkdes')}}</text>
		<view class="link">
			
			<view class="detail">
				<text>{{sheraAddress}}</text>
				<view>
					<image style="width: 45rpx;height: 45rpx;" src="../../static/image/cope.png" @click="copyAddress">
					</image>
				</view>
			</view>
		</view>
		<!-- 佣金奖金数额规则 -->
			<view class="title">{{$t('share.role.title')}} </view>
		<view class="push">
		
			<view>{{$t('share.role.info')}}</view>
			<view class="user"> 
				<view style="display: flex;flex-direction: column;">
					<text>{{$t('share.count')}} > 100</text>
					<text>{{$t('share.fund')}} > {{$t('share.month')}}5000USDT!</text>
				</view>
			</view> 
		    <img src="../../static/share/share-link.png" alt="" srcset="" />
			<view style="font-size: 28rpx;">1.{{$t('share.role.info.one')}}。</view>
			<view style="font-size: 28rpx;">2.{{$t('share.role.info.two')}}</view>
		</view>

	</view>
</template>

<script>
	const request = require("../../utils/request.js")
	export default {
		data() {
			return {
				shareInfo: {},
				sheraAddress: ''
			}
		},
		onShow() {
			//获取缓存中的code
			const userInfos=uni.getStorageSync('userInfo'); 
			const base = window.location.origin;
			this.sheraAddress=`${base}?code=` + userInfos.code
			this.getExpand()
		},
		mounted() {

		},
		methods: {
			copyAddress() {
				// 调用剪贴板API复制地址到剪贴板
				uni.setClipboardData({
					data: this.sheraAddress,
					success() {
						uni.showToast({
							title: 'Address copied',
							icon: 'none'
						});
					},
					fail() {
						uni.showToast({
							title: 'Replication failure',
							icon: 'none'
						});
					}
				});

			},
			//  推广数据接口
			getExpand() {
				var userInfo = uni.getStorageSync('userInfo')
				var data = {
					uid: userInfo.id,
				}
				var that = this
				request.tokenRequest({
					url: '/api/v1/main/myGetPushInfo',
					method: 'get',
					dataType: 'json',
					data: data,
					ok: function(res) {
						uni.hideLoading()
						if (res.data.code == '200') {
							that.shareInfo = res.data.data
							
						}
						/**
						 * "data": {
        "groupTotal": 0,            //团队总人数
        "yesterDayMyPushCount": 0,  //昨日推广数
        "weekMyPushCount": 1,       //本周推广数
        "monthMyPushCount": 1,      //本月推广数
        "rateLv4": "2",             //等级4佣金费率
        "rateLv5": "2.5",           //等级5佣金费率
        "rateLv2": "1",             //等级2佣金费率
        "rateLv3": "1.5",           //等级3佣金费率
        "rateLv1": "0.5",           //等级1佣金费率
        "toDayMyPushCount": 1,      //今日推广数
        "allMyPushCount": 1         //总推广数量
    }
						 */
					}
				})
			},
		},
	}
</script>

<style>
	page {
		background: #EFEFEF;
	
	}
	.info {
		display: flex;
		flex-direction: column;
		background: white;
		margin: 24rpx;
		display: flex;
		padding: 24rpx;
		border-radius: 24rpx;
		font-size: 30rpx;

		text {
			padding-top: 15rpx;
			color: #949494;
		}

	}

	.link {

		padding: 12rpx 24rpx;
		font-size: 30rpx;
	}
 
	.link .detail {
		background: white;
		display: flex;
		align-items: center;
		font-size: 28rpx;
		margin-top: 18rpx;
		padding: 20rpx;
		border-radius: 12rpx;
		justify-content: space-between;
	}

	.push {
		margin-top: 24rpx;
		padding: 24rpx;
		background: white;
		border-radius: 24rpx 24rpx 0 0;
		font-size: 30rpx;
		margin:24rpx ;
	}

	.push text {
		color: red;
		
	}
.title { 
		padding-left:50rpx;
		padding-top: 24rpx;
	}
	 
	.push .user {
		display: flex;
		align-items: center;
		margin-top: 24rpx;
	}
	 
</style>