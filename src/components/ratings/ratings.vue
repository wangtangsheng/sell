<template>
	<div class="ratings" ref="ratings">
		<div class="rating-content">
			<div class="overview border-1px">
				<div class="overview-left">
					<h1 class="score" >{{seller.score}}</h1>
					<div class="title">综合评分</div>
					<div class="rank" >高于周边商家 {{seller.rankRate}}%</div>
				</div>
				<div class="overview-right">
					<div class="score-wrapper">
			            <span class="title">服务态度</span>
			            <star :size="24" :score="seller.serviceScore"></star>
			            <span class="score">{{seller.serviceScore}}</span>
          			</div>
			        <div class="score-wrapper">
			            <span class="title">商品评分</span>
			            <star :size="24" :score="seller.foodScore"></star>
			            <span class="score">{{seller.foodScore}}</span>
			        </div>
          			<div class="delivery-wrapper">
			            <span class="title">送达时间</span>
			            <span class="delivery">{{seller.deliveryTime}}分钟</span>
          			</div>
				</div>
			</div>
			<ratingselect @select="selectRating" @toggle="toggleContent" :selectType="selectType" :onlyContent="onlyContent"
                    :ratings="ratings"></ratingselect>
            <div class="rating-wrapper">
		        <ul>
		          <li v-for="rating in ratings" v-show="needShow(rating.rateType, rating.text)" class="rating-item">
		            <div class="avatar">
		              <img width="28" height="28" :src="rating.avatar">
		            </div>
		            <div class="content">
		              <h1 class="name">{{rating.username}}</h1>
		              <div class="star-wrapper">
		                <star :size="24" :score="rating.score"></star>
		                <span class="delivery" v-show="rating.deliveryTime">{{rating.deliveryTime}}</span>
		              </div>
		              <p class="text">{{rating.text}}</p>
		              <div class="recommend" v-show="rating.recommend && rating.recommend.length">
		                <span class="icon-thumb_up"></span>
		                <span class="item" v-for="item in rating.recommend">{{item}}</span>
		              </div>
		              <div class="time">
		                {{rating.rateTime | formatDate}}
		              </div>
		            </div>
		          </li>
		        </ul>
      		</div>
		</div>
	</div>
</template>

<script>
import star from '@/components/star/star.vue'
import ratingselect from '@/components/ratingSelects/ratingselect.vue'
import BScroll from 'better-scroll'

export default{
	data (){
		return {
			ratings: [],
	        selectType: 2,
	        onlyContent: true
		}
	},
	props:['seller'],
	computed:{
		
	},
	components: {
		star,
		ratingselect
	},
	methods: {
		needShow(type, text) {
			//只看有内容的
	        if (this.onlyContent && !text) {
	          return false;
	        }
	        //默认看所有，否则根据自己的类型判断
	        if (this.selectType === 2) {
	          return true;
	        } else {
	          return type === this.selectType;
	        }
      },
		selectRating(type) {
	        this.selectType = type;
	        //重新渲染，计算高度，否则内容很少也可以滚动很多
	        this.$nextTick(() => {
	          this.scroll.refresh();
	        });
      	},
      	toggleContent() {
	        this.onlyContent = !this.onlyContent;
	        this.$nextTick(() => {
	          this.scroll.refresh();
	        });
      	}

	},
	created (){
		
	    this.$http.get('/api/ratings').then((res) => {
	       let data = res.data
	       if(data.code === 0){
	          this.ratings = data.data
	          this.$nextTick(() => {
	            this.scroll = new BScroll(this.$refs.ratings, {
	            	click: true
            	});
          });
	       }
	    },(err) => {
	      console.log(err)
	    
	    })
		
  	},
  	filters:{
  		formatDate (time){
  			let date = new Date(time)
  			let y = date.getFullYear();
  			let m = date.getMonth() + 1
  			let d = date.getDate();
  			return y + "-"+ m + "-" + d
  		}
  	}
}
	
</script>

<style  lang="stylus" type="text/stylus">
@import '../../common/stylus/mixin.styl'
	.ratings
		position: absolute
		top: 174px
		bottom: 0
		width: 100%
		overflow: hidden
		.overview
			display: flex
			padding: 18px 0
			border-1px(rgba(7,17,27,0.1))
			.overview-left
				flex: 0 0 137px
				width: 137px
				padding: 6px 0
				border-right: 1px solid rgba(7,17,27,0.2)
				text-align: center
				.score
					margin-bottom: 6px
					line-height: 28px
					font-size: 24px
					color: rgb(255,153,0)
				.title
					margin-bottom: 8px
					line-height: 12px
					font-size: 12px
					color: rgb(7,17,27)
				.rank
					line-height: 10px
					font-size: 10px
					color: rgb(147,153,159)
			.overview-right
				flex: 1
				padding: 6px 0 6px 24px 
				.score-wrapper
					margin-bottom: 8px
					font-size: 0
					.title
						display: inline-block
						vertical-align: top
						font-size: 12px
						line-height: 18px
						color: rgb(7,17,27)

					.star
						display: inline-block
						vertical-align: top
						margin: 0 12px
					.score
						display: inline-block
						vertical-align: top
						font-size: 12px
						line-height: 18px
						color: rgb(255,153,0)
				.delivery-wrapper
					font-size: 0
					.title
						line-height: 18px
						font-size: 12px
						color: rgb(7,17,27)
					.delivery
						font-size: 12px
						color: rgb(147,153,159)
						margin-left: 12px
		.rating-wrapper
			padding: 0 18px
			.rating-item
				display: flex
				padding: 18px 0
				border-1px(rgba(7,17,27,.1))
				.avatar
					flex: 0 0 28px
					width: 28px
					margin-right: 12px
					img
						border-radius: 50%
				.content
					position: relative
					flex: 1
					.name
						margin-bottom: 4px
						line-height: 12px
						font-size: 10px
						color: rgb(7,17,27)
					.star-wrapper
						margin-bottom: 6px
						font-size: 0
						.star
							display: inline-block
							vertical-align: top
							margin-right: 6px
						.delivery
							display: inline-block
							vertical-align: top
							line-height: 12px
							font-size: 10px
							color: rgb(147,153,159)
					.text
						margin-bottom: 8px
						line-height: 18px
						color: rgb(7,17,27)
						font-size: 12px
					.recommend
						line-height: 16px
						font-size:0
						.icon-thumb_up, .item
							display: inline-block
							margin: 0 8px 4px 0
							font-size: 9px
						.icon-thumb_up
							color: rgb(0,160,220)
						.item
							padding: 0 6px
							border: 1px solid rgba(7,17,27,.1)
							border-radius: 1px
							color: rgb(147,153,159)
							background-color: #fff
					.time
						position: absolute
						top: 0
						right: 0
						line-height: 12px
						font-size: 10px
						color: rgb(147,153,159)
</style>