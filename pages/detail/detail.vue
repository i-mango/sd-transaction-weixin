<template>
	<view class="container">
		<swiper class="home-swiper" :autoplay="autoplay" :interval="interval" :duration="duration" indicator-dots="true">
			<swiper-item v-for="pic in goodsList">
				<image :src="pic" class="slide-image" />
			</swiper-item>
		</swiper>
		<!-- 卖家信息 -->
		<view class="user">
			<image class="avatar" :src="userPicture"></image>
			<view>
				<view class="nickname">{{nickName}}</view>
			</view>
		</view>
		<view class="introduce-section">
					<text class="title">{{goodsTitle}}</text>
					<view class="price-box">
						<text class="price-tip">￥</text>
						<text class="price">{{goodsPrice}}</text>
					</view>
					<view class="bot-row">
						<text>收藏数:{{goodsLike}}</text>
						<text></text>
						<image style="width: 40px;height: 40px;" v-if="starVisible" @click="like" src="../../static/star-null.png"></image>
						<image style="width: 40px;height: 40px;" v-else @click="unlike" src="../../static/star.png"></image>
					</view>
		</view>
				<!-- 中间部分 -服务 -->
				<view class="c-row b-b">
					<text class="tit">服务</text>
					<view class="bz-list con">
						<text>7天无理由退货 ·</text>
						<text>假一赔十 · </text>
					</view>
				</view>
				<!-- 评价 -->
				<view class="eva-section">
					<view class="e-header">
						<uni-list-item link value="好评率 100%">
							<view slot="title">
								<view class="van-cell-text">评价(68)</view>
							</view>
						</uni-list-item>
					</view>
					<view class="eva-box">
						<image width="100%" height="100%" round class="myPhoto" src="../../static/demo6.jpg">
						</image>
						<view class="right">
							<text class="name">果果</text>
							<text class="con">商品收到了,质量不错,试了一下,卖家大姐人也很好，我有点事情还是等我忙完专门拿给我的</text>
							<view class="bot">
								<text class="time">2023-04-01 19:21</text>
							</view>
						</view>
					</view>
				</view>
				<!-- 图文详情 -->
				<view class="detail-desc">
					<view class="d-header">
						<text>———— 图文详情 ————</text>
					</view>
					<view class="d-photo">
						<image width="100%" height="100%" class="photo" v-for="(photo,index) in goodsList" :src="photo"
						 :key="index">
						</image>
					</view>
				</view>
				<view class="footer">
					<uni-goods-nav :fill="true"  :options="options" :buttonGroup="buttonGroup"  @click="onClick" @buttonClick="buttonClick" />
				</view>
		
	</view>
</template>

<script>
	import {mapState} from 'vuex';
	export default{
		data(){
			return{
				goodsList:[],
				goodsTitle:"",
				userPicture:"",
				nickName:"",
				goodsUserId:null,
				goodsPrice:0,
				goodsLike:0,
				goodsId:null,
				categoryId:null,
				starVisible:true,
				autoplay: true, //是否自动切换
				interval: 3000, //自动切换时长
				duration: 1200, //滑动动画时长
				options: [{
							icon: 'headphones',
							text: '客服'
						}, {
							icon: 'shop',
							text: '店铺',
							info: 2,
							infoBackgroundColor:'#007aff',
							infoColor:"red"
						}, {
							icon: 'cart',
							text: '购物车',
							info: 2
						}],
					    buttonGroup: [{
					      text: '加入购物车',
					      backgroundColor: '#ff0000',
					      color: '#fff'
					    },
					    {
					      text: '立即购买',
					      backgroundColor: '#ffa200',
					      color: '#fff'
					    }
					    ]
			}
		},
		onLoad(options) {
			this.categoryId=options.categoryId,
			this.userPicture=options.userPicture,
			this.nickName=options.nickName,
			this.getGoodsList(this.categoryId)
		},
		computed: {
			...mapState({
				loginStatus:state=>state.user.loginStatus,
				userInfo:state=>state.user.userInfo
			})
		},
		methods:{
			like() {
				this.goodsLike=this.goodsLike+1
				this.starVisible=false
			},
			unlike(){
				this.goodsLike=this.goodsLike-1
				this.starVisible=true
			},
			getGoodsList(e){
				uni.request({
					url:this.domain +"/commodity/listVo"+e,
					method:'GET',
					header:{
						'Content-Type':'application/x-www-form-urlencoded',
					},
					success: (res) => {
						this.goodsList=JSON.parse(res.data.data.subimages)
						this.goodsTitle=res.data.data.title
						this.goodsPrice=res.data.data.price
						this.goodsLike=res.data.data.likes
						this.goodsId=res.data.data.id
						this.goodsUserId = res.data.data.userId
					}
				})
			},
			onClick(e){
				if(e.content.text==="购物车"){
					if(this.loginStatus=false){
						uni.showToast({
							title:"请先登录",
							icon:'error'
						})
						uni.navigateTo({
							url:"/pages/login/login"
						})
					}else{
						uni.switchTab({
							url:"/pages/cart/cart"
						})
					}
				}
				
			},
			buttonClick (e) {    
					if(e.index==0){
						this.options[2].info++
						this.addCart()
					}else{
						uni.navigateTo({
							url:"/pages/pay/pay?goodsId="+this.goodsId
						})
					}
			},
			//加入购物车
			addCart(){
				if(this.userInfo.userId == this.goodsUserId){
					uni.showToast({
						title:"不能添加自己发布的商品到购物车哦~"
					})
					return
				}
				uni.request({
					url:this.domain+"/shoppingCart",
					method:'POST',
					data:{
						goodsId:this.goodsId,
						userId:this.userInfo.userId
					},
					header:{
						token:this.userInfo.token
					},
					success: (res) => {
						console.log("购物车",res)
						if(res.data.code==200){
							uni.showToast({
								title:"加入购物车成功"
							})
						}
					}
				})
			}
		},
	}
