<template>
	<view class="animated fadeIn faster" style="background: #F5F5F5;">

		<loading-plus v-if="beforeReady"></loading-plus>


		<uni-nav-bar :right-text="isedit?'完成':'编辑'" title="购物车" statusBar
		 :shadow="false" @click-right="isedit = !isedit" :fixed="true"></uni-nav-bar>
		 
		 <!-- 购物车为空 -->
		<view class="py-5 d-flex a-center j-center bg-white"
		v-if="disableSelectAll">
		 	<view class="iconfont icon-gouwuche text-light-muted" style="font-size: 50upx;"></view>
			<text class="text-light-muted mx-2">购物车还是为空</text>
			<view class="px-2 py-1 border border-light-secondary rounded"
			hover-class="bg-light-secondary">
				去逛逛
			</view>
		 </view>
		 
		 <!-- 购物车商品列表 -->
		 <view class="bg-white px-2" v-else>
			 <!-- 列表 -->
		 	<view class="d-flex a-center py-3 border-bottom border-light-secondary"
			v-for="(item,index) in list" :key="index">
		 		<label class="radio d-flex a-center j-center flex-shrink" 
				style="width: 80upx;height: 80upx;" @click="selectItem(index)">
		 			<radio :value="item.id" :checked="item.checked" 
					color="#FD6801"/>
		 		</label>
				
				<image :src="item.cover" 
				mode="widthFix" style="height: 150rpx;width: 150rpx;"
				class="border border-light-secondary rounded p-2 flex-shrink">
				</image>
				
				<view class="flex-1 d-flex flex-column pl-2">
					<view class="text-dark" style="font-size: 35upx;">
						{{item.title}}
					</view>
					<!-- 规格属性 -->
					<view class="d-flex text-light-muted mb-1"
					:class="isedit ? ' p-1 bg-light-secondary mb-2' : ''"
					@tap.stop="showPopup(index,item)"
					v-if="item.skus_type === 1">
						{{item.skusText}}
						<view class="iconfont icon-bottom font ml-auto"
						v-if="isedit"></view>
					</view>
					<view class="mt-auto d-flex j-sb">
						<price>{{item.pprice}}</price>
						<view class="a-self-end">
							<uni-number-box :min="item.minnum" 
							:value="item.num" :max="item.maxnum"
							@change="changeNum($event,item,index)">
							</uni-number-box>
						</view>
					</view>
				</view>
				
		 	</view>
		 </view>
		 
		 
		 
		 <view class="text-center main-text-color font-md font-weight mt-5">
			 为你推荐
		 </view>
		 <view class="position-relative d-flex a-center j-center text-secondary mb-3 pt-3">
			<view style="background: #F5F5F5;z-index: 2;" class="px-2 position-absolute">
				买的人还买了</view>
			<view class="position-absolute" style="height: 1upx;left: 0;right: 0;background-color: #DDDDDD;"></view>
		 </view>
		 <!-- 为你推荐 -->
		 <view class="row j-sb bg-white">
		 	<common-list v-for="(item,index) in hotList"
		 	:key="index" :item="item" :index="index">
		 	</common-list>
		 </view>

		 
		 <!-- 占位 -->
		 <view style="height: 100upx;"></view>
		 <!-- 合计 -->
		 <view class="d-flex a-center position-fixed left-0 right-0 bottom-0 border-top border-light-secondary a-stretch bg-white" style="height: 100upx;z-index: 1000;">
			 <label class="radio d-flex a-center j-center flex-shrink"
			 style="width: 120upx;" @click="doSelectAll">
			 	<radio color="#FD6801" :checked="checkedAll" :disabled="disableSelectAll"/>
			 </label>
			 <template v-if="!isedit">
			 	<view class="flex-1 d-flex a-center j-center font-md">
			 		合计 <price>{{totalPrice}}</price>
			 	</view>
			 	<view class="flex-1 d-flex a-center j-center main-bg-color text-white font-md" hover-class="main-bg-hover-color" @tap="orderConfirm"> 结算</view>
			 </template>
			 <template v-else>
			 	<view class="flex-1 d-flex a-center j-center font-md main-bg-color text-white">
			 		移入收藏
			 	</view>
			 	<view class="flex-1 d-flex a-center j-center bg-danger text-white font-md" hover-class="main-bg-hover-color" @tap="doDelGoods">删除</view>
			 </template>
		 </view>
		 
		 <!-- 属性筛选框 -->
		<sku-popup></sku-popup>
		 
		 
	</view>
