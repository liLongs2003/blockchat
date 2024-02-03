<template>
	<view class="login-page">
		<view style="position: absolute; top: 10rpx;right: 20rpx;">
			<img src="../../static/img/qiu.png" class="langecl" @click="showLang = true"
				style="width: 60rpx;height: 60rpx;" />
		</view>

		<view class="" style="height: 28vh;">
			<image style="width: 150rpx;height: 150rpx;margin-top: 250rpx;" src="../../static/img/logo.png"></image>
		</view>
		<view>
			<h3 style="padding-bottom: 48rpx; text-align: center;color: #fff;">
				{{!showRegister? $t('login.welcome-login'):$t('login.welcome-register')}}
			</h3>
		</view>

		<div class="login" v-if="!showRegister">
			<view class="logo-title">
				<view class="v1">
					<text> {{$t('login.user-login')}}</text>
				</view>
				<view class="v2">
					<text style="color: #A9A9A9;">{{$t('login.none-account')}}</text>
					<text style="padding-left: 20rpx;color: red;"
						@click="switchToRegister">{{$t('login.go-register')}}</text>
				</view>
			</view>
			<view class="input_cl">
				<view class="title_input">
					{{$t('login.account')}}
				</view>
				<view>
					<input v-model="username" type="text" :placeholder="$t('login.please-enter-account')" />
				</view>
			</view>
			<view class="input_cl">
				<view class="title_input">
					{{$t('login.password')}}
				</view>
				<view>
					<input v-model="password" type="password" :placeholder="$t('login.please-enter-password')" />
				</view>
			</view>

			<button class="login-btn" style="margin-top: 48rpx;background-color: #FF745F;" :disabled="lockLoginBtn"
				@tap="login">{{$t('login.log-in')}}</button>
			<button class="register-btn" style="background-color: #FCBE5B;">{{$t('login.forget-password')}}</button>
		</div>
		<div class="register" v-if="showRegister">
			<view class="logo-title">
				<view class="v1">
					<text> {{$t('login.user-register')}}</text>
				</view>
				<view class="v2">
					<text style="color: #A9A9A9;">{{$t('login.existing-account')}}</text>

					<text style="padding-left: 20rpx;color: red;" @click="switchToLogin">{{$t('login.go-login')}}</text>
				</view>
			</view>
			<view class="input_cl">
				<view class="title_input">
					{{$t('login.nickName')}}
				</view>
				<view>
					<input v-model="nickName" type="text" :placeholder="$t('login.please-enter-nickName')" />
				</view>
			</view>
			<view class="input_cl">
				<view class="title_input">
					{{$t('login.account')}}
				</view>
				<view>
					<input v-model="newUsername" type="text" :placeholder="$t('login.please-enter-account')" />
				</view>
			</view>

			<view class="input_cl">
				<view class="title_input">
					{{$t('login.set-password')}}
				</view>
				<view>
					<input v-model="newPassword" type="password" :placeholder="$t('login.please-enter-password')" />
				</view>
			</view>

			<view class="input_cl">
				<view class="title_input">
					{{$t('login.confirm-password')}}
				</view>
				<view>
					<input v-model="newPasswordAgain" type="password"
						:placeholder="$t('login.please-enter-password-again')" />
				</view>
			</view>
			<button class="register-btn" style="margin-top: 48rpx;background-color: #FF745F;"
				:disabled="lockBtnRegister" @click="register">
				{{$t('login.sign-in')}} </button>
			<button class="login-btn" style="background-color: #FCBE5B;" @click="switchToLogin">
				{{$t('login.back-to-login')}} </button>
		</div>
		<!-- 使用你的 tabbar 组件 -->
		<!--语言切换 -->
		<u-picker :show="showLang" :columns="columns" keyName="label" :cancelText="$t('comm.cancel')"
			:confirmText="$t('comm.confirm')" @confirm="changeLang" @cancel='showLang = false'
			:defaultIndex='pickerDefaultIndex'>
		</u-picker>
		<u-toast ref="uToast" />
	</view>

</template>


