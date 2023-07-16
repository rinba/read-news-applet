<template>
	<view class="newsbox">
		<view class="pic">
			<image :src="item.picurl" mode="aspectFill"></image>
		</view>
		<view class="text">
			<view class="title">{{item.title}}</view>
			<view class="info" v-if="!item.looktime">
				<text>{{item.author}}</text>
				<text>{{item.hits}}次浏览</text>
			</view>
			<view class="info" v-else>
				<text>浏览时间：{{item.looktime}}</text>
			</view>
		</view>
	</view>
</template>

<script>
	export default {
		name:"newsbox",
		props:{
			item:{
				type:Object,
				default(){
					return{
						title:"组件",
						picurl:"../../static/images/nopic.jpg",
						author:"张三",
						hits:668,
						looktime:"2023-07-11 12:54:55"
					}
				}
			}
		},
		data() {
			return {
				
			};
		}
	}
</script>

<style lang="scss" scoped>
	.newsbox{
		display: flex;
		.pic{
			width: 230rpx;
			height: 160rpx;
			image{
				height: 100%;
				width: 100%;
			}
		}
		.text{
			flex: 1;
			padding-left: 20rpx;
			display: flex;
			flex-direction: column;
			justify-content: space-between;
			.title{
				font-size: 38rpx;
				color: #333;
				//CSS设置文本超出显示省略号（两行文本）
				text-overflow: -o-ellipsis-lastline;
				overflow: hidden;				//溢出内容隐藏
				text-overflow: ellipsis;		//文本溢出部分用省略号表示
				display: -webkit-box;			//特别显示模式
				-webkit-line-clamp: 2;			//行数
				line-clamp: 2;					
				-webkit-box-orient: vertical;	//盒子中内容竖直排列
			}
			.info{
				font-size: 26rpx;
				color: #999;
				text{
					padding-right: 30rpx;
				}
			}
		}
	}
</style>