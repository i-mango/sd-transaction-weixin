<template>
	<view style="background-color: #f6f6f6;">
		<view v-if="hasGoods">
			<view class="nothing">空空如也</view>
		</view>
		<view v-else>
			<view class="title">{{typeName}}</view>
			<scroll-view>
				<view v-for="(item,index) in typeList" :key="index">
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
	export default{
		data(){
			return{
				hasGoods:true,
				cateId:null,
				typeName:"",
				userInfo:{},
				typeList:[],
				
			}
		},
		onLoad(options) {
			this.cateId=options.id,
			this.typeName=options.name,
			this.getGoodsList()
		},
		methods:{
			//获取商品信息
			getGoodsList(){
				uni.request({
					url:this.domain+"/commodity/listVo",
					method:'GET',
					data:{
						categoryId:this.cateId
					},
					header:{
						'Content-Type':'application/x-www-form-urlencoded',
					},
					success: (res) => {
						if(res.data.data==undefined){
							this.hasGoods=true
						}else{
							this.hasGoods=false
							this.typeList=res.data.data
						}
						
					}
				})
			},
			//详情页
			detail(item){
				uni.navigateTo({
					url: '/pages/detail/detail?categoryId=' + item.id
				});
			}
		}
	}
</script>

<style>
	.nothing{
		background: url("../../static/nothing/no_comment.png");
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