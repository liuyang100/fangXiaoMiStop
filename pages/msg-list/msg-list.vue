<template>
	<view class="p-2">
		<view class="mb-2 p-2 bg-white rounded" hover-class="bg-light"
		v-for="(item,index) in list" :key="index"
		@click="open(item)">
			<view class="text-dark font-md">{{item.title}}</view>
			<view class="font text-light-muted">
				{{item.desc}}
			</view>
			<view class="font text-light-muted">{{item.create_time}}</view>
		</view>
	</view>
</template>

<script>
	export default {
		data() {
			return {
				list:[
					{
						title:"你好",
						desc:"开启纯nvue原生渲染，大大提高性能",
						create_time:"2月"
					},
					{
						title:"Hellow word!",
						desc:"开启纯nvue原生渲染，大大提高性能",
						create_time:"9月份"
					}
				]
			}
		},
		methods: {
			open(item){
				uni.navigateTo({
					url: '../msg-detail/msg-detail?detail='+JSON.stringify(item),
				});
			}
		}
	}
</script>

<style>
page{
	background-color: #EEEEEE;
}
</style>
