<template>
	<view>	
		
		<card headTitle="关于" bodyStyle="background:#ffffff;">
			<uni-list-item :title="item.title" 
			v-for="(item,index) in list"
			:key="index" @click="navigate(item.path)"></uni-list-item>
		</card>
		
		
		<view class="p-3" v-if="userInfo.loginStatus==true">
			<button type="default" @click="logout">退出登录</button>
		</view>
	</view>
</template>

<script>
	import card from "../../components/card.vue"
	import { mapMutations } from 'vuex'
	export default{
		components: {
			card
		},
		data(){
			return{
				list:[
					{ title:"关于平台",path:"about" },
					{ title:"意见反馈",path:"" },
					{ title:"协议规则",path:"" },
					{ title:"资质证件",path:"" },
					{ title:"用户协议",path:"" },
					{ title:"隐私政策",path:"" },
				],
				userInfo:{}
			}
		},
		onShow() {
			this.userInfo = JSON.parse(uni.getStorageSync('userInfo'))
		},
		methods:{
			...mapMutations(['logout']),
			logout(){
				var that = this
				uni.showModal({
					title: '提示',
					content: '是否退出登录',
					success: function(res) {
						if (res.confirm) {
							uni.request({
								url:that.domain+'/logout',
								method: 'POST',
								data:{},
								header: {
								    token: that.userInfo.token
								},
								success: () => {
									that.logout()
									uni.removeStorageSync('userInfo')
									uni.showToast({
										title: '退出成功',
										icon: 'none'
									});
									uni.navigateBack({
										delta:1
									})
								}
							})
						} else if (res.cancel) {
							console.log('用户点击取消');
						}
					},
				})
			}
		}
	}
</script>

<style>
	page{
		background: #EEEEEE;
	}
</style>