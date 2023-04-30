<template>
	<view>
<!-- 		<view class="espCam" style="height: 600px; width: 100%; ">
			<p>123</p>
		</view> -->
		<view style="">
			<button type="primary" class="login" @click="toimg()" style="margin-bottom: 2%; width: 100%;">书本扫描</button>
			<!-- <button type="warn" @click="tobug()">樱桃虫害识别</button> -->
		</view>	
		
	</view>
	
</template>

<script>
	export default {
		data() {
			return {
				
			}
		},
		methods: {
			toimg(){
				uni.navigateTo({
				    url: '../img/img'
				});
			},
			tobug(){
				uni.navigateTo({
				    url: '../img/bug/bug'
				});
			}
		}
	}
</script>

<style>

</style>
