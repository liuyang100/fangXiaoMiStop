<script>
	export default {
		// 全局变量
		globalData:{
			text:""
		},
		onUniNViewMessage(e){
			let data = e.data;
			console.log(e.data)
			// 处理
			if(data.from && data.from === 'index'){
				// 通知分类页修改数据
				uni.$emit('index',data)
			}
		},
		onLaunch: function() {
			console.log('App Launch')
			// 初始化登录状态
			this.$store.commit('initUser')
		},
		onShow: function() {
			console.log('App Show')
		},
		onHide: function() {
			console.log('App Hide')
		}
	}
</script>

<style>
	/*每个页面公共css */
	/* 官方ui库 */
	@import "/common/uni.css";
	/* 第三方动画库 */
	@import "/common/animate.css";
	/* 自定义图标库 */
	@import "/common/icon.css";
	/* UI基础库 */
	@import "/common/zcm-main.css";
	/* 公共样式 */
	@import "/common/common.css";
</style>
