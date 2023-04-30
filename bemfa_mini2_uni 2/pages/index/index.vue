<template>
	<!-- index.wxml -->

	<view style="margin-top: 20px">
		<view style="text-align: center">
			<p style="font-size: 18px">{{dateFormat(date)}}</p>
			<br>
			<view style="margin-top: 30px">
				<p style="font-size: 38px;">设备状态：{{ device_status }}</p>
			</view>

			<!-- <view class="tempHumi" style="margin-top: 30px">
				<p>温度：{{ temp }}℃ 湿度：{{ humi }}%</p>
			</view> -->
			
			
			<view class="exchangeAir">
				<p>升降门：{{ powerstatus }}</p>
			</view>
			<div style="display: flex;">
				<div style="flex: 1; flex-basis: 0;">
					<button @tap="openclick">打开</button>
				</div>
				<div style="flex: 1; flex-basis: 0;">
					<button @tap="closeclick">关闭</button>
				</div>
			</div>
			<view class="temp-humi">
				<view>
					<!-- #ifdef APP-PLUS || H5 -->
					<view @click="echarts.onClick" :prop="option" :change:prop="echarts.updateEcharts" id="echarts"
						class="echarts"></view>
					<!-- #endif -->
					<!-- #ifndef APP-PLUS || H5 -->
					<view>非 APP、H5 环境不支持</view>
					<!-- #endif -->
				</view>
			</view>


			<!-- 			<view class="uni-flex uni-row">
			  <view class="flex-item"><button @tap="openclick">打开</button></view>
			  <view class="flex-item"><button @tap="closeclick">关闭</button></view>
			</view> -->

			

			<!-- 			<view>
					<button type="primary" class="login" @click="tomore()">更多设备</button>
			</view> -->
		</view>
	</view>
</template>

