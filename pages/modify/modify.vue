<template>
	<view class="container">
		<view class="avatar-container">
			<!-- 头像显示区域 -->
			<!-- 	<image class="avatar" :src="avatarUrl" mode="aspectFill"></image> -->
			<img :src="avatarUrl" class="avatar" />
			<button style="background: white;" class="changeA" @click="changeAvatar"> {{$t('modify.changeAvatar')}}
			</button>
		</view>

		<view class="form">
			<!-- 昵称输入框 -->
			<view class="form-item">
				<text> {{$t('modify.nickname')}} </text>
				<input v-model="nickname" type="text" :placeholder="$t('modify.nicknameView')"></input>
			</view>

			<!-- 密码输入框 -->
			<view class="form-item">
				<text>{{$t('modify.newPassword')}}</text>
				<input v-model="password" type="password" :placeholder="$t('modify.newPasswordView')"></input>
			</view>

			<view class="form-item">
				<text>{{$t('modify.passwordAgain')}}</text>
				<input v-model="passwordAgain" type="password" :placeholder="$t('modify.passwordAgainView')"></input>
			</view>
			<!-- 提交按钮 -->

			<button class="save" @click="saveChanges"> {{$t('modify.save')}} </button>

		</view>
		<button class="logout" @click="loginOut"> {{$t('modify.logOut')}} </button>
		<u-toast ref="uToast" />
	</view>
</template>

<script>
	const request = require("../../utils/request.js")
	export default {
		data() {
			return {
				avatarUrl: '/static/image/avarta.png', // 头像地址
				nickname: '', // 昵称
				password: '', // 密码
				passwordAgain: '', //再次输入
				userInfo: uni.getStorageSync('userInfo')

			};
		},
	 
		onLoad() { 
			const userInfo = uni.getStorageSync('userInfo')
			this.avatarUrl = userInfo.avatar 
		},
		methods: {
			changeAvatar() {
				// 处理更换头像的逻辑
				// 可以调用uni-app提供的图片选择/上传方法
			},
			saveChanges() {
				// 处理保存修改的逻辑，可以发送请求将修改后的信息提交到后端服务器
				// 示例中只是简单地打印修改后的信息
			 if (this.password.trim() ==""|| this.nickname.trim() == '') {
			  
			 	this.$refs.uToast.error(this.$t('modify.please-enter-info'))
			 	return 
			 }
				// 可以在此处添加逻辑将数据提交到后端进行保存
				var that = this
				if (this.nickname != undefined && this.nickname != '') {
					uni.showModal({
						title: that.$t('showModal.title'),
						content:that.$t('modify.isUpdateNickname'),
						success: (res) => {
							if (res.confirm) {
								var data = {
									id: that.userInfo.id,
									nickName: that.nickname
								}
								that.modifyData(data)
							}
						}
					})
					return
				}
				if (this.password != undefined && this.password != '') {
					if (this.passwordAgain == "" && this.passwordAgain != undefined) { 
						 
						 this.$refs.uToast.error(that.$t('modify.passwordAgainView'))
						return
					}
				}
				if (this.passwordAgain != undefined && this.passwordAgain != '') {
					if (this.password == "" && this.password != undefined) {
					 
					 this.$refs.uToast.error(that.$t('modify.newPasswordView'))
						  
						return
					}
				}
				if (that.password != this.passwordAgain) {
 
					this.$refs.uToast.error(that.$t('login.login-no-same'))
					return 
				}
			
				if (this.password != undefined && this.password != '') {
					uni.showModal({
						title: that.$t('showModal.title'),
						content: that.$t('modify.isUpdatePassword')+'?',
						success: (res) => {
							if (res.confirm) {
								if (that.password == this.passwordAgain) {
									var data = {
										id: that.userInfo.id,
										passWord: that.password
									}
									that.modifyData(data)
								} else {
									 
									this.$refs.uToast.error(that.$t('login.login-no-same'))
								}
							}
						}
					})
				}
			},

			modifyData(data) {
				uni.showLoading({
					title:this.$t('modify.updateLoading')
				})
				var that=this;
				request.tokenRequest({
					url: '/api/v1/ucenter/updateUserInfo',
					method: 'get',
					dataType: 'json',
					data: data,
					ok: function(res) {
						uni.hideLoading()
						if (res.data.code == '200') {
							 
							that.$refs.uToast.success(that.$t('modify.updateSuccess'))
							uni.setStorageSync('userInfo', res.data.data)
							that.nickname = ""
							that.password = ""
							that.passwordAgain = ''

						}
					}
				})
			},

			loginOut() {
				const local = uni.getLocale();
				uni.showModal({
					title: local == 'en' ? 'Reminder' : '提示', // Adjusted the conditional statement
					content: local == 'en' ? 'Are you sure to log out' :
					'是否确认退出登录', // Adjusted the conditional statement
					confirmText: local == 'en' ? 'Confirm' : '退出登录', // Adjusted the conditional statement
					cancelText: local == 'en' ? 'Cancel' : '取消', // Adjusted the conditional statement
					success: (res) => {
						if (res.confirm) {
							uni.removeStorageSync('token'); 
							this.$refs.uToast.success( this.$t('modify.logout')+'!')
							setTimeout(function() {
								uni.reLaunch({
									url: '/pages/login/login'
								});
							}, 1500);
						}
					}
				});
			}

		},
	};
</script>

<style>
	page {
		background-color: #EFEFEF;
	}

	/* 样式可以根据实际需求进行修改 */
	.container {
		padding: 20px;
	}

	.avatar-container {
		text-align: center;
		margin-bottom: 20px;
	}

	.avatar-container button {
		width: 250rpx;
		font-size: 32rpx;
		margin: 0rpx auto;
	}

	.avatar {
		width: 100px;
		height: 100px;
		border-radius: 10rpx;
		margin-bottom: 10px;
	}

	.form {
		background-color: #fff;
		padding: 20px;
		border: 1prx #eee solid;
		border-radius: 5px;
	}

	.form-item {
		margin-bottom: 15px;
		font-size: 32rpx;
		display: flex;
		border-bottom: 1px solid #ccc;
		;
	}

	.form-item text {
		width: 200rpx;
		padding-top: 18rpx;


	}

	input {
		width: 70%;
		padding: 10px;
		font-size: 28rpx;
	}

	.save {
		font-size: 32rpx;
		width: 400rpx;
		background: #FE7A65;
		color: #fff;
		border-radius: 50rpx;
		height: 90rpx;
		line-height: 90rpx;
		margin-top: 80rpx;
		margin-bottom: 15rpx;
	}

	.logout {
		font-size: 32rpx;
		width: 400rpx;
		margin-top: 32rpx;
		background: #FABF5F;
		color: #fff;
		border-radius: 50rpx;
		height: 90rpx;
		line-height: 90rpx;

	}
</style>