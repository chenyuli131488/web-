<template>
	<view class="content">
		<view class="title">{{title}}</view>
		<view class="art-content">
			<rich-text class="richText" :nodes="strings"></rich-text>
		</view>
	</view>
</template>
<script>
	export default {
		data() {
			return {
				title:''
			}
			
		},
		onLoad:function(e){//接收index传递过来的参数
			console.log(e);
		
			// reu
			uni.request({//与api交互
				url: 'https://unidemo.dcloud.net.cn/api/news/36kr/' +e.newsid,//动态参数就是 详情页传回来的参数
				method: 'GET',//接收到数据
				data: {},
				success: res => {//接口调用成功的回调函数
					console.log(res);
					this.title = res.data.title;
					this.strings = res.data.content;
				},
				fail: () => {},
				complete: () => {}
			});
		}
		// methods: {
			
		// }
	}
</script>

<style>
.content{
	padding:10upx 2%;
	 width:96%;
	 flex-wrap: wrap;//进行换行工作
}
.title{
	line-height: 2em;
	font-weight: 700;
	font-size:38upx;
}
.art-content{
	line-height: 2em;
}
</style>
