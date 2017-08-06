<template>
	<div class="star" :class="starSize">
		<span v-for="starItem in starItems" :class="starItem" class="star-item" ></span>
	</div>
</template>

<script> 
	const STARLENGTH = 5
	export default {
		data (){
			return {

			}
		},
		props:['size','score'],
		computed:{
			starSize (){
				return 'star-' + this.size
			},
			starItems (){
				let result = [];
				let score = Math.floor(this.score * 2)/2;
				let halfLength = score % 1 !== 0;
				let onLength = Math.floor(score);
				for (let i = 0; i < onLength; i++){
					result.push('on')
				}
				if(halfLength){
					result.push('half')
				}
				while(result.length < STARLENGTH){
					result.push('off')
				}
				return result
			}
		},
		methods:{
			getScore (){
				console.log(this.score)
			}
		}

	}
</script>

<style lang="stylus" type="text/stylus">
	@import '../../common/stylus/mixin.styl'
	.star
		&.star-48
			.star-item
				display: inline-block
				width: 20px
				height: 20px
				margin-right: 20px
				&:last-child
					margin-right: 0
				background-size: 20px 20px
				background-repeat: no-repeat
				&.on
					bg-image('star48_on')
				&.half
					bg-image('star48_half')
				&.off
					bg-image('star48_off')
		&.star-36
			.star-item
				display: inline-block
				width: 15px
				height: 15px
				margin-right: 16px
				&:last-child
					margin-right: 0
				background-size: 15px 15px
				background-repeat: no-repeat
				&.on
					bg-image('star36_on')
				&.half
					bg-image('star36_half')
				&.off
					bg-image('star36_off')
		&.star-24
			.star-item
				display: inline-block
				width: 10px
				height: 10px
				margin-right: 3px
				&:last-child
					margin-right: 0
				background-size: 10px 10px
				background-repeat: no-repeat
				&.on
					bg-image('star24_on')
				&.half
					bg-image('star24_half')
				&.off
					bg-image('star24_off')


	
</style>