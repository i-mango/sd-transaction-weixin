<template>
	<view class="container">
		<view class="left-bottom-sign"></view>
		<view>
			<image src="../../static/arrow.png" class="arrow" @click="navBack"></image>
		</view>
		<view class="wrapper">
			<uni-forms :value="formData" ref="form" :rules="rules">
				<uni-forms-item name="username" label="用户名称:">
					<uni-easyinput type="text" v-model="formData.username" placeholder="请输入用户名" />
				</uni-forms-item>
				<uni-forms-item name="password" label="用户密码:">
					<uni-easyinput type="password" v-model="formData.password" placeholder="请输入密码" />
				</uni-forms-item>
				<uni-forms-item name="phoneNum" label="电话号码:">
					<uni-easyinput type="text" v-model="formData.phoneNum" placeholder="请输入手机号" />
				</uni-forms-item>
				<uni-forms-item name="nickName" label="用户昵称">
					<uni-easyinput type="text" v-model="formData.nickName" placeholder="请输入昵称" />
				</uni-forms-item>
				<uni-forms-item name="avatar" label="用户头像:">
					<uni-file-picker 
					limit="1" 
					:del-icon="false" 
					disable-preview 
					:imageStyles="imageStyles"
					file-mediatype="image" 
					:auto-upload="false" 
					@select="upload">
					</uni-file-picker>
				</uni-forms-item>
				<button class="confirm-btn" @click="toRegit">注册</button>
			</uni-forms>
		</view>
		<view class="login-section">
					已有账号?
			<text @click="openLogin">马上登录</text>
		</view>
	</view>
</template>

<script>
export default {
	data() {
		return {
			formData:{
				username: '',
				password: '',
				nickName:'',
				phoneNum:'',
				avatar:''
			},
			//表单校验规则
			rules:{
				username:{
					rules:[{
						required:true,
						errorMessage:'用户名不能为空！'
					}
					]
				},
				password:{
					rules:
					[
						{
							required:true,
							errorMessage:'密码不能为空！'
						},
						{
							validateFunction: function(rule, value, data, callback) {
								if (value.length > 12 || value.length < 6) {
									callback('密码长度在6-12位！')
								} else {
									callback();
								}
							}
						}
					]
				},
				phoneNum:
				{
					rules:
					[
						{
							required:true,
							errorMessage:'手机号不能为空！'
						},
						{
							validateFunction: function(rule, value, data, callback) {
								let iphoneReg = (
									/^(13[0-9]|14[1579]|15[0-3,5-9]|16[6]|17[0123456789]|18[0-9]|19[89])\d{8}$/
								); //手机号码
								if (!iphoneReg.test(value)) {
									callback('手机号码格式不正确，请重新填写')
								}
							}
						}
					]
				}
			},
		}
	},
	methods: {
		navBack() {
			uni.navigateBack();
		},
		upload(e) {
			const tempFilePaths = e.tempFilePaths;//e是获取的图片源
			uni.uploadFile({
				url: this.domain + '/upload', //上传图片的后端接口
				filePath: tempFilePaths[0],
				name: 'file',
				success: res => {
					this.formData.avatar = res.data
				}
			})
		},
		toRegit() {
			if(this.username&&this.password&&this.nickName&&this.phoneNum){
				uni.showLoading({
					title:'注册中',
					mask: true
				})
				uni.request({
					url: this.domain + '/register',
					method: 'POST',
					data: {
					    username: this.formData.username,
						password: this.formData.password,
						nickName:this.formData.nickName,
						phoneNum:this.formData.phoneNum,
						avatar:this.formData.avatar
					},
					header: {
					    "Content-Type": "application/x-www-form-urlencoded"
					},
					success: (res) => {
						uni.hideLoading()
						if(res.data.code==200){
							uni.showToast({
								title:'注册成功',
								icon:'none'
							})
							uni.navigateTo({
								url:'/pages/login/login'
							})
						}else{
							uni.showToast({
								title:res.data.msg,
								icon:'error'
							})
						}
					}
				})
			}else{
				uni.showModal({
					title:"填写信息不完整",
					showCancel:false
				})
			}
		},
		
		openLogin(){
			uni.navigateTo({
				url:'/pages/login/login'
			})
		}
	}
}
</script>

<style lang="scss">
page {
	background: #fff;
}

.container {
	position: relative;
	width: 100vw;
	height: 100vh;
	overflow: hidden;
	background: #fff;
}
.arrow{
	margin-top: 5px;
	width: 20px;
	height: 20px;
}

.left-bottom-sign {
	position: absolute;
	left: -280upx;
	bottom: -260upx;
	border: 100upx solid #d0d1fd;
	border-radius: 50%;
	padding: 150upx;
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
	// margin-top: 70upx;
	background: $uni-color-primary;
	color: #fff;
	font-size: $font-lg;

	&:after {
		border-radius: 100px;
	}
}

.login-section {
	position: absolute;
	left: 0;
	margin-top: 150upx;
	width: 100%;
	font-size: $font-sm + 2upx;
	color: $font-color-base;
	text-align: center;

	text {
		color: $font-color-spec;
		margin-left: 10upx;
	}
}
</style>
