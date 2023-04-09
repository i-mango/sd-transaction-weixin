<template>
	<view class="container">
		<view class="left-bottom-sign"></view>
		<view>
			<image src="../../static/arrow.png" class="arrow" @click="navBack"></image>
		</view>
		<view class="right-top-sign"></view>
		<text class="sign-title">欢迎来到智慧社区跳蚤市场</text>
		<view class="left-top-sign">LOGIN</view>
		<view class="wrapper">
			<uni-forms :value="formData" ref="form" :rules="rules">
				<uni-forms-item name="username">
					<uni-easyinput prefixIcon="contact" type="text" v-model="formData.username" placeholder="请输入用户名" />
				</uni-forms-item>
				<uni-forms-item name="password">
					<uni-easyinput prefixIcon="locked" type="password" v-model="formData.password"
						placeholder="请输入密码" />
				</uni-forms-item>
				<button class="confirm-btn" form-type="submit" @click="toLogin">登录</button>
			</uni-forms>
		</view>
		<view class="register-section">
			还没有账号?
			<text @click="toRegist">马上注册</text>
		</view>
	</view>
</template>

<script>
	import {
		mapMutations
	} from 'vuex';
	export default {
		data() {
			return {
				formData: {
					username: '',
					password: ''
				},
				//表单校验规则
				rules: {
					username: {
						rules: [{
							required: true,
							errorMessage: '请输入用户名'
						}]
					},
					password: {
						rules: [{
								required: true,
								errorMessage: '请输入密码'
							},
							{
								format: 'password',
								errorMessage: '请输入正确的密码'
							}
						]
					}
				}
			}
		},
		methods: {
			...mapMutations(['login']),
			navBack() {
				uni.switchTab({
					url: "/pages/user/user"
				})
			},
			toRegist() {
				uni.navigateTo({
					url: "/pages/register/register"
				});
			},
			toLogin() {
				if (this.formData.username && this.formData.password) {
					this.$refs.form.validate().then(() => {
						uni.showLoading({
							title: '登录中...',
							mask: true
						});
						uni.request({
							url: this.domain + '/login',
							method: 'POST',
							data: {
								username: this.formData.username,
								password: this.formData.password
							},
							header: {
								"Content-Type": "application/json;charset=utf-8"
							},
							success: (res) => {
								let userToken = {
									token: res.data.data
								}
								uni.request({
									url: this.domain + '/userInfo',
									method: 'GET',
									header: {
										token: userToken.token
									},
									success: (response) => {
										let userInfo = {
											userId: response.data.data.id,
											phoneNum: response.data.data.phoneNum,
											username: response.data.data.username,
											nickName: response.data.data.nickName,
											token: userToken.token,
											avatar: response.data.data.picture,
											loginStatus:true
										}
										this.login(userInfo)
									}
								})
								uni.hideLoading()
								if (res.data.code == 200) {
									// 状态存储
									uni.showToast({
										title: '登录成功',
										icon: 'none'
									});
									uni.switchTab({
										url: '/pages/user/user'
									})
								} else {
									uni.showToast({
										title: res.data.msg,
										icon: 'error'
									})
								}
							}
						})
					})
				} else {
					uni.showToast({
						title: '信息填写不完整',
						icon: 'error'
					})
				}
			}
		}
	}
</script>

<style lang="scss">
	page {
		background: #fff;
	}

	.container {
		padding-top: 30px;
		position: relative;
		width: 100vw;
		height: 100vh;
		overflow: hidden;
		background: #fff;
	}

	.arrow {
		width: 20px;
		height: 20px;

	}

	.sign-title {
		margin-left: 40px;
		background-image: linear-gradient(to right, orange, purple);
		color: transparent;
		-webkit-background-clip: text;
		font-size: 20px;
	}

	.left-top-sign {
		font-size: 120upx;
		color: $page-color-base;
		position: relative;
		left: -16upx;
	}

	.right-top-sign {
		position: absolute;
		top: 1upx;
		right: -80upx;
		z-index: 95;

		&:before,
		&:after {
			display: block;
			content: '';
			width: 400upx;
			height: 80upx;
			background: #b4f3e2;
		}

		&:before {
			transform: rotate(50deg);
			border-radius: 0 50px 0 0;
		}

		&:after {
			position: absolute;
			right: -198upx;
			top: 0;
			transform: rotate(-50deg);
			border-radius: 50px 0 0 0;
		}
	}

	.left-bottom-sign {
		position: absolute;
		left: -270upx;
		bottom: -100upx;
		border: 100upx solid #d0d1fd;
		border-radius: 50%;
		padding: 180upx;
	}

	.wrapper {
		position: relative;
		z-index: 90;
		background: #fff;
		padding-bottom: 40upx;
	}

	.confirm-btn {
		width: 630upx;
		height: 76upx;
		line-height: 76upx;
		border-radius: 50px;
		margin-top: 50upx;
		background: $uni-color-primary;
		color: #fff;
		font-size: $font-lg;

		&:after {
			border-radius: 100px;
		}
	}

	.register-section {
		position: absolute;
		left: 0;
		margin-top: 300upx;
		width: 100%;
		font-size: $font-sm + 2upx;
		color: $font-color-base;
		text-align: center;

		text {
			color: $font-color-spec;
			margin-left: 10upx;
		}
	}

	.input-item {
		display: flex;
		border-bottom: 1upx solid #C0C4CC;
		margin-top: 10upx;
	}

	.inputText {
		width: 75%;
		margin-top: 5upx;
		margin-left: 20upx;
		float: left;
	}

	.title {
		float: left;
	}
</style>
