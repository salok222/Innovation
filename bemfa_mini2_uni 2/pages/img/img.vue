<template>
	<view>
		<view class="radio-list">
			<view style="color: #DD524D;"> 请选择类别：默认为通用扫描</view>
<!-- 			<radio-group class="radioG" @change="radioChange">
				<label class="radioR" v-for="(item, index) in radioItems" :key="item.value">
					<view>
						<radio :value="item.value" :checked="item.checked" />
					</view>
					<view> {{item.name}} </view>
				</label>
			</radio-group> -->
			
		</view>
		
		<view class="line"></view>

		<view class="content">
			<!-- 选择图片区域 -->
			<view class="images_box">
				<image class='img' :src='imgTempUrl' mode='aspectFill'></image>
			</view>
			<!-- 按钮区域 -->
			<button class="btn" type="primary" @click="imgAdd">选择图片</button>
			<button class="btn" type="primary" @click="getToken">上传识别</button>
			<button class="btn" type="primary" @click="getResult">获取结果</button>
			<!-- 识别结果 -->
			<view class="txt" v-for="(item, index) in resItems" :key="item.value">
				<text>有 {{ (item.score * 100).toFixed(1) }}% 概率是{{item.keyword}}{{item.name}} </text>
			</view>
		</view>
	</view>
</template>

<script>
	//定义本页用到的变量
	import {
		pathToBase64,
		base64ToPath
	} from 'image-tools'
	var base64Img = ''
	var id = 'eXAZRzuo7xUvp7RWEp0iazRK'
	var secret = 'htZFOc5V9rzR4cXFqtysj3T1FnLUMwOL'
	var typeUrl = 'https://aip.baidubce.com/rest/2.0/image-classify/v2/advanced_general?access_token=' //默认是动物识别


	export default {
		data() {
			return {
				type: 0,
				imgTempUrl: '',
				resItems: [],
				radioItems: [{
						value: 0,
						name: '动物识别',
						checked: false
					},
					{
						value: 1,
						name: '植物识别',
						checked: false
					}
				],

			}
		},

		// 分享给朋友
		onShareAppMessage: function() {},
		//分享到朋友圈
		onShareTimeline: function() {},

		methods: {
			radioChange(e) {
				//console.log(e.detail.value)
				this.type = e.detail.value
				if (this.type == 0) { //动物识别
					typeUrl = 'https://aip.baidubce.com/rest/2.0/image-classify/v1/animal?access_token='
				}
				if (this.type == 1) { //植物识别
					typeUrl = 'https://aip.baidubce.com/rest/2.0/image-classify/v1/plant?access_token='
				}
			},

			//选择图片


			imgAdd() {
				// wx.chooseImage({
				// 	count: 1,
				// 	sizeType: ['original', 'compressed'],
				// 	sourceType: ['album', 'camera'],
				// 	success: (res) => {
				// 		this.imgTempUrl = res.tempFilePaths[0]
				// 		this.getB64ByUrl(this.imgTempUrl)  //调用方法并传入参数
				// 	}
				// })


				uni.chooseImage({
					count: 1, //默认9
					sizeType: ['original', 'compressed'], //可以指定是原图还是压缩图，默认二者都有。
					sourceType: ['album', 'camera'], //从相册选择。

					success: (res) => {
						console.log("res-----------------------------------------------", res);

						// 之前的方法  小程可用 APP不可用
						// let base64Img = uni.getFileSystemManager().readFileSync(res.tempFilePaths[0], "base64"); //转码 

						// 都可以用
						this.imgTempUrl = res.tempFilePaths[0]
						pathToBase64(res.tempFilePaths[0])
							.then(path => {
								uni.setStorageSync('base64I', path)
							})
							.catch(error => {
								console.error(error)
							})


						// const arrayBuffer = new Uint8Array(res.tempFilePaths[0]); //先将本地图片路径转换成array类型 
						// const base64Img = uni.arrayBufferToBase64(arrayBuffer); //再转换成base64类型
						// uni.setStorageSync('base64I', base64Img)
						// console.log(base64Img) //成品就在这里了

					},
					fail(err) {

					}
				});


			},


			plantTap() {
				let that = this
				this.getToken(function(token) { //先调用getToken方法得到token,再传入getResult方法中运行
					// getResult()
				});
			},

			getToken: function(callback) { //回调函数，要有返回值
				uni.showLoading({
					title: '加载中'
				});
				uni.request({
					method: 'POST',
					url: 'https://aip.baidubce.com/oauth/2.0/token?client_id=eXAZRzuo7xUvp7RWEp0iazRK&client_secret=htZFOc5V9rzR4cXFqtysj3T1FnLUMwOL&grant_type=client_credentials',
					data: {},

					header: {
						'Content-Type': 'application/json',
						'Accept': 'application/json', // 默认值
					},

					success(res) {
						//console.log(res)
						uni.setStorageSync('token', res.data.access_token)
						// var token = res.data.access_token
						// return callback(token) //返回值
						uni.hideLoading();
					},
					fail(err) {
						console.log('错误', err)
					}
				});
			},
			getResult: function() {
				let that = this
				uni.showLoading({
					title: '结果获取中'
				});
				uni.request({
					url: typeUrl + uni.getStorageSync('token'),
					method: "post",
					data: {
						image: uni.getStorageSync('base64I')
					},
					header: {
						'content-type': 'application/x-www-form-urlencoded' // 默认值
					},
					success(res) {
						console.log(res.data)
						that.resItems = res.data.result
						uni.hideLoading();
					}
				})
			},


		}
	}
</script>

<style>
	.radio-list {
		margin-left: 3.3%;
	}

	.radioG {
		display: flex;
		flex-direction: row;
		flex-wrap: wrap;
		justify-content: flex-start;
	}

	.radioR {
		display: flex;
		flex-direction: row;
		margin: 5rpx;
	}

	.line {
		border-top: 1rpx;
		border-top-style: solid;
		border-color: #007AFF;
	}

	.content {
		display: flex;
		flex-direction: column;
		align-items: center;
		justify-content: center;
	}

	.btn {
		margin: 10px;
		width: 80%;
	}


	.images_box {
		border: 1rpx;
		border-style: solid;
		border-color: rgba(0, 0, 0, 0.452);
		width: 98%;
		height: 496rpx;
		margin: 10rpx;
		position: relative;
	}

	.img {
		width: 100%;
		height: 100%;
	}

	.txt {
		margin: 10px;
		font-size: 20px;
		color: #007AFF;
	}
</style>