<template>
	<view class="wrapper">
		<Ahh5Animated :delay="delay" :duration="duration" timing="ease" count="1">
			<view class="name">{{name}}</view>
			<!-- 分段关键：此处的style -->
			<view class="line" :style="{'background-size': size, 'background-image':linears}">
				<view v-for="(item,index) in line" :key="index">
					<!-- 可选 -->
					<view v-if="item.remark==0 && item.length>4" class="button" :style="{'width': item.length+'%'}"
						@click="click(item)">预约</view>
					<!-- 可选，但时间段长度过小，不显示 -->
					<view v-else-if="item.remark==0 && item.length<=4" class="button_hide"
						:style="{'width': item.length+'%'}" @click="click(item)"></view>
					<!-- 不可选 -->
					<view v-else-if="item.remark==1" class="button_hide" :style="{'width': item.length+'%'}"
						@click="click2(item)"></view>
				</view>
			</view>
		</Ahh5Animated>
	</view>
</template>

<script>
	import Ahh5Animated from "@/components/ahh5-animated/ahh5-animated"
	export default {
		name: "sl-periodpicker",
		components: {
			Ahh5Animated: Ahh5Animated,
		},
		props: {
			startTime: {
				type: String,
				default: ''
			},
			endTime: {
				type: String,
				default: ''
			},
			//日程安排/工作计划（即，已有“业务”的时间段）
			schedule: {
				type: Object,
				default () {
					return {}
				}
			},
		},
		created: function(e) {
			// console.time("periodpicker create")
			this.measure()
			// this.layout()
			this.draw()
			// console.timeEnd("periodpicker create")
			this.animate()
		},
		data() {
			return {
				// length:' 5% 100%, 9% 100%, 13% 100%, 32% 100%, 41% 100%',
				// x:'0% 100%,5% 100%,14% 100%,27% 100%,59% 100%,',
				// size:' 5% 100%, 14% 100%, 27% 100%, 59% 100%, 100% 100%',
				// linears:'linear-gradient(#FF7473, #FF7473),linear-gradient(#fffff0, #fffff0),linear-gradient(#FF7473, #FF7473),linear-gradient(#fffff0, #fffff0),linear-gradient(#FF7473, #FF7473)',
				size: '', //宽度比例
				linears: '', //颜色区分
				line: [], //时间条绘制
				sumTime: '', //总时长,
				delay: 0, // 延时秒数
				duration: 1, //执行动画时间

				name: '', //标题
				room: '', //会议室名
				timeData: [] //已有“业务”的时间段
			};
		},
		methods: {
			measure() { //measure\layout\draw
				console.log('=====start====')
				this.name = this.schedule.name 
				this.timeData = this.schedule.schedule
				console.log('已有“业务”的时间段', JSON.stringify(this.timeData))
				
				var sumTime = this.timeDifference(this.startTime, this.endTime)
				console.log(Number(sumTime))
				//空时间段（无“业务”） timeData格式[],
				if (this.isEmptyObject(this.timeData)) {
					const s = {
						"start": this.startTime,
						"end": this.endTime,
						"length": 100,
						"x": 0,
						"remark": 0
					}
					this.line.push(s)
					return
				}
				//去重
				this.unique(this.timeData)
				//排序 按时间顺序从前往后
				this.timeData.sort(function(a, b) {
					return a.start > b.start ? 1 : -1;
				})
				//打标记
				this.timeData.forEach(item => {
					item.length = Number((this.timeDifference(item.start, item.end) / sumTime * 100).toFixed(2))
					item.x = Number((this.timeDifference(this.startTime, item.start) / sumTime * 100).toFixed(2))
					item.remark = 1 //0 可选；1 不可选(已被预约的)
				})

				console.log(JSON.stringify(this.timeData))

				this.timeData.forEach((item, index) => {
					if (index == 0) {
						//第一项未到顶，增加空白项
						if (item.x != 0) {
							const s = {
								"start": this.startTime,
								"end": item.start,
								"length": item.x,
								"x": 0,
								"remark": 0
							}
							this.line.unshift(s)
						}
						this.line.push(item)
						//只有一项
						if (this.timeData.length == 1 && item.x + item.length < 100) {
							const s = {
								"start": item.end,
								"end": this.endTime,
								"length": 100 - (item.x + item.length),
								"x": item.x + item.length,
								"remark": 0
							}
							this.line.push(s)
						}
					} else {
						if (item.x > (this.timeData[index - 1].x + this.timeData[index - 1].length)) {
							const s = {
								"start": this.timeData[index - 1].end,
								"end": item.start,
								"length": item.x - (this.timeData[index - 1].x + this.timeData[index - 1]
									.length),
								"x": this.timeData[index - 1].x + this.timeData[index - 1].length,
								"remark": 0
							}
							this.line.push(s)
						}
						this.line.push(item)
						//最后一项未到底，增加空白项
						if (this.timeData.length - 1 == index && item.x + item.length < 100) {
							const s = {
								"start": item.end,
								"end": this.endTime,
								"length": 100 - (item.x + item.length),
								"x": item.x + item.length,
								"remark": 0
							}
							this.line.push(s)
						}
					}
				})
				console.log('时间条比例', JSON.stringify(this.line))
			},
			layout() { //measure\layout\draw
				// 即将加入新功能，置灰当前时刻之前的可选时间段
			},
			draw() {
				this.line.forEach((item, index) => {
					if (index == 0) {
						this.size += item.length + '% 100%,' //第一项，展示长度
					} else {
						//其它项，展示x+length
						if (item.length == 0) { //该项时间段很短很短，length占比趋近于0
							this.size += item.x + 2 + '% 100%,'
						} else {
							this.size += item.x + item.length + '% 100%,'
						}
					}
					if (item.remark == 0) { //0 可选；1 不可选(已被预约的)
						this.linears += 'linear-gradient(#578BFF, #578BFF),'
					} else if (item.remark == 1) {
						this.linears += 'linear-gradient(#FF7473, #FF7473),'
					}
				})
				this.size = this.size.substring(0, this.size.length - 1) //删除最后的,号
				this.linears = this.linears.substring(0, this.linears.length - 1) //删除最后的,号

				console.log('宽度比例', this.size)
				console.log('颜色区分', this.linears)
				console.log('=====end====')
			},
			//获取时间差(分钟)
			timeDifference(time1, time2) {
				let platform = uni.getSystemInfoSync().platform
				if (platform == 'ios') {
					time1 = time1.replace(/-/g, ':').replace(' ', ':');
					time1 = time1.split(':');
					var date1 = new Date(time1[0], (time1[1] - 1), time1[2], time1[3], time1[4], time1[5]);
					time2 = time2.replace(/-/g, ':').replace(' ', ':');
					time2 = time2.split(':');
					var date2 = new Date(time2[0], (time2[1] - 1), time2[2], time2[3], time2[4], time2[5]);
					var s1 = date1.getTime(),
						s2 = date2.getTime();
					return parseInt((s2 - s1) / (1000 * 24 * 60))
				}
				var date1 = new Date(time1)
				var date2 = new Date(time2)
				var s1 = date1.getTime(),
					s2 = date2.getTime();
				return parseInt((s2 - s1) / (1000 * 24 * 60))
			},
			//判断当前时间是否在指定时间段内
			isDuringPeriod(beginDateStr, endDateStr) {
				var curDate = new Date(),
					beginDate = new Date(beginDateStr),
					endDate = new Date(endDateStr);
				if (curDate >= beginDate && curDate <= endDate) {
					return true;
				}
				return false;
			},
			click(item) {
				console.log(JSON.stringify(item))
				uni.showToast({
					icon: 'none',
					title: this.schedule.name + ' 空闲时间段：' + item.start.substring(11, 16) + ' - ' + item.end.substring(11, 16)
				})
			},
			click2(item) {
				console.log(JSON.stringify(item))
				console.log(JSON.stringify(this.timeData))
				this.timeData.forEach((item2, index) => {
					if (item.start == item2.start) {
						uni.showModal({
							title: this.schedule.name + '当前预约情况',
							content: '时间：' + item.start.substring(0, 16) + ' - ' + item.end.substring(11,
								16) + '\n预约人：' + item2.user,
							showCancel: false,
							buttonText: '确定',
							success: function(res) {
								if (res.confirm) {
									console.log('用户点击确定');
								} else if (res.cancel) {
									console.log('用户点击取消');
								}
							}
						});
					}
				})
			},
			isEmptyObject(obj) {
				for (var key in obj) {
					return false
				};
				return true
			},
			//去重
			unique(arr) {
				arr = arr.reduce((prev, cur) => prev.includes(cur) ? prev : [...prev, cur], []);
			},
			//动画
			animate() {
				this.delay = Math.random() * 3
				this.duration = Math.random() * 2
			},
		},

	}
</script>

<style>
	.wrapper {
		margin: 0 auto;
		width: 100%;
		text-align: center;
	}

	.name {
		margin-bottom: 12px;
		width: 10%;
		height: 50px;
		font-size: 12px;
		font-style: oblique;
		line-height: 50px;
		text-align: center;
		background-repeat: no-repeat;
		float: left;
	}

	.line {
		width: 90%;
		height: 50px;
		background-repeat: no-repeat;
		float: right;
	}

	.button {
		height: 50px;
		font-size: 10px;
		color: #fff;
		float: left;
		/** 垂直居中 **/
		line-height: 50px;
		/** 水平居中 **/
		text-align: center;
	}

	.button_hide {
		height: 50px;
		font-size: 0.01px;
		float: left;
		line-height: 50px;
		text-align: center;
	}

	.vip {
		color: #FF0000;
		font-size: 14rpx;
	}
</style>
