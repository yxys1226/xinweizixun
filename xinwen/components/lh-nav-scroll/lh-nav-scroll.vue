<template>
	<view class="nav-scroll">
		<scroll-view :scroll-x="true" class="scroll-view" :scroll-left="currentItemLeft" scroll-with-animation="true">
			<view class="nav-scroll-item" v-for="(item,index) in navItems" :key="index" :style="{'line-height':`${height}rpx`, 'color':`${item.selected?color:'#333'}`}" @click="changTab(index)">
				{{item.name}}
			</view>
			<view class="nav-scroll-line" :style="{ top:`${height-6}rpx`, left: `${currentLineLeft}px`, background:`${color}`, width:`${currentLineWidth}px`}" v-if="loaded"></view>
		</scroll-view>
	</view>
</template>

<script>
	export default{
		props:{
			currentIndex:{
				type:Number,
				default:0
			},
			height:{
				type:Number,
				default:70
			},
			color:{
				type:String,
				default:'#05A8E9'
			},
			items:{
				type:Array,
				default:[]
			},
			scroll:{
				type:Boolean,
				default:true
			}
		},
		data(){
			return{
				loaded:false,
				navItems:[],
				docItems:[],
				scrollLeft:0,
				screenWidth:0,
				
				navItemsIndex:'',
				currentItemLeft:0,
				currentLineLeft:0,
				currentLineWidth:uni.upx2px(36)
			}
		},
		watch:{
			items:{
				handler(newVal, oldVal){
					this.navItems = newVal;
				},
				deep:true
			}
		},
		mounted:async function() {
			this.navItems = this.items;
			this.navItemsIndex = this.navItems.findIndex((item)=>{  //这边是按默认选中的导航，也可以再传递一个默认的currentIndex来当默认值
				return item.selected === true;
			})
			if(this.navItemsIndex === -1){
				this.navItemsIndex = 0;
			}
			
			this.screenWidth = uni.getSystemInfoSync().screenWidth;
			this.docItems = await this.getDocItems();  //使用异步可以控制changTab函数的直接调用
			this.changTab(this.navItemsIndex, false);
		},
		methods:{
			getDocItems(){
				return new Promise((ret)=>{
					this.$nextTick(()=>{
						const query = uni.createSelectorQuery().in(this);  //当前组件作用域内
						setTimeout(()=>{
							const view = query.selectAll('.nav-scroll-item');
							view.boundingClientRect(data => {
								this.docItems = data;  //得到每个菜单的doc属性值
								ret(this.docItems);
							}).exec();
							this.loaded = true;  //需要导航菜单完成后显示
						}, 100);
					})
				})
			},
			changTab(index, callback = true){
				if(callback && this.navItemsIndex === index){  //防止重复点击
					return false;
				}
				const currentDocItem = this.docItems[index];
				const itemLeft = currentDocItem.left || 0; //计算当前选中的导航项加载时候初始的left
				const itemWidth = currentDocItem.width || 0; //当前选中菜单的宽度
				
				this.currentItemLeft = itemLeft - this.screenWidth / 2; //选中菜单居中显示（减去屏幕的一半，剩余部分就是横向滚动条位置）
				this.currentItemLeft += itemWidth / 2; //还要再加上本身宽度的一半的误差
				
				this.currentLineLeft = itemLeft + itemWidth / 2;  //选中菜单的下划线位置 = 菜单所在的位置 + 菜单自身的宽度/2
				this.currentLineLeft -= this.currentLineWidth / 2;   //还要再减去下划线本身宽度一半的误差
				
				this.navItemsIndex = index;
				this.navItems.forEach((nItem,nIndex)=>{
					if(index === nIndex){
						this.$set(this.navItems[nIndex], 'selected', true);
					}else{
						this.$set(this.navItems[nIndex], 'selected', false);
					}
				})
				if(callback){
					this.$emit('currentItem', this.navItems[index]);
				}
			}
		}
	}
</script>

<style lang="scss">
.nav-scroll{
	display: inline-block;
	width: 100%;
	white-space: nowrap;
	position: relative;
	&::after{
		position: absolute;
		left: 0;
		bottom: 0;
		width: 100%;
		height: 2rpx;
		background-color: #ddd;
		transform:scaleY(.5);
		content: '';
	}
	&-item{
		padding:0 20rpx;
		display: inline-block;
		font-size: 32rpx;
	}
	&-line{
		position: absolute;
		height: 4rpx;
		border-radius: 2rpx;
		transition: left .3s ease-in-out;
		z-index: 10;
	}
	::-webkit-scrollbar {
	    display: none;
	}
}
</style>