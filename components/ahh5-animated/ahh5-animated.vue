<template>
	<view class="components animated" :class="tmpName" :style="style" v-show="show">
		<slot></slot>
	</view>
</template> 
<script>
	export default {
		props: {
			// 延时秒数
			delay: {
				type: Number,
				default: 0
			},
			// 动画名称
			name: {
				type: String,
				default: 'lightSpeedIn'//'bounceInLeft'
			},
			// 执行动画时间
			duration: {
				type: Number,
				default: 1
			},
			// 播放方法 linear ease ease-in ease-out ease-in-out cubic-bezier(n,n,n,n)
			timing: {
				type: String,
				default: 'ease'
			},
			// animation-play-state: paused|running;
			playState: {
				type: String,
				default: 'running'
			},
			// count 执行次数 infinite
			count: {
				type: String,
				default: "1"
			},
			// 是否展示
			isShow: {
				type: Boolean,
				default: true
			}
		},
		watch: {
			isShow: function(val) {
				if (val) {
					this.startAnimation()
				} else {
					this.show = false
				}
			}
		},
		data() {
			return {
				show: false,
				style: '',
				tmpName:""
			};
		},
		created() {
			if (this.isShow) {
				this.startAnimation()
			}
			if(this.show==false&&this.name.indexOf('Out')>-1){
				this.show=true
			}
		},
		methods: {
			createStyle() {
				this.style =
					`
					animation-duration:${this.duration}s;
					animation-timing-function:${this.timing};
					animation-iteration-count:${this.count};
				`
				//animation-play-state:${this.playState};
			},
			startAnimation() {
				setTimeout(() => {
					this.createStyle()
					this.tmpName=this.name
					this.show = true
				}, this.delay * 1000)
			}
		},
		components: {

		}
	}
</script>

<style lang="less" scoped>
	@import "./ahh5-animated.css";

	.components {
		overflow: hidden;
	}
</style>
