<template>
	<view class="content_ui">
		<!-- 日历 -->
		<sl-calendar :rightnow="true" @change="chooseDay" :selected="selected"></sl-calendar>

		<view class="content_wrapper">
			<!-- 时间栏 -->
			<view style="font-size: 12px;margin-top: 16rpx;">
				<text style="float: left;margin-left: 10%;">{{startTime.substring(12,16)}}</text>
				<text style="float: center;">12:00</text>
				<text style="float: right;">{{endTime.substring(11,16)}}</text>
			</view>

			<!-- 会议室list -->
			<view v-for="(item,index) in rooms" :key="index" class="list">
				<!-- 分时段选择器 -->
				<sl-periodpicker :schedule="item" :startTime="startTime" :endTime="endTime" :key="menuKey" />
			</view>
		</view>
	</view>
</template>

<script>
	import {
		getNextDay,
		timeFormat,
		getRecentDay
	} from "@/components/sl-calendar/util.js"
	export default {
		data() {
			return {
				currentDate: "",
				startTime: '08:00:00',
				endTime: '20:00:00',
				rooms: [{ //已有会议
						"name": "600室",
						"room_id": "0",
						"schedule": [{
							"start": "08:50:00",	//此处仅作示例需要，真实数据为"yyyy-MM-dd HH:mm:ss"
							"end": "10:30:00",
							"user": "招商引资部（国际合作部）-某某某"
						}]
					},
					{
						"name": "601室",
						"room_id": "1",
						"schedule": [{
							"start": "09:00:00",
							"end": "10:00:00",
							"user": "战略投资策划部-某某某"
						}]
					},
					{
						"name": "602室",
						"room_id": "2",
						"schedule": [{
								"start": "14:00:00",
								"end": "16:30:00",
								"user": "工程管理部-某某某"
							},
							{
								"start": "09:00:00",
								"end": "12:00:00",
								"user": "产品技术部-松林"
							}
						]
					},
					{
						"name": "603室",
						"room_id": "3",
						"schedule": [{
							"start": "14:30:00",
							"end": "17:00:00",
							"user": "综合办公室-某某某"
						}]
					},
					{
						"name": "700室",
						"room_id": "4",
						"schedule": []
					},
					{
						"name": "705室",
						"room_id": "5",
						"schedule": []
					},
					{
						"name": "723室",
						"room_id": "6",
						"schedule": []
					},
					{
						"name": "800室",
						"room_id": "7",
						"schedule": [{
							"start": "14:00:00",
							"end": "15:59:59",
							"user": "计划财务部-某某某"
						}]
					},
					{
						"name": "801室",
						"room_id": "8",
						"schedule": [{
							"start": "08:45:00",
							"end": "11:00:00",
							"user": "法务风控部-某某某"
						}]
					},
					{
						"name": "802室",
						"room_id": "9",
						"schedule": []
					}
				],
				selected: [], //红点
				menuKey: 1, //当key 值变更时，会自动的重新渲染，原理（https://cn.vuejs.org/v2/guide/list.html#%E7%BB%B4%E6%8A%A4%E7%8A%B6%E6%80%81）
			}
		},
		onShow() {
			//今日
			this.currentDate = timeFormat(new Date(), "yyyy-MM-dd ") //注意此处，"yyyy-MM-dd "日期后面留一空格字符 
			this.startTime = this.currentDate + '08:00:00'
			this.endTime = this.currentDate + '20:00:00'

			//假数据
			this.rooms.forEach(i => {
				if (i.schedule.length != 0) {
					i.schedule.forEach(j => {
						j.start = this.currentDate + j.start
						j.end = this.currentDate + j.end
					})
				}
			})
			console.log('假数据（已有业务的时间段）', JSON.stringify(this.rooms))

			// this.forceDraw()
		},
		methods: {
			//日历选择日期
			chooseDay(e) {
				console.log('日历选择:', e)
				this.currentDate = timeFormat(new Date(e.replace(/-/g, "/")), "yyyy-MM-dd");
				this.startTime = this.currentDate + ' 08:00:00',
					this.endTime = this.currentDate + ' 20:00:00',
					console.log('业务起始时间（每日）', this.startTime)
				console.log('业务终止时间（每日）', this.endTime)

				//请求该日已有业务的时间段数据

			},
			//强制Vue重新渲染组件
			forceDraw() {
				++this.menuKey
			},
		}
	}
</script>

<style lang="scss" scoped>
	.content {
		display: flex;
		justify-content: center;
		align-items: center;
		height: 150px;
		text-align: center;
	}

	.content-text {
		font-size: 18px;
		color: $uni-text-color;
	}

	.content_ui {
		display: flex;
		flex-direction: column;
		align-items: left;
		justify-content: center;
		background-color: #fff;
	}

	.content_wrapper {
		margin: 0 auto;
		width: 100%;
		text-align: center;
	}

	.list {
		display: flex;
		flex-direction: column;
	}
</style>
