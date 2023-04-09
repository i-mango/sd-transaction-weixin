<template>
	<view style="background-color:#eeeeee;">
		<view class="user-section">
			<image class="bg" src="../../static/images/user/user-bg1.jpg"></image>
			<view @click="openLogin" class="user-info-box">
				<view>
					<image class="portrait" :src="loginStatus==false ? '../../static/demo6.jpg' : userInfo.avatar"></image>
				</view>
				<view class="info-box">
					<text class="username">{{loginStatus==false ? '登录/注册' : userInfo.nickName}}</text>
				</view>
			</view>
		</view>
		<view class="cover-container"
			:style="[
				{
					transform: coverTransform,
					transition: coverTransition
				}
			]">
			<image src="../../static/images/user/arc.png" class="arc"></image>
		</view>

		<view style="background-color: #ffffff;">
			<uni-list>
				<uni-list-item title="我的订单" link to="/pages/order/order"></uni-list-item>
			</uni-list>
			<uni-row>
				<uni-col :span="6">
					<view style="text-align: center; margin: 5px;" @click="openOrder(1)">
						<view>
							<image style="width: 50upx;height: 50upx;" src="../../static/images/user/pay.png"></image>
						</view>
						<text class="text">待付款</text>
					</view>
				</uni-col>
				<uni-col :span="6">
					<view style="text-align: center;margin: 5px;" @click="openOrder(2)">
						<view>
							<image style="width: 50upx;height: 50upx;" src="../../static/images/user/received.png"></image>
						</view>
						<text class="text">待收货</text>
					</view>
				</uni-col>
				<uni-col :span="6">
					<view style="text-align: center;margin: 5px;" @click="openOrder(3)">
						<view>
							<image style="width: 50upx;height: 50upx;" src="../../static/images/user/evaluation.png"></image>
						</view>
						<text class="text">待评价</text>
					</view>
				</uni-col>
				<uni-col :span="6">
					<view style="text-align: center;margin: 5px;" @click="openOrder(4)">
						<view>
							<image style="width: 50upx;height: 50upx;" src="../../static/images/user/sale.png"></image>
						</view>
						<text class="text">待维修</text>
					</view>
				</uni-col>
			</uni-row>
		</view>
		<view style="margin-top: 5px;">
			<uni-list>
				<uni-list-item title="我的信息" :show-extra-icon="true" showArrow :extra-icon="userInfoIcon" link to="/pages/user/userInfo/userInfo"></uni-list-item>
				<uni-list-item title="我的出售" :show-extra-icon="true" showArrow :extra-icon="sellIcon" link to="/pages/my/info/info"></uni-list-item>
				<uni-list-item title="我的发布" :show-extra-icon="true" showArrow :extra-icon="paperplaneIcon" link to="/pages/my/info/info"></uni-list-item>
				<uni-list-item title="我的求购" :show-extra-icon="true" showArrow :extra-icon="purchaseIcon" link to="/pages/my/info/info"></uni-list-item>
				<uni-list-item title="我的地址" :show-extra-icon="true" showArrow :extra-icon="addressIcon" link to="/pages/user/user-path-list/user-path-list"></uni-list-item>
				<divider></divider>
				<uni-list-item :show-extra-icon="true" showArrow :extra-icon="moreIcon" title="更多设置" link to="/pages/user/user-set"></uni-list-item>
			</uni-list>
		</view>

	</view>
</template>

<script>
	import {mapState} from 'vuex';
	export default{
		data(){
			return{
				coverTransform: 'translateY(0px)',
				coverTransition: '0s',
				userInfoIcon:{
					color: '#4cafd9',
					size: '22',
					type: 'contact-filled'
				},
				sellIcon:{
					color: '#4cafd9',
					size: '22',
					type: 'checkbox-filled'
				},
				paperplaneIcon:{
					color:'#4cafd9',
					size:'22',
					type:'paperplane-filled'
				},
				purchaseIcon:{
					color: '#4cafd9',
					size: '22',
					type: 'cart-filled'
				},
				addressIcon:{
					color: '#4cafd9',
					size: '22',
					type: 'location-filled'
				},
				moreIcon:{
					color: '#4cafd9',
					size: '22',
					type: 'gear-filled'
				}
			}
		},
		computed:{
			...mapState({
				loginStatus:state=>state.user.loginStatus,
				userInfo:state=>state.user.userInfo
			})
		},
		methods:{
			openOrder(e){
				if (this.userInfo.loginStatus==false) {
					uni.showToast({
						title:"请先登录！"
					})
					uni.navigateTo({
						url: '../login/login',
					});
				}else{
					uni.navigateTo({
						url: "/pages/order/order?tabIndex="+e,
					});
				}
			},
			//跳转登录
			openLogin(){
				if (this.loginStatus==false) {
					uni.navigateTo({
						url: '../login/login',
					});
				}
			}
		}
	}
</script>

<style>
	.text{
		font-size: 12px;
	}
	.user-section {
		height: 420upx;
		padding: 100upx 30upx 0;
		position: relative;
	}
	.bg {
		position: absolute;
		left: 0;
		top: 0;
		width: 100%;
		height: 70%;
		filter: blur(1px);
		opacity: 0.7;
	}
	.user-info-box {
		height: 180upx;
		display: flex;
		align-items: center;
		position: relative;
		z-index: 1;
	}
	.portrait {
		width: 130upx;
		height: 130upx;
		border: 5upx solid #fff;
		border-radius: 50%;
	}
	.username {
		font-size: $font-lg + 6upx;
		color: $font-color-dark;
		margin-left: 20upx;
	}
	.cover-container {
		background: $page-color-base;
		margin-top: -150upx;
		padding: 0 30upx;
		position: relative;
		background: #f5f5f5;
		padding-bottom: 20upx;
	}

	.arc {
		position: absolute;
		left: 0;
		top: -34upx;
		width: 100%;
		height: 36upx;
	}
</style>