</template>

<script>
	import loading from "@/common/mixin/loading.js"
	
	import uniNavBar from "@/components/uni-ui/uni-nav-bar/uni-nav-bar.vue"
	import price from "@/components/common/price.vue"
	import uniNumberBox from "@/components/uni-ui/uni-number-box/uni-number-box.vue"

	import {mapState,mapGetters,mapActions,mapMutations} from "vuex"
	
	import commonList from "@/components/common/common-list.vue"
	import skuPopup from '@/components/cart/sku-popup.vue';
	
	export default {
		mixins:[loading],
		components: {
			uniNavBar,
			price,
			uniNumberBox,
			commonList,
			skuPopup
		},
		data() {
			return {
				isedit: false,
				hotList:[]
			}
		},
		computed: {
			...mapState({
				list:state=>state.cart.list
			}),
			...mapGetters([
				'checkedAll',
				'totalPrice',
				'disableSelectAll'
			])
		},
		onLoad() {
			this.getData()
			// 监听购物车更新
			uni.$on('updateCart',()=>{
				this.getData()
			})
		},
		beforeDestroy() {
			uni.$off('updateCart')
		},
		onPullDownRefresh() {
			this.getData()
		},
		methods:{
			...mapActions([
				'doSelectAll',
				'doDelGoods',
				'doShowPopup',
			]),
			...mapMutations([
				'selectItem',
				'initCartList',
				'unSelectAll'
			]),
			changeNum(e,item,index){
				if (item.num === e) return;
				uni.showLoading({
					title:"加载中..."
				})
				this.$H.post('/cart/updatenumber/'+item.id,{
					num:e
				},{
					token:true
				}).then(res=>{
					console.log(res);
					item.num = e
					uni.hideLoading()
				})
			},
			// 订单结算
			orderConfirm(){
				uni.navigateTo({
					url: '../order-confirm/order-confirm'
				});
			},
			showPopup(index,item){
				if(!this.isedit){
					return;
				}
				this.$H.get('/cart/'+item.id+'/sku',{},{
					token:true
				}).then(res=>{
					
					// 商品规格（选项部分）
					let check = item.skusText.split(',')
					res.selects = res.goods_skus_card.map((v,i)=>{
					  let selected = 0
					  let list = v.goods_skus_card_value.map((v1,i1)=>{
						  if(check[i] === v1.value){
							  selected = i1
						  }
						  return {
							  id:v1.id,
							  name:v1.value
						  }
					  })
					  return {
						id:v.id,
						title:v.name,
						selected,
						list:list
					  }
					})
					// 商品规格（匹配价格）
					res.goods_skus.forEach(item=>{
					  let arr = []
					  for (let key in item.skus) {
						arr.push(item.skus[key].value)
					  }
					  item.skusText = arr.join(',')
					})
					
					this.doShowPopup({
						index:index,
						data:res
					})
				})
			},
			// 获取数据
			getData(){
				// 获取购物车数据
				this.$H.get('/cart',{},{
					token:true,
					toast:false
				}).then(res=>{
					// 取消选中状态
					this.unSelectAll()
					// 赋值
					this.initCartList(res)
					uni.stopPullDownRefresh()
				}).catch(err=>{
					uni.stopPullDownRefresh()
				})
				// 获取热门推荐
				this.$H.get('/goods/hotlist').then(res=>{
					this.hotList = res.map(v=>{
						return {
						  id:v.id,
						  cover:v.cover,
						  title:v.title,
						  desc:v.desc,
						  oprice:v.min_oprice,
						  pprice:v.min_price
						}
					})
				})
			}
		}
	}
</script>

<style>

</style>