</script>

<style>
	page {
			background: #f8f8f8;
			padding-bottom: 160rpx;
		}
	 
		.container {
			width: 100%;
			height: 100%;
			margin: 0 auto;
		}
	 
		.carousel {
			height: 722rpx;
			position: relative
		}
	 
		/*轮播控件*/
		.home-swiper {
			width: 100%;
			height: 100;
		}
		.slide-image{
			width: 100%;
			height: 100%;
		}
		.swiper-item {
			display: flex;
			justify-content: center;
			align-content: center;
			height: 750upx;
			overflow: hidden;
		}
	 
		.slide-image {
			width: 100%;
			height: 100%;
		}
	 
		/* 商品介绍 */
		.introduce-section {
			background: #ffffff;
			padding: 20rpx 30rpx;
		}
	 
		.introduce-section .title {
			font-size: 32rpx;
			color: #303133;
			height: 50rpx;
			line-height: 50rpx;
		}
	 
		.introduce-section .price-box {
			display: flex;
			align-items: baseline;
			height: 50rpx;
			padding: 10rpx 0;
			font-size: 26rpx;
			color: #fa436a;
		}
	 
		.introduce-section .price {
			font-size: 34rpx;
		}
	 
		.introduce-section .m-price {
			margin: 0 12rpx;
			color: #909399;
			text-decoration: line-through;
		}
	 
		.introduce-section .coupon-tip {
			align-items: center;
			padding: 4rpx 10rpx;
			background: #fa436a;
			font-size: 24rpx;
			color: #ffffff;
			border-radius: 6rpx;
			line-height: 1;
			transform: translateY(-4rpx);
		}
	 
		.introduce-section .bot-row {
			display: flex;
			align-items: center;
			height: 30rpx;
			font-size: 24rpx;
			color: #909399;
		}
	 
		.introduce-section .bot-row text {
			flex: 1;
		}
	 
		.share-section {
			display: flex;
			align-items: center;
			color: #606266;
			background: linear-gradient(left, #fdf5f6, #fbebf6);
			padding: 12rpx 30rpx;
		}
	 
		.share-section .share-icon {
			display: flex;
			align-items: center;
			width: 70upx;
			height: 30upx;
			line-height: 1;
			border: 1px solid #fa436a;
			border-radius: 4upx;
			position: relative;
			overflow: hidden;
			font-size: 22upx;
			color: #fa436a;
	 
		}
	 
		.share-section .share-icon:after {
			content: '';
			width: 50rpx;
			height: 50rpx;
			border-radius: 50%;
			left: -20rpx;
			top: -12rpx;
			position: absolute;
			background: #fa436a;
		}
	 
		.share-section .icon-xingxing {
			position: relative;
			z-index: 1;
			font-size: 24rpx;
			margin-right: 35rpx;
			color: #fff;
			line-height: 1;
	 
		}
	 
		.share-section .tit {
			font-size: 28rpx;
			margin-left: 10rpx;
		}
	 
		.share-section .icon-bangzhu1 {
			padding: 10rpx;
			font-size: 30rpx;
			line-height: 1;
		}
	 
		.share-section .share-btn {
			flex: 1;
			text-align: right;
			font-size: 24rpx;
			color: #fa436a;
	 
		}
	 
		.share-section .icon-you {
			font-size: 24rpx;
			margin-left: 4rpx;
			color: #fa436a;
		}
	 
		.shareInformation {
			width: 100%;
			height: 275px;
		}
	 
		.c-list {
			width: 100%;
			font-size: 26rpx;
		}
	 
		.c-list .van-cell-text {
			width: 25%;
			display: flex;
			float: left;
			color: #606266;
		}
	 
		.c-list .con .select-text {
			margin-right: 10rpx;
			color: #303133;
		}
	 
		.typeInformation {
			width: 100%;
			margin-bottom: 5%;
		}
	 
		.typeInformation .a-t {
			display: flex;
			width: 90%;
			margin: 0 auto;
		}
	 
		.typeInformation .a-t image {
			width: 130rpx;
			height: 130rpx;
			border-radius: 8rpx;
		}
	 
		.typeInformation .a-t .right {
			display: flex;
			flex-direction: column;
			padding-left: 24rpx;
			font-size: 26rpx;
			color: #606266;
			line-height: 42rpx;
		}
	 
		.typeInformation .a-t .right .price {
			font-size: 32rpx;
			color: #fa436a;
			margin-bottom: 10rpx;
	 
		}
	 
		.typeInformation .a-t .right .select-text {
			margin-right: 10rpx;
		}
	 
		.typeInformation .attr-list {
			display: flex;
			flex-direction: column;
			font-size: 30rpx;
			color: #606266;
			padding-top: 30rpx;
			padding-left: 10rpx;
		}
	 
		.typeInformation .item-list {
			padding: 20rpx 0 0;
			display: flex;
			flex-wrap: wrap;
		}
	 
		.typeInformation .item-list .selected {
			background: #fbebee;
			color: #fa436a;
		}
	 
		.typeInformation .item-list text {
			display: flex;
			-webkit-box-align: center;
			-webkit-align-items: center;
			align-items: center;
			-webkit-box-pack: center;
			-webkit-justify-content: center;
			justify-content: center;
			background: #eee;
			margin-right: 20rpx;
			margin-bottom: 20rpx;
			border-radius: 100rpx;
			min-width: 60rpx;
			height: 60rpx;
			padding: 0 20rpx;
			font-size: 28rpx;
			color: #303133;
		}
	 
		.typeInformation .vButton {
			width: 90%;
			margin: 0 auto;
			margin-top: 30rpx
		}
	 
		.c-row {
			display: flex;
			align-items: center;
			padding: 20rpx 30rpx;
			position: relative;
			font-size: 26rpx;
			color: #606266;
			background-color: #FFFFFF;
		}
	 
		.c-row .tit {
			width: 23%;
		}
	 
		.c-row .con-list {
			flex: 1;
			display: flex;
			flex-direction: column;
			color: #303133;
			line-height: 40rpx;
		}
	 
		.c-row .bz-list {
			height: 40rpx;
			font-size: 26rpx;
			color: #303133;
		}
	 
		.c-row .con {
			flex: 1;
		}
	 
		.c-row .bz-list text {
			display: inline-block;
			margin-right: 30rpx;
		}
	 
		.eva-section {
			background-color: #FFFFFF;
			display: flex;
			flex-direction: column;
			margin-top: 16rpx;
		}
	 
		.eva-section .eva-box {
			display: flex;
			padding: 20rpx 32rpx;
		}
	 
		.eva-section .eva-box .myPhoto {
			flex-shrink: 0;
			width: 80rpx;
			height: 80rpx;
		}
	 
		.eva-box .right {
			flex: 1;
			display: flex;
			flex-direction: column;
			font-size: 28rpx;
			color: #606266;
			padding-left: 26rpx;
		}
	 
		.eva-box .right .con {
			font-size: 28rpx;
			color: #303133;
			padding: 20rpx 0;
		}
	 
		.eva-box .right .bot {
			font-size: 24rpx;
			display: flex;
			color: #909399;
		}
	 
		.detail-desc {
			background-color: #FFFFFF;
			margin: 16rpx 0px;
			/* height: 2026px; */
		}
	 
		.detail-desc .d-header {
			align-items: center;
			height: 80rpx;
			font-size: 30rpx;
			color: #303133;
			position: relative;
			text-align: center;
			line-height: 80rpx;
	 
		}
	 
		.detail-desc .d-header text {
			padding: 0 20rpx;
			background: #FFFFFF;
			position: relative;
		}
	 
		.detail-desc .d-photo {
			width: 100%;
			/* height: 400px; */
		}
		.photo{
			text-align: center;
		}
		.page-bottom {
			position: fixed;
			left: 30rpx;
			bottom: 30rpx;
			display: flex;
			width: 690rpx;
			height: 100rpx;
			border-radius: 16rpx;
	 
		}
		.footer{
			width: 100%;
				position: fixed;
				bottom: 0;
				left: 0;
		}
</style>