<script>
	//QQ交流群：824273231
	//日期：20200629
	//有问题加群咨询或者联系微信19092550573

	//获取应用实例
	const app = getApp();
	export default {
		data() {
			return {
				uid: '4af7f37866684e9a8c5e4dc826e872b2',
				topic: 'led',
				type: '1',
				device_status: '...',
				temp: '...',
				tempArray: [],
				humi: '...',
				humiArray: [],
				//默认离线
				powerstatus: '已关闭', //默认关闭
				date: new Date().toISOString(), //现在时间

				option: {
					lineStyle: {
						color: 'red' // 折线颜色
					},
					itemStyle: {
						color: 'red' // 折线颜色
					},
					title: {
						text: '借阅人数实时变化'
					},
					tooltip: {},
					// legend: {
					// 	data: ['温度']
					// },
					xAxis: {
						data: ["-30s","-25s", "-20s", "-15s", "-10s", "-5s", "0s"]
					},
					yAxis: {
						max:100
					},
					series: [
						{
						name: '温度',
						type: 'line',
						data: [0, 0, 0, 0, 0, 0, 0],
						lineStyle: {
						      color: 'red' // 设置第一条折线的颜色为红色
						    },
						itemStyle: {
							color: 'red' // 折线颜色
							},
						},
						// {
						// name: '湿度',
						// type: 'line',
						// data: [0, 0, 0, 0, 0, 0, 0],
						// lineStyle: {
						//       color: 'blue' // 设置第二条折线的颜色为蓝色
						//     },
						// itemStyle: {
						// 	color: 'blue' // 折线颜色
						// 	},
						// },
					]
				}
			};
		},
		created() {
			setInterval(async() => {
				// // 模拟API返回的温度值
				// const temp = Math.floor(Math.random() * 50);
				// const humi = Math.floor(Math.random() * 50);
				const temp = uni.getStorageSync('temp');
				const humi = uni.getStorageSync('humi');
				// const temp = this.temp;
				// 将温度值添加到tempArray中
				this.tempArray.push(temp);
				this.humiArray.push(humi);

				if (this.tempArray.includes(temp)) {
					console.log(`${temp} successfully added to tempArray`);
				} else {
					console.log(`${temp} was not added to tempArray`);
				}
				if (this.humiArray.includes(humi)) {
					console.log(`${humi} successfully added to humiArray`);
				} else {
					console.log(`${humi} was not added to humiArray`);
				}

				// 如果数组长度超过5，删除最早的值
				if (this.tempArray.length > 7) {
					this.tempArray.splice(0, 1);
				}
				if (this.humiArray.length > 7) {
					this.humiArray.splice(0, 1);
				}
				console.log(this.tempArray);
				console.log(this.humiArray);
				this.option.series[0].data =this.tempArray;
				this.option.series[1].data =this.humiArray;
			}, 2000);
		},
		onLoad: function() {
			var that = this;
			let _this = this;
			setInterval(function() {
				_this.date = Date.parse(new Date());
			}, 1000);
			//请求设备状态
			//设备断开不会立即显示离线，由于网络情况的复杂性，离线1分钟左右才判断真离线
			uni.request({
				url: 'https://apis.bemfa.com/va/online',
				//状态api接口，详见巴法云接入文档
				data: {
					uid: that.uid,
					topic: that.topic,
					type: that.type
				},
				header: {
					'content-type': 'application/x-www-form-urlencoded'
				},
				success(res) {
					console.log(res.data);
					if (res.data.data == true) {
						that.setData({
							device_status: '在线'
						});
					} else {
						that.setData({
							device_status: '离线'
						});
					}
					console.log(that.device_status);
				}
			});

			//请求询问设备开关/状态
			uni.request({
				url: 'https://apis.bemfa.com/va/getmsg',
				//get接口，详见巴法云接入文档
				data: {
					uid: that.uid,
					topic: that.topic,
					type: that.type
				},
				header: {
					'content-type': 'application/x-www-form-urlencoded'
				},
				success(res) {
					console.log(res.data.data[0].msg.split('#').pop());
					uni.setStorageSync('temp', res.data.data[0].msg.split('#')[1]);
					uni.setStorageSync('humi', res.data.data[0].msg.split('#')[2]);
					if (res.data.data[0].msg.split('#').pop() === 'on') {
						that.setData({
							powerstatus: '已打开',
							temp: res.data.data[0].msg.split('#')[1],
							humi: res.data.data[0].msg.split('#')[2]
						});

					} else {
						that.setData({
							powerstatus: '已关闭',
						});
					}
					console.log(that.powerstatus);
				}
			});

			//设置定时器，每五秒请求一下设备状态
			setInterval(function() {
				console.log('定时请求设备状态,默认五秒');
				uni.request({
					url: 'https://apis.bemfa.com/va/online',
					//get 设备状态接口，详见巴法云接入文档
					data: {
						uid: that.uid,
						topic: that.topic,
						type: that.type
					},
					header: {
						'content-type': 'application/x-www-form-urlencoded'
					},
					success(res) {
						console.log(res.data);
						if (res.data.data == true) {
							that.setData({
								device_status: '在线'
							});
						} else {
							that.setData({
								device_status: '离线'
							});
						}
						console.log(that.device_status);
					}
				});

				//请求询问设备开关/状态
				uni.request({
					url: 'https://apis.bemfa.com/va/getmsg',
					//get接口，详见巴法云接入文档
					data: {
						uid: that.uid,
						topic: that.topic,
						type: that.type
					},
					header: {
						'content-type': 'application/x-www-form-urlencoded'
					},
					success(res) {
						console.log(res.data.data[0].msg.split('#').pop());
						uni.setStorageSync('temp', res.data.data[0].msg.split('#')[1]);
						uni.setStorageSync('humi', res.data.data[0].msg.split('#')[2]);
						if (res.data.data[0].msg.split('#').pop() === 'on') {
							that.setData({
								powerstatus: '已打开',
								temp: res.data.data[0].msg.split('#')[1],
								humi: res.data.data[0].msg.split('#')[2],
							});
						}
						console.log(that.powerstatus);
						console.log(res.data.data[0].msg.split('#')[1]);
						console.log(res.data.data[0].msg.split('#')[2]);
						console.log(tempArray);
					}
				});
			}, 5000);
		},
		methods: {
			//echarts
			// changeOption() {
			// 	const data = this.option.series[0].data
			// 	// 随机更新示例数据
			// 	data.forEach((item, index) => {
			// 		data.splice(index, 1, Math.random() * 40)
			// 	})
			// },

			tomore() {
				uni.navigateTo({
					url: '../more/more'
				});
			},
			//”打开“按钮处理函数函数
			openclick: function() {
				//当点击打开按钮，更新开关状态为打开
				var that = this;
				that.setData({
					powerstatus: '已打开'
				});

				//控制接口
				uni.request({
					url: 'https://apis.bemfa.com/va/postmsg',
					//api接口，详见接入文档
					method: 'POST',
					data: {
						//请求字段，详见巴法云接入文档，http接口
						uid: that.uid,
						topic: that.topic,
						type: that.type,
						msg: 'on' //发送消息为on的消息
					},

					header: {
						'content-type': 'application/x-www-form-urlencoded'
					},
					success(res) {
						console.log(res.data);
						uni.showToast({
							title: '打开成功',
							icon: 'success',
							duration: 1000
						});
					}
				});
			},

			//”关闭“按钮处理函数函数
			closeclick: function() {
				//当点击关闭按钮，更新开关状态为关闭
				var that = this;
				that.setData({
					powerstatus: '已关闭'
				});

				//控制接口
				uni.request({
					url: 'https://apis.bemfa.com/va/postmsg',
					//api接口，详见接入文档
					method: 'POST',
					data: {
						uid: that.uid,
						topic: that.topic,
						type: that.type,
						msg: 'off'
					},
					header: {
						'content-type': 'application/x-www-form-urlencoded'
					},
					success(res) {
						console.log(res.data);
						uni.showToast({
							title: '关闭成功',
							icon: 'success',
							duration: 1000
						});
					}
				});
			},
			dateFormat(time) {
				let date = new Date(time);
				let year = date.getFullYear();
				// 在日期格式中，月份是从0开始的，因此要加0，使用三元表达式在小于10的前面加0，以达到格式统一  如 09:11:05
				let month = date.getMonth() + 1 < 10 ? "0" + (date.getMonth() + 1) : date.getMonth() + 1;
				let day = date.getDate() < 10 ? "0" + date.getDate() : date.getDate();
				let hours = date.getHours() < 10 ? "0" + date.getHours() : date.getHours();
				let minutes = date.getMinutes() < 10 ? "0" + date.getMinutes() : date.getMinutes();
				let seconds = date.getSeconds() < 10 ? "0" + date.getSeconds() : date.getSeconds();
				// 拼接
				return year + "年" + month + "月" + day + "日" + hours + ":" + minutes + ":" + seconds;
				// return year + "-" + month + "-" + day;
			}
		}
	};
