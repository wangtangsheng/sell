<template>
	<div class="header">
		<div class="content-wrapper">
			<div class="avatar">
				<img :src="seller.avatar" width="64" height="64">
			</div>
			<div class="content">
				<div class="title">
					<span class="brand"></span>
					<span class="name">{{seller.name}}</span>
				</div>
				<div class="description">{{seller.description}}/{{seller.deliveryTime}}分钟送达</div>
				<div class="support" v-if="seller.supports">
					<span class="icon" :class='typeMap[seller.supports[0].type]'></span>
					<span class="text">{{seller.supports[0].description}}</span>
				</div>
			</div>
			<div class="support-count" v-if="seller.supports && seller.supports.length" @click="showDetail">
				<span class="count">{{seller.supports.length}}个</span>
				<span class="icon-keyboard_arrow_right"></span>
			</div>
		</div>
		<div class="bulletin-wrapper" @click="showDetail">
			<span class="bulletin-title"></span><span class="bulletin-text">{{seller.bulletin}}</span><span class="icon-keyboard_arrow_right"></span>
		</div>
		<div class="background">
			<img :src="seller.avatar" width="100%" height="100%">
		</div>
		<transition   name="bounce"
  			enter-active-class="bounceInLeft"
  			leave-active-class="bounceOutRight">
  				
			<div class="detail" v-show="isShowDetail">
				<div class="detail-wrapper">
					<div class="detail-main">
						<h1 class="name">{{seller.name}}</h1>
						<div class="star-wrapper">
							<star size="48" :score="seller.score"></star>
						</div>
						<div class="title">
							<div class="line"></div>
							<div class="text">优惠信息</div>
							<div class="line"></div>
						</div>
						<ul class="supports" v-if="seller.supports">
							<li class="support-item" v-for=" (item,index) in seller.supports">
								<span class="icons" :class="typeMap[item.type]"></span>
								<span class="text">{{item.description}}</span>
							</li>
						</ul>
						<div class="title">
							<div class="line"></div>
							<div class="text">商家公告</div>
							<div class="line"></div>
						</div>
						<div class="bulletin">
							<p class="content">{{seller.bulletin}}</p>
						</div>
					</div>
				</div>
				<div class="detail-footer" @click="hideDetail"><span class="icon-close"></span></div>
			</div>
  		</transition>	
	</div>
</template>

<script >
import star from '@/components/star/star.vue'
export default {
	data (){
		return {
			typeMap:['decrease','discount','special','invoice','guarantee'],
			isShowDetail: false
		}
	},
	components:{
		star
	},
	methods:{
		showDetail (){
			this.isShowDetail = true
		},
		hideDetail (){
			this.isShowDetail = false
		}
	},
	props:['seller'],
	created(){}
	
}
	
</script>

<style  lang="stylus" type="text/stylus">
	@import '../../common/stylus/mixin.styl'
	.header
		position: relative
		overflow: hidden
		color: #fff
		background-color: rgba(7,17,27,0.5) 
		.content-wrapper
			padding:24px 12px 18px 24px
			position: relative
			/*去除空白字符*/
			font-size: 0
			.avatar
				display: inline-block
				vertical-align: top
				img
					border-radius: 2px
			.content
				display: inline-block
				margin-left: 16px
				font-size: 14px
				.title
					margin: 2px 0 8px 0
					font-size: 0
					.brand
						display: inline-block
						vertical-align: top
						width: 30px
						height: 18px
						bg-image('brand')
						background-size: 30px 18px
						background-repeat: no-repeat
					.name
						margin-left: 6px
						font-size: 16px
						line-height: 18px
						font-weight: bold
				.description
					margin-bottom: 10px
					line-height: 12px
					font-size: 12px
				.support 
					.icon
						display: inline-block
						vertical-align: middle
						width: 12px
						height: 12px
						margin-right: 4px
						background-size: 12px 12px 
						background-repeat: no-repeat
						&.decrease
							bg-image('decrease_1')
						&.discount
							bg-image('discount_1')
						&.special
							bg-image('special_1')
						&.guarantee
							bg-image('guarantee_1')
						&.invoice
							bg-image('invoice_1')
					.text
						line-height: 12px
						font-size: 10px
			.support-count
				position: absolute
				right: 12px
				bottom: 14px
				padding: 0 8px
				height: 24px
				line-height: 24px
				border-radius: 14px 
				background-color: rgba(0,0,0,0.2)
				text-align: center
				.count
					font-size:10px
				.icon-keyboard_arrow_right
					font-size: 10px
					line-height: 24px
					margin-left: 2px
		.bulletin-wrapper
			position: relative
			height: 28px
			line-height: 28px
			padding: 0 22px 0 12px
			white-space: nowrap
			overflow: hidden
			text-overflow: ellipsis
			background-color: rgba(7,17,27,0.2)
			.bulletin-title
				display: inline-block
				vertical-align: -2px
				width: 22px
				height: 12px
				bg-image('bulletin')
				background-size: 22px 12px
				background-repeat: no-repeat
			.bulletin-text
				font-size: 10px
				margin: 0 4px	
			.icon-keyboard_arrow_right
				position: absolute
				right: 12px
				bottom: 8px
				font-size: 10px 
		.background
			position: absolute
			top: 0
			left: 0
			width: 100%
			height: 100%
			z-index: -1
			filter: blur(10px)
		.detail
			position: fixed
			z-index: 100
			top: 0
			left: 0
			width: 100%
			height: 100%
			overflow: auto
			background-color: rgba(7,17,27,0.8)
			.detail-wrapper
				width: 100%
				min-height: 100%
				.detail-main
					padding: 64px 36px 84px 36px
					.name
						line-height: 16px
						text-align: center
						font-size: 16px
						font-weight: 700
					.star-wrapper
						margin-top: 16px
						padding: 2px 0
						text-align: center
					.title
						display: flex
						margin: 28px auto 24px auto
						.line
							flex: 1
							position: relative
							top: -6px
							border-bottom: 1px solid rgba(255,255,255,0.2)
						.text
							padding: 0 12px
							font-size: 14px
							font-weight: 700
					.supports
						.support-item
							padding: 0 12px
							margin-bottom: 12px
							font-size: 0
							&:last-child 
								margin-bottom:0
							.icons
								display:inline-block
								width: 16px
								height: 16px
								vertical-align: top
								margin-right: 6px
								background-size: 16px 16px
								background-repeat: no-repeat
								&.decrease
									background-color:red
									bg-image('decrease_2')
								&.discount
									bg-image('discount_2')
								&.special
									bg-image('special_2')
								&.guarantee
									bg-image('guarantee_2')
								&.invoice
									bg-image('invoice_2')
							.text
								line-height: 16px
								font-size: 12px
					.bulletin
						margin: 0 auto
						.content
							padding: 0 12px
							line-height: 14px
							font-size: 12px



			.detail-footer
				width: 32px
				height: 32px
				border-radius: 32px
				margin: -64px auto 0 auto
				font-size: 32px
				color: rgba(255,255,255,.5)





	
</style>