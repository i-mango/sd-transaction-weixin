<template>
	<view style="background-color: #f6f6f6;">
		<view class="search-box">
			<view class="search">
				<input confirm-type="search" v-model="name" @confirm="confirm" bindconfirm="confirm" placeholder="卧室阳台秋千 双人"></input>
				<button class="search-txt" @click="confirm">搜 索</button>
			</view>
		</view>
		<uni-notice-bar scrollable show-icon text="购买二手物品请谨慎,保护自身财产安全,诚信交易,打造绿色社区交易环境！" />
		<uni-grid :column="4" :square="false" :showBorder="false">
			<uni-grid-item v-for="item in tabBars">
				<view class="nav-view" @click="openDetailList(item)">
					<image :src="item.caPicture"></image>
					<view>{{item.name}}</view>
				</view>
			</uni-grid-item>
		</uni-grid>
		<view class="title">新品发布<span style="color: grey; float: right;">查看更多>></span></view>
		<scroll-view>
			<view v-for="(item,index) in goodsList" :key="index">
				<view class="content" @click="detail(item)">
					<image class="img" :src="item.mainimage"></image>
					<view class="main-content">
						<text class="main-title">{{item.name}}</text>
						<view class="body-content">
							<view class="body-content-s">{{item.title}}</view>
							<view class="body-content-s" v-if="item.subtitle!=null">{{item.subtitle}}</view>
							<view style="font-weight:bold;font-size:20px;color: #e64340;">￥{{item.price}}</view>
							<view class="user">
								<image class="avatar" :src="item.userPicture"></image>
								<label class="nickname">{{item.nickName}}</label>
							</view>
						</view>
					</view>
				</view>
			</view>
		</scroll-view>
		<view v-for="content in tabBars">
			<view class="title" v-if="typeList[content.id-1]!=[]">{{content.name}}<span style="color: grey; float: right;" @click="openDetailList(content)">查看更多>></span></view>
			<scroll-view>
				<view v-for="(item,index) in typeList[content.id-1]" :key="index">
					<view class="content" @click="detail(item)">
						<image class="img" :src="item.mainimage"></image>
						<view class="main-content">
							<text class="main-title">{{item.name}}</text>
							<view class="body-content">
								<view class="body-content-s">{{item.title}}</view>
								<view class="body-content-s" v-if="item.subtitle!=null">{{item.subtitle}}</view>
								<view style="font-weight:bold;font-size:20px;color: #e64340;">￥{{item.price}}</view>
								<view class="user">
									<image class="avatar" :src="item.userPicture"></image>
									<label class="nickname">{{item.nickName}}</label>
								</view>
							</view>
						</view>
					</view>
				</view>
			</scroll-view>
		</view>
		
	</view>
	
		
</template>

<script>
	export default {
		data() {
			return {
				value:0,
				name:"",
				categoryId:0,
				userInfo:{},
				tabBars:[],
				goodsList:[],
				typeList:[]
			}
		},
		onShow() {
			this.userInfo = JSON.parse(uni.getStorageSync('userInfo'))
			// 初始化事件
			this.__init()
			this.getList()
		},
		methods: {
			// 初始化事件
			__init(){
				// 获取顶部选项卡
				uni.request({
					url:this.domain+"/category",
					method:"GET",
					header:{
						'Content-Type':'application/json;charset=UTF-8',
					},
					success: (res) => {
						this.tabBars = res.data.data
						// this.typeList=[]
						this.tabBars.forEach(item=>{
							uni.request({
								url:this.domain+"/commodity/listVo",
								method:'GET',
								data:{
									categoryId:item.id
								},
								header:{
									token:this.userInfo.token
								},
								success: (response) => {
									this.typeList.push(response.data.data)
									console.log(this.typeList)
									console.log(response.data.data)
								}
							})
						})
					}
				})
			},
			getList(){
				uni.request({
					url:this.domain+"/commodity/listVo",
					method:"GET",
					header:{
						'Content-Type':'application/x-www-form-urlencoded',
					},
					success: (res) => {
						this.goodsList=res.data.data
					}
				})
			},
			//详情
			detail(item) {
				uni.navigateTo({
					url: '/pages/detail/detail?categoryId=' + item.id+'&userPicture='+item.userPicture+"&nickName="+item.nickName
				});
			},
			confirm(){
				uni.navigateTo({
					url:'/pages/searchEvery/searchEvery?name='+this.name
				})
			},
			openDetailList(e){
				uni.navigateTo({
					url:"/pages/search/search?id="+e.id+'&name='+e.name
				})
			},
		},
		
	}
</script>

<style>
	.search-box{
		height: 60px;
		width: 100%;
	}
	.search{
		width: 90%;
		height: 70%;
		margin-left: 20px;
		margin-top: 20px;
		border: #20c3fd 1px solid;
		border-radius: 30px;
	}
	.search input{
		height: 100%;
		margin-left: 1rem;
		display: inline-block;
		width: 60%;
	}
	.search-txt{
		width: 30%;
		height: 100%;
		line-height: 40px;
		text-align: center;
		border-radius: 30px;
		color: white;
		background-color: #20c3fd;
		float: right;
	}
	.nav-view{
		width: 100px;
		height: 100px;
		text-align: center;
	}
	.nav-view image{
		width: 60%;
		height: 60%;
	}
	.title {
		font-size: 16px;
		font-weight: bold;
		margin: 10px;
		border-left: 3px solid #e64340;
		padding-left: 4px;
	}
	.user {
		display: flex;
		align-items: center;
		margin-left: 50px;
	}
	
	.user .avatar {
		width: 30px;
		height: 30px;
		border-radius: 50%;
	}
	
	.user .nickname {
		font-size: 12px;
		color: #555;
	}
	.content{
	    display: flex;
	    background-color: #ffffff;
	    border-radius: 20px;
	    margin: 10upx;
	}
	.img{
	    margin-left: 20upx;
		width: 300px;
		height: 200px;
		border-radius: 25%;
	}
	.main-content{
	    margin: 20upx;
	    align-content: flex-start;
	    /*父元素设置超过宽度显示...*/
	    overflow: hidden;
	    text-overflow: ellipsis;
	}
	.mian-title{
	    font-size:35upx;
	    font-weight: bold;
	    /*过长的子元素设置不换行*/
	    white-space: nowrap;
	}
	.body-content{
	    /*让字在一条水平线上*/
	    align-items: baseline;
	    display: flex;
	    flex-direction:row;
	    flex-wrap:wrap;
	    margin-top:5upx;
	}
	.body-content-s{
	    font-size:27upx;
	    color:#808080;
		width: 100%;
	    margin-top:10upx;
	    height: 45upx;
	}
</style>