</script>

//Echarts
<script module="echarts" lang="renderjs">
	let myChart
	export default {
		mounted() {
			if (typeof window.echarts === 'function') {
				this.initEcharts()
			} else {
				// 动态引入较大类库避免影响页面展示
				const script = document.createElement('script')
				// view 层的页面运行在 www 根目录，其相对路径相对于 www 计算
				script.src = 'static/echarts.js'
				script.onload = this.initEcharts.bind(this)
				document.head.appendChild(script)
			}
		},
		methods: {
			initEcharts() {
				myChart = echarts.init(document.getElementById('echarts'))
				// 观测更新的数据在 view 层可以直接访问到
				myChart.setOption(this.option)
			},
			updateEcharts(newValue, oldValue, ownerInstance, instance) {
				// 监听 service 层数据变更
				myChart.setOption(newValue)
			},
			onClick(event, ownerInstance) {
				// 调用 service 层的方法
				ownerInstance.callMethod('onViewClick', {
					test: 'test'
				})
			}
		}
	}
</script>

<style>
	@import './index.css';
</style>
<style>
	button {
		width: 60%;
		margin-top: 40rpx;
	}

	.tempHumi {
		font-size: 27px;
	}

	.exchangeAir {
		font-size: 37px;
		margin-top: 30px;

	}

	.content {
		display: flex;
		flex-direction: column;
		align-items: center;
		justify-content: center;
	}

	.echarts {
		margin-top: 30px;
		width: 100%;
		height: 300px;
	}
</style>
