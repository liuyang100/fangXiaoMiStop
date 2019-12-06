<template>
	<view class="bg-white" @click.stop="openDetail">
		<divider></divider>
		<!-- 头部 -->
		<view class="d-flex a-center p-2 border-bottom border-light-secondary">
			<text class="text-light-muted font-md">{{item.create_time}}</text>
			<text class="main-text-color ml-auto font-md">{{item.status}}</text>
		</view>
		<!-- 身体 -->
		<view class="px-2">
			<block v-for="(order,orderIndex) in item.order_items" :key="orderIndex">
				<order-list-item :item="order" :index="orderIndex"></order-list-item>
			</block>
		</view>
		<!-- 底部 -->
		<view class="d-flex a-center p-2">
			<text class="text-dark font-md ml-auto">
				共{{item.total_num}}件商品，合计：￥{{item.total_price}}
			</text>
		</view>
		<view class="d-flex j-end a-center px-2 pb-2">
			<view 
			class="rounded border border-light-secondary py-1 px-2 text-secondary"
			hover-class="bg-light-secondary"
			@click.stop="openAfterSale">
				申请售后
			</view>
			<view class="ml-2 rounded border border-light-secondary py-1 px-2 text-secondary" 
			hover-class="bg-light-secondary"
			@click.stop="openLogistics">
				查看物流
			</view> 
			<view class="ml-2 rounded border border-light-secondary py-1 px-2 text-secondary" 
			hover-class="bg-light-secondary">
				确认收货
			</view>
		</view>
	</view>
</template>

<script>
	import orderListItem from './order-list-item.vue';
	export default {
		components: {
			orderListItem
		},
		props: {
			item: Object,
			index:Number
		},
		methods:{
			openDetail(){
				uni.navigateTo({
					url: '../../pages/order-detail/order-detail',
				});
			},
			openLogistics(){
				uni.navigateTo({
					url: '/pages/logistics-detail/logistics-detail',
				});
			},
			openAfterSale(){
				uni.navigateTo({
					url: '/pages/after-sale/after-sale',
				});
			}
		}
	}
</script>

<style>
</style>
