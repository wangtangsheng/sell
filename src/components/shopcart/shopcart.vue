<template>
	<div class="shopcart">
		<div class="content" @click="toggleList">
			<div class="content-left">
				<div class="logo-wrapper">
					<div class="logo" :class="{ highlight: totalCount > 0}">
						<span class="icon-shopping_cart"></span>
					</div>
					<div class="num" v-show="totalCount > 0">{{totalCount}}</div>
				</div>
				<div class="price" :class="{highlight: totalPrice > 0}">￥{{totalPrice}}</div>
				<div class="desc">另需配送费{{deliveryPrice}}元</div>
			</div>
			<div class="content-right">
				<div class="pay" :class="payClass">{{payDesc}}</div>
			</div>
		</div>
		
	</div>
</template>

<script>
	import BScroll from 'better-scroll'
	import cartcontrol from '../cartcontrol/cartcontrol.vue'
	export default {
		data (){
			return{
				isFold: true
			} 
		},
		props: {
			selectFoods:{
				type: Array,
				default (){
					return [{
						price: 10,
						count: 0
					}]
				}
			},
			deliveryPrice: {
				type: Number,
				default: 0
			},
			minPrice: {
				type: Number,
				default: 0
			}
		},
		components:{
			cartcontrol
		},
		computed: {
			showList (){
				if( !this.totalCount){
					this.isFold = true;
					return;
				}
				let show = !this.isFold
				if(show){
					this.$nextTick( ()=>{
						if(!this.scroll){
							this.scroll= new BScroll(this.$refs['list-content'],{
							click: true
						})
						}else{
							this.scroll.refresh()
						}
						
					})
				}
				return show
			},
			totalPrice (){
				let total = 0;
				this.selectFoods.forEach( (food) => {
					total += food.price * food.count
				})
				return total
			},
			totalCount (){
				let count = 0
				this.selectFoods.forEach( (food) => {
					count += food.count
				})
				return count
			},
			payDesc (){
				if( this.totalPrice === 0){
					return `￥${this.minPrice}元起送`
				}else if( this.totalPrice < this.minPrice){
					let diff  = this.minPrice -this.totalPrice;
					return `还差${diff}元起送`
				}else {
					return '结算'
				}
			},
			payClass (){
				
				 if( this.totalPrice < this.minPrice){
					return 'not-enough'
				}else {
					return 'enough'
				}
			}
		},
		methods: {
			toggleList (){
				if(!this.totalCount){
				return
				}
				this.isFold = !this.isFold
			},
			getSelectFoods (food){
			
				if(this.selectFoods.length){
					let hasFood = this.selectFoods.some( (item) => {
						return item.name === food.name
					})
					let length = this.selectFoods.length
					if(hasFood){
						console.log(this.selectFoods)

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
		}
	}
</script>
<style type="text/stylus" lang="stylus">
	@import '../../common/stylus/mixin.styl'

	.shopcart
		position: fixed
		left: 0
		bottom: 0
		z-index: 50
		width: 100%
		height: 48px
		.content
			display: flex
			background-color: #141d27
			.content-left
				flex: 1
				font-size: 0
				.logo-wrapper
					display: inline-block
					position: relative
					top: -10px
					margin: 0 12px 
					padding: 6px
					width: 56px
					height: 56px
					box-sizing: border-box
					vertical-align: top
					border-radius: 50%
					background-color: #141d27
					.logo
						width: 100%
						height: 100%
						border-radius: 50%
						text-align: center
						background-color: #2b343c
						&.highlight
							background-color: rgb(0,160,220)
							.icon-shopping_cart
								color: #fff
						.icon-shopping_cart
							font-size: 24px
							line-height: 44px
							color: #80858a
					.num
						position: absolute
						top: 0
						right: 0
						width: 24px 
						height: 16px
						line-height: 16px
						text-align: center
						border-radius: 16px
						font-size: 9px
						font-weight: 700
						color: #fff
						background-color: rgb(240,20,20)
						box-shadow: 0 4px 8px 0 rgba(0,0,0,0.4)
				.price
					display: inline-block
					vertical-align: top
					margin-top: 12px
					font-size: 16px
					font-weight: 700
					line-height: 24px
					padding-right: 12px
					color: rgba(255,255,255,.4)
					border-right: 1px solid rgba(255,255,255,0.1)
					&.highlight
						color: #fff
				.desc
					display: inline-block
					vertical-align: top
					line-height: 24px
					margin: 12px 0 0 12px
					color: rgba(255,255,255,.4)
					font-size: 10px
			.content-right
				flex: 0 0 105px
				width: 105px
				.pay
					height: 48px
					line-height: 48px
					text-align: center
					font-size: 12px 
					color: rgba(255,255,255,.4)
					font-weight: 700
					&.not-enough
						background-color: #2b333b
					&.enough
						background-color: #00b43c
						color: #fff
		.shopcart-lsit
			position: absolute
			bottom: 48px
			left: 0
			z-index: -1
			width: 100%
			&.slide1-enter-active, &.slide1-leave-active
				all 3s ease
			&.slide1-enter, &.slide1-leave-to
				transform: translateY(100%)

			.list-header
				height: 40px
				line-height: 40px
				padding: 0 18px
				background-color: #f3f5f7
				border-bottom: 1px solid rgba(7,17,27,.1)
				.title
					float: left
					font-size: 14px
					color: rgb(7,17,27)
				.empty
					float: right
					font-size: 12px
					color: rgb(0,160,220)
			.list-content
				padding: 0 18px
				max-height: 217px
				overflow: hidden
				background-color: #fff
				.food
					position: relative
					padding: 12px 0
					box-sizing: border-box
					border-1px(rgba(7,17,27,0.1))
					.name
						line-height: 24px
						font-size: 14px
						color: rgb(7,17,27)
					.price
						position: absolute
						right: 100px
						bottom: 12px
						line-height: 24px
						font-size: 14px
						font-weight: 700
						color: rgb(240,20,20)
					.cartcontrol-wrapper
						position: absolute
						right: 0
						bottom: 6px





</style>