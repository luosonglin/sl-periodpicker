<template>
	<view class="calendar">
		<view class="wrapper">
			<view class="month">
				{{curMonth}}月
			</view>
			<view class="left"></view>
			<view class="daylist">
				<view v-for="(item,index) in list" @click="chooseDay(index)" class="daylist-item"
					:class="[index<todayIndex?'disable':'']">
					<view class="daylist-day" :class="[index==active?'active':'']">
						{{item| filterDay}}
					</view>
					<!-- 后续再更 -->
					<!-- <text class="circle" v-if="selected[index]"></text> -->
					<view class="daylist-week">
						{{item| filterWeek}}
					</view>
				</view>
			</view>
			<view class="right"></view>
		</view>
	</view>
</template>

<script>
	import {
		getNextDay,
		getBeforeDay,
		getCurMonthDayNum,
		getElseMonthDayNum,
		isSameDay,
		timeFormat,
		getRecentDay
	} from "./util.js"
	export default {
		name: "sl-calendar",
		props: {
			/**
			 * 是否立即触发change
			 */
			rightnow: {
				type: Boolean,
				default: true
			},
			// 时间范围
			day: {
				type: Number,
				default: 7 * 4 //4周
			},
			//红点
			selected: {
				type: Array,
				default () {
					return []
				}
			}

		},
		filters: {
			filterWeek: function(time) {
				let t = new Date(time)
				let week = t.getDay()
				let cur = ['周日', '周一', '周二', '周三', '周四', '周五', '周六'][week]
				return cur ? cur : ''
			},
			filterYear: function(time) {
				let t = new Date(time)
				let year = t.getFullYear()()
				return year ? year : ''
			},
			filterDay: function(time) {
				let t = new Date(time)
				let day = t.getDate()
				return day ? day : ''
			},
		},
		computed: {
			curYear() {
				if (this.list[this.active]) {
					let date = new Date(this.list[this.active])
					let year = date.getFullYear()
					return year
				}
				return ''
			},
			curMonth() {
				if (this.list[this.active]) {
					let date = new Date(this.list[this.active])
					let month = date.getMonth() + 1
					return month
				}
				return ''
			}
		},
		data() {
			return {
				active: -1,
				todayIndex: -1,
				list: [],
			};
		},
		watch: {
			day: {
				handler(newValue, oldValue) {
					// console.time("create")
					const day = getRecentDay(this.day);
					// console.timeEnd("create")
					this.active = day.index;
					this.todayIndex = day.index;
					this.list = day.list;
					if (this.rightnow) {
						this.$emit("change", this.list[this.active])
					}
					
					// for (let item of this.list) {
					// 	let temp = this.selected.findIndex(c => c == item.oa_meeting__begin_time.substring(0,4))
					// 	if (temp > -1) {
					// 		item.selected = true;
					// 	} else {
					// 		item.selected = false;
					// 	}
					// 	console.log(item)
					// }
				},
				immediate: true
			}
		},
		methods: {
			chooseDay(index) {
				if (index < this.todayIndex) {
					uni.showToast({
						title: '只能预约未来时间',
						icon: 'none'
					})
					return;
				}
				if (this.active == index) {
					return;
				}
				this.active = index
				this.$emit("change", this.list[this.active])
				
			}
		}
	}
</script>

<style lang="scss" scoped>
	.calendar {

		.wrapper {
			height: 120rpx;
			overflow: hidden;
			box-shadow: 0px 5px 5px 0px rgba(87, 139, 255, 0.1);
			display: flex;
			align-items: center;

			.calendar-year {
				font-size: 40rpx;
				font-weight: 500;
				color: #232323;
			}

			.month {
				height: 80rpx;
				width: 120rpx;
				text-align: center;
				line-height: 80rpx;
				margin: 20rpx 0;
				font-size: 40rpx;
				font-family: PingFang SC;
				font-weight: 500;
				color: #000;
			}

			.daylist {
				overflow: auto;
				flex: 1;
				// width: 0;	//华为平板 显示不出
				white-space: nowrap; //横排
				height: 100%;

				.daylist-item {
					height: 100%;
					display: inline-flex; //横排
					flex-direction: column;
					justify-content: center;
					align-items: center;
					padding: 0 22rpx;

					&.disable {
						.daylist-week {
							color: #000;
						}

						.daylist-day {
							color: #000;
						}

						//未选中
						.circle {
							margin-top: -2px;
							width: 4px;
							height: 4px;
							border-radius: 5px;
							background-color: $uni-color-error;
						}
					}

					.daylist-week {
						font-size: 22rpx;
						font-weight: 400;
						color: #000;
					}

					.daylist-day {
						margin-top: 10rpx;
						font-size: 28rpx;
						font-weight: 400;
						color: #000;
						width: 40rpx;
						height: 40rpx;
						text-align: center;
						padding: 2rpx;
						border-radius: 50%;

						&.active {
							//选中
							background: #578BFF;
							color: white;
						}
					}

					//选中红点
					.circle {
						margin-top: -5px;
						width: 4px;
						height: 4px;
						border-radius: 5px;
						background-color: $uni-color-error;
					}
				}
			}

			.left {
				height: 0;
				width: 0;
				margin-right: 2px;
				border: 4px;
				border-style: dashed dashed dashed solid;
				border-top-color: transparent;
				border-left-color: transparent;
				border-bottom-color: transparent;
				color: grey;
			}

			.right {
				height: 0;
				width: 0;
				margin-left: 2px;
				border: 4px;
				border-style: dashed dashed dashed solid;
				border-top-color: transparent;
				border-right-color: transparent;
				border-bottom-color: transparent;
				color: grey;
			}
		}
	}
</style>