<script>
	const request = require("../../utils/request.js")
	// '@dcloudio/uni-ui'
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
				nickName: '',
				username: "",
				password: "",
				newUsername: "",
				newPassword: "",
				newPasswordAgain: "",
				showRegister: false,
				lockBtnRegister: false,
				lockLoginBtn: false,
				code: ''

			};
		},
		onLoad(option) {
			// http://localhost:8080/?code=HJHQ
			if (option['code'] != undefined) {
				this.code = option['code']
			} else {
				this.code = ''
			}		 
			this.getLanguage()
		},

		methods: {
			// 获取当前语言
			getLanguage() {
				this.pickerDefaultIndex = []
				
				const nowLan = uni.getLocale();
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


			},

			login() {
				// 处理登录逻辑
				// 检查账号密码是否正确
				// 如果正确则登录，否则提示错误信息
				var that = this
				if (this.username == '') {
					this.$refs.uToast.error(this.$t('login.please-enter-account'))
					return
				}
				if (!/^[a-zA-Z0-9]+$/.test(this.username)) {
					this.$refs.uToast.error(this.$t('login.account-only-letters-or-numbers'))
					return
				}
				if (this.password == '') {

					this.$refs.uToast.error(this.$t('login.please-enter-password'))
					return
				}
				var data = {
					loginName: that.username,
					loginPass: that.password
				}
				uni.showLoading({
					title: that.$t('login.login-loading') + '...'
				})
				that.lockLoginBtn = true;
				request.request({
					url: '/api/v1/ucenter/login',
					method: 'post',
					dataType: 'json',
					data: data,
					ok: function(res) {
						if (res.data.code == '200') {

							that.$refs.uToast.success(that.$t('login.login-success'))

							uni.setStorageSync('token', res.data.data.token)
							uni.setStorageSync('header', res.data.data.tokenHeader)

							uni.setStorageSync('userInfo', res.data.data.userInfo)
							setTimeout(() => {
								uni.switchTab({
									url: '/pages/home/home'
								})
							}, 1000);
						} else {

							that.$refs.uToast.error(that.$t(res.data.message))
						}
						that.lockLoginBtn = false;
					},
					err: function(e) {
						console.log(e)
					}
				})
			},

			register() {
				// 处理注册逻辑
				// 创建新账号并进行相关操作
				var that = this
				if (this.newUsername == '') {
					this.$refs.uToast.error(this.$t('login.please-enter-account'))
					return
				}
				if (this.nickName == '') {
					this.$refs.uToast.error(this.$t('login.please-enter-nickName'))
					return
				}
				if (!/^[a-zA-Z0-9]+$/.test(this.newUsername)) {
					this.$refs.uToast.error(this.$t('login.account-only-letters-or-numbers'))
					return
				}
				if (this.newPassword == '') {
					this.$refs.uToast.error(this.$t('login.please-enter-password'))
					return
				}
				if (this.newPasswordAgain == '') {
					this.$refs.uToast.error(this.$t('login.please-enter-password-again'))

					return
				}
				if (this.newPasswordAgain != this.newPassword) {
					this.$refs.uToast.error(this.$t('login.login-no-same'))
					return
				}

				var data = {
					fatherCode: that.code,
					loginName: that.newUsername,
					loginPass: that.newPassword,
					nickName: that.nickName
				}
				//锁定注册按钮
				uni.showLoading({
					title: that.$t('login.register-loading') + '...'
				})
				that.lockBtnRegister = true;
				request.request({
					url: '/api/v1/ucenter/register',
					method: 'post',
					dataType: 'json',
					data: data,
					ok: function(res) {
						if (res.data.code == '200') {
							//清空文本信息
							that.newUsername = ''
							that.newPassword = ''
							that.newPasswordAgain = ''
							uni.showModal({
								title: 'Tips',
								content: that.$t('login.register-success') + '，' + that.$t(
									'login.please-login'),
								showCancel: false,
								confirmText: that.$t('login.login'),
								success: () => {
									that.switchToLogin()
								}
							})
						} else {

							that.$refs.uToast.error(that.$t(res.da.message))
						}
						that.lockBtnRegister = false;
						uni.hideLoading()
					}
				})
			},
			switchToRegister() {
				this.showRegister = true;
			},
			switchToLogin() {
				this.showRegister = false;
			}
		}
	};
</script>

<style scoped>
	page {
		background-image: url('../../static/img/login_bg.jpg');
		background-repeat: no-repeat;
		background-position: center;
		background-size: cover;

	}

	.login-page {

		display: flex;
		flex-direction: column;
		align-items: center;
		height: 100vh;

	}

	.title_input {
		width: 170rpx;
	}

	.logo-title {
		display: flex;
		width: 100vw;
		padding-top: 50rpx;

		.v1 {
			width: 50%;
			font-size: 33rpx;
			color: red;
			font-weight: 400;
			padding-left: 20rpx;
		}

		.v2 {
			width: 50%;
			font-size: 28rpx;
			text-align: right;
			padding-right: 20rpx;
		}
	}


	.login {
		border-top-right-radius: 50rpx;
		border-top-left-radius: 50rpx;
		background-color: #fff;

		width: 100vw;
		height: 80vh;
		padding-left: 20rpx;
		padding-right: 20rpx;
		margin-top: 10rpx;
	}

	.register {
		border-top-right-radius: 50rpx;
		border-top-left-radius: 50rpx;
		background-color: #fff;
		/* margin-top: 32rpx; */
		width: 100vw;
		height: 80vh;
		padding-left: 20rpx;
		padding-right: 20rpx;
		margin-top: 10rpx;
	}

	.input_cl {
		display: flex;
		margin: 24rpx 10rpx;
		margin-top: 35rpx;
		padding: 20rpx;
		border-bottom: #A9A9A9 solid 1rpx;

		input {
			color: #A9A9A9;
			padding-left: 20rpx;

		}

	}

	button {
		margin: 24rpx auto;
		width: 500rpx;
		height: 100rpx;
		line-height: 100rpx;
		border-radius: 40rpx;
		color: white;
		font-size: 32rpx;
	}

	.login-btn {
		background: #4cb81b;
	}

	.register-btn {
		background: #2093f5;
	}

	/* ... Your existing styles ... */
	.lang-icon {
		width: 40rpx;
		height: 40rpx;
		margin-top: 20rpx;
	}

	.lang-popup {
		display: flex;
		flex-direction: column;
		align-items: center;
	}

	.picker {
		margin-top: 20rpx;
	}

	.lang-btn {
		margin: 10rpx;
		font-size: 28rpx;
	}

	.header {
		position: absolute;
		top: 20rpx;
		right: 20rpx;
		height: 20vh;
	}

	.lang-icon {
		width: 35rpx;
		height: 35rpx;
	}
</style>