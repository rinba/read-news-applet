<template>
	<view class="detail">
		<view class="title">{{detail.title}}</view>
		<view class="info">
			<view class="author">编辑：{{detail.author}}</view>
			<view class="time">发布日期：{{detail.posttime}}</view>
		</view>
		<view class="content">
			<!--富文本有单独的写法-->
			<rich-text :nodes="detail.content"></rich-text>
		</view>
		<view class="description">
			声明：本站的内容均采集于腾讯新闻，如果侵权请联系管理(3255620496@qq. com)进行整改删除，本站进行了内容采集不代表本站及作者观点，若有
侵犯请及时联系管理员，谢谢您的支持。
		</view>
	</view>
</template>

<script>
	import {parseTime} from "@/utils/time.js"
	export default {
		data() {
			return {
				options:null,
				detail:{}
			};
		},
		onLoad(e) { //使用onLoad页面生命周期钩子将路由参数转化为接口参数
			//console.log(e);
			this.options=e;
			this.getDetail();
		},
		methods:{
			getDetail(){
				uni.request({
					url:"https://ku.qingnian8.com/dataApi/news/detail.php",
					data:this.options, //由于options拿取整个接口参数（一个对象），所以此处直接给，不写大括号
					success:res=>{
						//console.log(res)
						//解析时间戳
						res.data.posttime=parseTime(res.data.posttime);
						//处理图片无法正常在微信小程序中显示
						res.data.content=res.data.content.replace(/<img>/gi,'<img style="max-width:100%"')
						this.detail=res.data;
						//设置详情页标题
						uni.setNavigationBarTitle({
							title:this.detail.title
						});
						this.saveHistory();
					}
				})
			},
			saveHistory(){
				let historyArr=uni.getStorageSync("history") || []
				let item={
					id:this.detail.id,
					classid:this.detail.classid,
					picurl:this.detail.picurl,
					title:this.detail.title,
					looktime:parseTime(Date.now())
				}
				//浏览历史去重
				let index = historyArr.findIndex(i=>{
					return i.id == this.detail.id
				})
				//console.log(index) 某一条新闻没看过返回-1,看过的直接显示在缓存中的位置
				if(index>=0){
					historyArr.splice(index,1) //删除
				}
				
				historyArr.unshift(item)
				historyArr=historyArr.slice(0,10) //截取10条，数组的非变更方法
				uni.setStorageSync("history",historyArr)
			},
		}
	}
</script>

<style lang="scss">
	.detail{
		padding: 30rpx;
		.title{
			font-size: 46rpx;
			color: #333;
		}
		.info{
			background: #F6F6F6;
			padding: 20rpx;
			font-size: 25rpx;
			color: #666;
			display: flex;
			justify-content: space-between;
			margin: 40rpx 0;
		}
		.content{
			padding-bottom: 50rpx;
			/deep/ img{
				max-width: 100%;
			}
		}
		.description{
			background: #FEF0F0;
			font-size: 26rpx;
			padding: 20rpx;	
			color: #F89898;
			line-height: 1.8em;
		}
	}
</style>
