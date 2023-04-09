<template>
	<view class="content">
		<view class="header">
			<image :src="member.avatar" class="avatar"></image>
		</view>
		<uni-forms :value="member" ref="form">
			<uni-forms-item name="nickname" label="昵称">
				<uni-easyinput type="text" v-model="member.nickname" placeholder="请输入昵称" />
			</uni-forms-item>
			<uni-forms-item name="phoneNum" label="手机号码">
				<uni-easyinput type="text" v-model="member.phoneNum" placeholder="请输入手机号码" />
			</uni-forms-item>
			<button @click="submitForm" type="primary">保存</button>
		</uni-forms>
	</view>
</template>

<script>
	export default {
		data() {
			return {
				member: {}
			}
		},
		onLoad(){
			this.getMember()
		},
		methods: {
			getMember(){
				var that = this;
				uni.request({
				    url: that.domain + '/sysUser',
					data: {
				        id:state.userInfo.userId
				    },
					method: 'GET',
				    header: {
				        token: uni.getStorageSync('token')
				    },
				    success: (res) => {
				        console.log(res.data);
				        this.member = res.data;
				    }
				});
			},
			submitForm(){
				var that = this;
				uni.request({
					url: that.domain + '/sysUser',
					data: this.member,
					method: 'POST',
					header: {
						token: uni.getStorageSync("token")
					},
					success: (res) => {
						if(res.data.code == 0){
							uni.switchTab({
								url:'/pages/user/user'
							})
						}else{
							
						}
					}
				});
				
			}
		}
	}
</script>

<style>
	.content{
		padding: 10px;
	}
	
	.header{
		padding: 20px;
		text-align: center;
	}
	
	.avatar{
		margin: 0 auto;
		width: 80px;
		height: 80px;
		border-radius: 50%;
	}
</style>
