<template>
	<view class="home">
		<scroll-view class="navScroll" scroll-x>
			<view class="item" 
			:class="index==navIndex ? 'active' : ''" 
			v-for="(item,index) in navArr" :key="item.id" 
			@click="clickNav(index,item.id)">{{item.classname}}</view>
		</scroll-view>
		<view class="content">
			<view class="row" v-for="item in newsArr" :key="item.id">
				<newsbox @click.native="goDetail(item)" :item="item"></newsbox>
			</view>
		</view>
		<view class="nodata" v-if="!newsArr.length">
			<image src="../../static/images/nodata.png" mode=""></image>
		</view>
		<view class="loading" v-if="newsArr.length">
			<view v-if="loading==1">加载数据中...</view>
			<view v-if="loading==2">没有更多了</view>
		</view>
	</view>
</template>

<script>
	export default {
		data() {
			return {
				navIndex:0,
				navArr:[],
				newsArr:[],
				currentPage:1,
				loading:0 //0是默认，1是加载中，2是没有更多了
			}
		},
		onLoad() {
			this.getNavData();
			this.getNewsData();
		},
		onReachBottom(){
			if(this.loading==2){
				return;
			}
			//console.log("到底部了");
			this.currentPage++;
			this.loading=1;
			this.getNewsData();
		},
		methods: {
			//点击导航切换
			clickNav(index,id){
				this.navIndex=index;
				//console.log(id);
				this.currentPage=1;
				this.loading=0;
				this.newsArr=[];//将原有的数组清空，此时只传过来新数组的内容
				this.getNewsData(id);
			},
			//跳转到详情页
			goDetail(item){
				//console.log(item)
				uni.navigateTo({
					url:`/pages/detail/detail?cid=${item.classid}&id=${item.id}`
				})
			},
			//获取导航列表数据
			getNavData(){
				uni.request({
					url:"https://ku.qingnian8.com/dataApi/news/navlist.php",
					success: res => {
						//console.log(res)
						this.navArr = res.data
					}
				})
			},
			//获取新闻列表数据
			getNewsData(id=50){
				uni.request({
					url:"https://ku.qingnian8.com/dataApi/news/newslist.php",
					data:{
						cid:id,
						page:this.currentPage
					},
					success: res => {
						//console.log(res)
						if(res.data.length==0){
							this.loading=2
						}
						//使用ES6的扩展运算符，将老数组和新数组的内容拼在一起
						this.newsArr = [...this.newsArr,...res.data]
					}
				})
			}
		}
	}
</script>

<style lang="scss" scoped>
	.navScroll{
		height: 100rpx;
		background: #F7F8FA;
		position:fixed;
		top:var(--window-top);
		left:0;
		z-index: 10;
		white-space: nowrap; //让横向滚动条能够滚动，显示被遮挡的内容。
		//穿透修改scroll-view内置组件，取消H5小程序中横向滚动条下方的短杠。注意微信小程序没有短杠
		/deep/ ::-webkit-scrollbar {
				width: 4px !important;
				height: 1px !important;
				overflow: auto !important;
				background: transparent !important;
				-webkit-appearance: auto !important;
				display: block;
		}
		.item{
			font-size: 40rpx;
			display: inline-block;
			padding: 0 30rpx;
			line-height: 100rpx; //设置行高，使文字竖直居中
			color: #333;
			&.active{
				color: #31C27C;
			}
		}
	}
	.content{
		padding: 30rpx;
		padding-top: 130rpx;
		.row{
			border-bottom: 1px dotted #efefef;
			padding: 20rpx 0;
		}
	}
	.nodata{
		display: flex;
		justify-content: center;
		image{
			width: 360rpx;
		}
	}
	.loading{
		text-align: center;
		font-size: 26rpx;
		color:#888;
		line-height: 2em;
	}
</style>