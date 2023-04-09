<template>
<view>
	 <!-- 购物车为空 -->
	<view v-if="userInfo.loginStatus==false|| hasGoods==false">
		<view class="nothing">
			<text style="font-size: 25px;color: grey;">空空如也</text>
			<button class="nothingbutton" @click="toShopping">去逛逛</button>
		</view>
	</view>
	<!-- 购物车商品列表 -->
	<view v-else>
		<!-- 通过循环商品呈现出每个商品 -->
		<view class="goods-detail" v-for="(item,index) in goodsList" :key="index">
			<view class="detail-left">
				<view class="goods-left">
					<!-- 商品的选择框 -->
					<checkbox-group @change="selected(item)">
						<label>
							<checkbox :value="item.price" class="selected" color="#555555" :checked="item.checked"/>
						</label>
					</checkbox-group>
					<image :src="item.mainimage" mode="widthFix" style="width: 150rpx;"></image>
				</view>
				<view class="size">
					<text style="font-size: 25rpx;">{{item.name}}</text>
					<text style="font-size: 25rpx; color: gray;">{{item.title}}</text>
					<text class="goods-price">￥{{item.price}}/件</text>
				</view>
			</view>
			<view class="detail-right">
				<text class="subtract" @click="reduce(item)">-</text>
				<text class="num">{{item.num}}</text>
				<text @click="add(item)" class="add">+</text>
			</view>
		</view>
	</view>
	<!-- 全选总计 -->
	<view class="end">
		<view class="end-left">
			<checkbox-group @change="selectgoods()">
				<label>
					<checkbox  :checked="allchecked" />全选
				</label>
			</checkbox-group>
			<view>
				总计：<text style="color: #f00;font-weight: bold;">￥ {{totalPrice}}</text>
			</view>
		</view>
		<view class="end-right">
			结算({{totalNum}})
		</view>
	</view>
</view>
	
</template>

<script>
	export default{
		data(){
			return{
				userInfo:{},
				hasGoods:false,
				goodsList:[],
				totalNum:0,
				totalPrice:0,
				//全选是否选中
				allchecked:false,
			}
		},
		onShow() {
			this.userInfo = JSON.parse(uni.getStorageSync('userInfo'))
			this.getCartGoods()
		},
		methods:{
			//去逛逛
			toShopping(){
				uni.switchTab({
					url:"/pages/index/index"
				})
			},
			//获取购物车商品信息
			getCartGoods(){
				uni.request({
					url:this.domain+"/shoppingCart/"+this.userInfo.userId,
					method:'GET',
					header:{
						token:this.userInfo.token
					},
					success: (res) => {
						if(res.data.data.length==0){
							this.hasGoods=false
						}else{
							this.hasGoods=true
							this.goodsList = []
							res.data.data.forEach((item) =>  {
								uni.request({
									url: this.domain + "/commodity/" + item.goodsId,
									method: "GET",
									header:{
										token:this.userInfo.token
									},
									success: (response) => {
										response.data.data.checked=item.checked
										response.data.data.num=1
										response.data.data.cartId=item.id
										this.goodsList.push(response.data.data)
									}
								})
							})
						}
					}
				})
			},
			//选择商品
			selected(item){
				if(item.checked==1){
					item.checked=0
					this.allchecked=false
				}else{
					// 判断每一个商品是否是被选择的状态
					item.checked==1
					const cartList = this.goodsList.forEach(item => {
						return item.checked ==1
					})
					if (cartList) {
						this.allChecked = true
					} else {
						this.allChecked = false
					}
				}
			},
			//全选
			selectgoods(){
				this.allChecked = !this.allChecked
				if (this.allChecked) {
					this.goodsList.map(item => {
						item.checked = 1
					})
				} else {
					this.goodsList.map(item => {
						item.checked = 0
					})
				}
			},
			//增加商品数量
			add(item){
				item.num=item.num+1
			},
			//减少商品数量
			reduce(item){
				if(item.num>1){
					item.num=item.num-1
				}else{
					uni.showModal({
						title:"警告",
						content:"再减就没有啦！确定要删除吗？",
						success: (res) => {
							if(res.confirm){
								uni.request({
									url:this.domain+"/shoppingCart/"+item.cartId,
									method:'DELETE',
									header:{
										token:this.userInfo.token
									},
									success:function(res){
										if(res.data.code==200){
											getCartGoods()
											uni.showToast({
												title:"删除商品成功"
											})
										}
									}
								})
							}else if(res.cancel){
								console.log("有点击了取消")
							}
						}
					})
				}
			},
			//总数量
			totalNum(){
				
			},
		}
	}
</script>

<style lang="scss">
	.nothing{
		height: 180px;
		background-image: url("../../static/nothing/no_comment.png");
		text-align: center;
	}
	.nothingbutton{
		width: 100px;
		margin-top: 100px;
	}
	.goods{
			line-height: 80rpx;
			background-color: #FFFFFF;
			&-detail{
			    display: flex;
			    padding: 30rpx 15rpx 30rpx 30rpx;
			    background-color: #fff;
			    justify-content: space-between;
			    border-bottom: 5rpx solid #F1F1F1;
			    align-items: center;
			    .detail-left{
			        display: flex;
			        .goods-left{
			            display: flex;
			            align-items: center;
			        }
			    }
			    .size{
			        display: flex;
			        justify-content: space-around;
			        flex-direction: column;
			        margin-left: 30rpx;
			        .goods-price{
			            font-size: 25rpx;
			            color: #F44545;
			            
			        }
			    }
			    .detail-right{
			        text{
			            width: 50rpx;
			            line-height: 50rpx;
			            text-align: center;
			            display: inline-block;
			            background-color: #F7F7F7;
			            margin-right: 10rpx;
			        }
			        .add {
			            color: #FA4305;
			            border-radius: 10rpx 30rpx 30rpx 10rpx;
			            margin-right: 20rpx;
			        }
			        .subtract{
			            border-radius: 30rpx 10rpx 10rpx 30rpx;
			        }
			    }
			}
		}
		.empty{
			
			    position: relative;
			    top: 220rpx;
			    text-align: center;
			    display: flex;
			    align-items: center;
			    flex-direction: column;
			    &-text{
			        color: #808080;
			        margin-bottom: 50rpx;
			    }
			    &-button{
			        width: 300rpx;
			        height: 90rpx;
			        color:orange;
			        border: 1rpx solid orange;
			        text-align: center;
			        line-height: 90rpx;
			        border-radius: 48rpx;
			    }		
		}
		.end{
		    width: 100%;
		    height: 90rpx;
		    background-color:#fff;
		    position: fixed;
		    bottom: 100rpx;
		    left: 0;
		    display: flex;
		    align-items: center;
		    &-left{
		        width: 70%;
		        display: flex;
		        justify-content: space-between;
		        padding: 0 30rpx;
		        .end-flex{
		            display: flex;
		            align-items: center;
		        }
		    }
		    &-right{
		        width: 30%;
		        line-height: 90rpx;
		        background-color: #F44545;
		        text-align: center;
		        color: #fff;
		    }
		}
</style>