<template>
	<view class="content">
		<image class="title" :src="image" ></image>
	<view>
		<text class="title">{{title + 'very good'}}</text>
		<input type="text" :value="title" @input="change">
	</view>
	  <!-- 页面跳转 错误写法：url="{{url}}" -->
	  n<navigator :url="url">
		  <view>
			  I am {{student.age >=18 ? '成年':'未成年'}} years old. <br>
             I have skills such as:{{skill[0]}},{{skill[1]}},{{skill[2]}}
		  </view>
	  </navigator>
	  
	</view>
</template>

<script>
	export default {
		data() {
			return {
				
			}
		},
		methods: {
			
		}
	}
</script>

<style>

</style>
