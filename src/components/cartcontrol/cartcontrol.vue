<template>
	<div class="cartcontrol">
		<!-- <transition name="fade"
  			enter-active-class="fadeRight"
  			leave-active-class="fadeRight"> -->
			<div class="cart-decrease icon-remove_circle_outline" v-show="food.count>0" @click="decreaseCart"></div>
		<!-- </transition> -->
		<div class="cart-count" v-show="food.count>0">{{food.count}}</div>
		<div class="cart-add icon-add_circle" @click="addCart"></div>
	</div>
</template>

<script>
	export	default {
		data (){
			return {
				test:{
					a:1,
					b:2
				}
			}
		},
		props: {
			food: {
				type: Object
			}
		},
		methods: {
			addCart (event){
				//防止电脑端2次点击
				if(!event._constructed){
					return
				}
				console.log("被点击了")
				if( !this.food.count){
					this.food.count = 1;
					this.$set(this.food,'count',1)
				}else {
					this.food.count ++
				}
				//console.log(this.food)
				this.$forceUpdate();
				this.calcuSelectFoods()
			},
			decreaseCart (){
				if(!event._constructed){
					return
				}
				if(this.food.count){
					this.food.count --
				}
				this.$forceUpdate();
				this.calcuSelectFoods()
			},
			calcuSelectFoods (){
				//调用父组件的方法
				this.$emit('select-foods',this.food)
			}
		},
		created (){
			// console.log(this.food)
		}
	}
</script>

<style type="text/stylus" lang="stylus">
	.cartcontrol
		font-size: 10px
		.cart-decrease
			display: inline-block
			padding: 6px
			line-height: 24px
			font-size: 24px		
		.cart-count
			display: inline-block
			vertical-align: top
			width: 12px
			padding: 6px
			line-height: 24px
			text-align: center
			font-size: 10px
			color: rgb(147,153,159)
		.cart-add
			display: inline-block
			line-height: 24px
			font-size: 24px
			padding: 6px
			color: rgb(0,160,220)
</style>
