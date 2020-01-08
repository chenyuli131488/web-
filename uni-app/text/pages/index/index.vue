 <!-----<template><script><style>-这三个页面只能出现一个--->
<template>
	<view class="content">
		<!-- 新闻列表 -->
		<!--列表快捷键 uListMedia-->
		<view class="uni-list">
			<!-- 第一种方法 -->
			<!-- <navigator></navigator> -->
			<!-- 第二种方法 利用点击做法-->
		    <!-- v-for 进行新闻列表的for循环-->
		<!-- <navigator url="../infor/infor"> -->
			 <view class="uni-list-cell" hover-class="uni-list-cell-hover" v-for="(item,index) in news" :key="index"
			@tap="openinfor"   :data-newsid="item.post_id"><!--传递参数-->
			 	<view class="uni-media-list">
			 		<image class="uni-media-list-logo" :src="item.author_avatar"></image>
			 		<view class="uni-media-list-body">
			 			<view class="uni-media-list-text-top">{{item.title}}</view>
			 			<view class="uni-media-list-text-bottom uni-ellipsis">{{item.created_at}}</view>
			 		</view>
			 	</view>
			 </view>
		<!-- </navigator>	 -->
		</view>
	</view>
	
</template>

<script>
	//VM 协调者调度器
	export default {
		//Model:所有的数据
		data() {
			return {
			    news:[]
			}
		},
		// 生命周期
		onLoad:function() {
			uni.showLoading({
				title:"加载中..."
			})
            uni.request({//发送页面请求
            	url: 'https://unidemo.dcloud.net.cn/api/news',//后台端口
            	method: 'GET',//接收后台传来的数据
            	data: {},
            	success: res => {
					console.log(res);
                    // 当前数组放到当前页面
					this.news = res.data;//把数据放在页面上
					uni.hideLoading();
				},
            	fail: () => {},
            	complete: () => {}
            });  
		},
		methods: {
           openinfor(e){
		   console.log(e);
		  //    var newsid = e.currentTarget.dataset.newsid;
			 // console.log(newsid);
			 //   //unak
			 // uni.navigateTo({
			 // 	url: '../infor/infor?newsid =' +newsid //这是跳转到infor页面上
			 // });
		   }
		}
	}
</script>
<style>
.uni-media-list-logo{
	width:20%;
	hight:100upx;
}
.uni-media-list-body{
	height:auto;//自动高度
	border:1px solid blue;
}
.uni-media-list-text-top{
	line-height:1.6em;
}
</style>
