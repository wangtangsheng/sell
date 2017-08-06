<template>
	<div class="goods">
		<div class="menu-wrapper" ref="menu-wrapper">
			<ul>
				<li v-for="(item,index) in goods" class="menu-item" :class="{current: currentIndex === index}" @click="selectMenu(index,$event)">
					<span class="text border-1px" >
						<span v-show="item.type > 0" class="icon" :class="typeMap[item.type]"></span>{{ item.name }}
					</span>
				</li>
			</ul>
		</div>
		<div class="foods-wrapper" ref="foods-wrapper">
			<ul>
				<li v-for="item in goods" class="food-list" ref="food-list">
					<h1 class="title">{{item.name}}</h1>
					<ul>
						<li v-for="food in item.foods" class="border-1px food-item">
							<div class="icon"><img :src="food.icon"></div>
							<div class="content">
								<h2 class="name">{{food.name}}</h2>
								<p class="desc">{{food.description}}</p>
								<div class="extra">
									<span class="count">月售{{food.sellCount}}份</span><span>好评率{{food.rating}}%</span>
								</div>
								<div class="price">
									<span class="now">￥{{food.price}}</span><span class="old" v-show="food.oldPrice">￥{{food.oldPrice}}</span>
								</div>
								<div class="cart-wrapper">
									<cartcontrol :food="food" @select-foods="getSelectFoods" ></cartcontrol>
								</div>
							</div>
						</li>
					</ul>
				</li>
			</ul>
		</div>
		<shopcart :select-foods="selectFoods" :delivery-price="seller.deliveryPrice" :min-price="seller.minPrice"></shopcart>
	</div>
</template>

<script>
import BScroll from 'better-scroll'
import shopcart from '../shopcart/shopcart.vue'
import cartcontrol from '../cartcontrol/cartcontrol.vue'
export default{
	data(){
		return {
			goods:[],
			typeMap:['decrease','discount','special','invoice','guarantee'],
			//每个区间的高度集合
			heightList: [],
			scrollY: 0,
			selectFoods: []
		}
	},
	props:['seller'],
	components: {
		shopcart,
		cartcontrol
	},
	computed: {
		currentIndex (){
			//获取当前的位置对应的索引
			for(let i = 0; i <this.heightList.length; i++){
				let height1 = this.heightList[i];
				let height2 = this.heightList[ i+1 ]
				if(!height2 || (this.scrollY >= height1 && this.scrollY < height2)){
					return i;
				}
			}
			return 0
		}
	},
	methods:{
		selectMenu (index,event){
			if(!event._constructed){
				return false
			}
			//console.log(index,event)
			let foodList = this.$refs['food-list'];
			let el = foodList[index];
			//滚动到指定区域
			this.foodScroll.scrollToElement(el,300)
		},
		_initScroll (){
			console.log(this.$refs);
			this.menuScroll = new BScroll(this.$refs['menu-wrapper'],{
				//点击生效
				click: true
			});
			this.foodScroll = new BScroll(this.$refs['foods-wrapper'],{
				probeType: 3,
				//点击生效
				click: true
			})
			this.foodScroll.on('scroll',(pos) => {
				this.scrollY = Math.abs(Math.round(pos.y));
				//console.log(this.scrollY)
			})
		},
		_calculateHeight (){
			let foodList = this.$refs['food-list'];
			//console.log(foodList);
			let height = 0;
			this.heightList.push(height)
			for(let i = 0; i < foodList.length;i++){
				height += foodList[i].clientHeight;
				this.heightList.push(height);
			}
			console.log(this.heightList)
		},

		getSelectFoods (food){
			
			if(this.selectFoods.length){
				let hasFood = this.selectFoods.some( (item) => {
					return item.name === food.name
				})
				let length = this.selectFoods.length
				if(hasFood){
					//console.log(this.selectFoods)

					for (let i=0; i< length; i++){
						if(this.selectFoods[i].name === food.name){
							if(food.count){
								this.selectFoods.splice(i,1,food) 
							}else{
								this.selectFoods.splice(i,1)
							}
							
							break
						}
					}
				}else{
					this.selectFoods.push(food)
				}

			}else{
				this.selectFoods.push(food)
			}
	
		}
	},
	created(){
		 this.$http.get('/api/goods').then((res) => {
       		let data = res.data
	       if(data.code === 0){
	          this.goods = data.data
	          //
	          this.$nextTick( () => {
	          	 this._initScroll()
	          	 this._calculateHeight()
	          })
	          console.log(this.goods)
	       }
    },(err) => {
      console.log(err)
    
    })
	}
}
	
</script>

<style  lang="stylus" type="text/stylus">
	@import '../../common/stylus/mixin.styl'

	.goods
		position: absolute
		top: 174px
		bottom: 46px
		display: flex
		width: 100%
		overflow: hidden
		.menu-wrapper
			flex: 0 0 80px
			width: 80px
			background-color: #f3f5f7
			.menu-item
				display: table
				height: 54px
				width: 56px
				line-height: 14px
				padding: 0 12px
				&.current
					position: relative
					z-index: 10
					margin-top: -1px
					font-weight: 700
					background-color: #fff
					.text
						border-none()
				.icon
					display: inline-block
					vertical: top
					width: 12px
					height: 12px
					margin-right: 4px
					background-size: 12px 12px
					background-repeat: no-repeat
					&.decrease
						bg-image('decrease_3')
					&.discount
						bg-image('discount_3')
					&.guarantee
						bg-image('guarantee_3')
					&.invoice
						bg-image('invoice_3')
					&.special
						bg-image('special_3')
				.text
					display: table-cell
					width: 56px
					vertical-align: middle
					border-1px(rgba(7,17,27,0.1))
					font-size: 12px


		.foods-wrapper
			flex: 1
			.title
				padding-left: 14px
				height: 26px
				line-height: 26px
				border-left: 2px solid #d9dde1
				font-size: 12px
				color: rgb(147,153,159)
				background-color: #f3f5f7
			.food-item
				display: flex
				padding: 18px 0
				margin: 0 18px
				border-1px(rgba(7,17,27,0.1))
				&:last-child
					border-none()
				.icon
					flex: 0 0 57px
					margin-right: 10px
				.content
					flex: 1
					.name
						margin: 2px 0 8px 0
						height: 12px
						line-height: 14px
						font-size: 14px
						color: rgb(7,17,27)
					.desc, .extra
						line-height: 10px 
						font-size: 10px 
						color: rgb(147,153,159)
					.desc
						margin-bottom: 8px
						line-height: 12px
					.extra
						.count 
							margin-right: 12px
					.price
						font-weight: 700
						line-height: 24px
						.now
							margin-right: 8px
							font-size: 14px
							color: rgb(240,20,20)
						.old
							text-decoration: line-through
							font-size: 10px
							color: rgb(147,153,159)
					.cartcontrol
						position: absolute
						right: 0
						bottom: 12px 





</style>