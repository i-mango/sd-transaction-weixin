<template>
<view class="form">
	<uni-forms :type="formData" ref="form" :rules="rules" label-width="80">
		<uni-forms-item name="name" label="商品名称">
			<uni-easyinput type="text" v-model="formData.name" placeholder="请输入商品名称" />
		</uni-forms-item>
		<uni-forms-item name="title" label="商品标题">
			<uni-easyinput type="text" v-model="formData.title" placeholder="请输入商品标题" />
		</uni-forms-item>
		<uni-forms-item name="subtitle" label="商品副标题">
			<uni-easyinput type="text" v-model="formData.subtitle" placeholder="请输入商品副标题" />
		</uni-forms-item>
		<uni-forms-item name="type" label="商品类型">
			<select-lay :value="tval" name="type" slabel="name" svalue="id" :options="range" @selectitem="selectitem"></select-lay>
		</uni-forms-item>
		<uni-forms-item name="price" label="商品价格">
			<uni-easyinput type="text" v-model="formData.price" placeholder="请输入商品名称" />
		</uni-forms-item>
		<uni-forms-item name="mainimage" label="商品主图片">
			<uni-file-picker limit="1" :del-icon="false" disable-preview :imageStyles="imageStyles" file-mediatype="image" :auto-upload="false" @select="upload"></uni-file-picker>
		</uni-forms-item>
		<uni-forms-item name="subimages" label="商品图片集">
			<uni-file-picker :value="formData.subimages" :auto-upload="false" file-mediatype="image" mode="grid" file-extname="png,jpg" :limit="9" @select="handleSelect" @delete="handleDelete" @success="success"></uni-file-picker>
		</uni-forms-item>
		<uni-forms-item name="detail" label="商品描述">
			<uni-easyinput type="textarea" v-model="formData.detail" placeholder="请输入商品描述" />
		</uni-forms-item>
		<uni-forms-item>
			<button type="default" @click="publishGoods">发布</button>
		</uni-forms-item>
	</uni-forms>
</view>
</template>

<script>
	import selectLay from '../../components/select-lay/select-lay.vue';
	import {mapState} from 'vuex';
	export default{
		components:{
			selectLay
		},
		data(){
			return{
				formData:{
					name:'',
					title:"",
					subtitle:"",
					type:0,
					price:"",
					mainimage:"",
					subimages:[],
					detail:""
				},
				range:[],
				tval:0
			}
		},
		computed:{
			...mapState({
				loginStatus:state=>state.user.loginStatus,
				userInfo:state=>state.user.userInfo
			})
		},
		onShow() {
			this.getType()
		},
		methods:{
			//获取商品类别
			getType(){
				uni.request({
					url:this.domain+"/category",
					method:'GET',
					header:{
						"Content-Type": "application/json;charset=utf-8"
					},
					success: (res) => {
						this.range=res.data.data
					}
				})
			},
			selectitem(index, item) {
			    if (index >= 0) {
					this.tval = item.id;
			    } else {
			        this.tval = ""
			    }
			},
			upload(e){
				if(this.loginStatus=false){
					uni.showToast({
						title:"请先登录",
						icon:"error"
					})
					uni.navigateTo({
						url:"/pages/login/login"
					})
				}else{
					const tempFilePaths = e.tempFilePaths;//e是获取的图片源
				uni.uploadFile({
					url:this.domain+'/upload',
					filePath:tempFilePaths[0],
					name:'file',
					header:{
						token:this.userInfo.token ? this.userInfo.token: ""
					},success: (res) => {
						this.formData.mainimage=JSON.parse(res.data).data
					}
				})
				}
			},
			async handleSelect(res) { // 上传图片
				if(this.loginStatus=false){
					uni.showToast({
						title:"请先登录",
						icon:"error"
					})
					uni.navigateTo({
						url:"/pages/login/login"
					})
				}else{
					await this.uploadImg(res.tempFilePaths);
				}
			},
			async uploadImg(tempFilePaths) {
			    if (!tempFilePaths.length) return;
			    const path = tempFilePaths.pop();
			    this.formData.subimages.push(path)
			    const [err, {data}] = await uni.uploadFile({
			        url: this.domain+"/upload",
			        filePath: path,
			        name: 'file',
			        header: {
			            token:this.userInfo.token 
			        }
			    });
			    if (!this.isGuid(data)) {
			        // upload fail
			        this.formData.subimages.pop()
			        uni.showToast({
			            title: "上传失败",
			            icon: "none"
			        })
			    }else{
			        // upload success
			        this.formData.subimages[this.formData.subimages.length - 1].name = data
			    }
			    this.uploadImg(tempFilePaths,token);
			},

			publishGoods(){
				if(this.loginStatus=false){
					uni.showToast({
						title:"请先登录",
						icon:"error"
					})
					uni.navigateTo({
						url:"/pages/login/login"
					})
				}else{
					uni.showLoading({
						title:"发布中..."
					})
					uni.request({
						url:this.domain+"/commodity",
						method:'POST',
						data:{
							cateId:this.tval,
							name:this.formData.name,
							title:this.formData.title,
							subtitle:this.formData.subtitle,
							price:this.formData.price,
							mainimage:this.formData.mainimage,
							subimages: JSON.stringify(this.formData.subimages),
							detail:this.formData.detail
						},
						header:{
							token:this.userInfo.token
						},
						success: (res) => {
							uni.hideLoading()
							if(res.data.code==200){
								uni.showToast({
									title:"发布成功"
								})
							}else{
								uni.showToast({
									title:"发布失败请检查"
								})
							}
						}
					})
				}
				
			},
			change(e){
				console.log("e",e)
			}
		}
	}
</script>

<style>
	.form{
		width: 96%;
		margin-left: 10px;
		margin-top: 5px;
	}
</style>