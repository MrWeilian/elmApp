<template>
  <div class="ratings" ref="ratings">
  	<div class="ratings-content">
  		<div class="overview">
  			<div class="overview-left">
  				<h1 class="score">{{ seller.score }}</h1>
  				<div class="title">综合评分</div>
  				<div class="rank">高于周边商家{{ seller.rankRate }}%</div>
  			</div>
  			<div class="overview-right">
  				<div class="score-wrapper">
  					<span class="title">服务态度</span>
  					<star :size="36" :score="seller.serviceScore"></star>
  					<span class="score">{{ seller.serviceScore }}</span>
  				</div>
  				<div class="score-wrapper">
  					<span class="title">商品评分</span>
  					<star :size="36" :score="seller.foodScore"></star>
  					<span class="score">{{ seller.foodScore }}</span>
  				</div>
  				<div class="delivery-wrapper">
  					<span class="delivery-title">送达时间</span>
  					<span class="delivery-time">{{ seller.deliveryTime }}分钟</span>
  				</div>
  			</div>
  		</div>
  		<ratingSelect :select-type="selectType" :only-content="onlyContent" :ratings="ratings" @selectTypeChange="selectChange" @toggleContent="toggleChange"></ratingSelect>
  		<div class="ratings-wrapper">
  			<ul class="ratings-ul">
  				<li v-for="rating in ratings" class="ratings-item" v-show="showRatings(rating.rateType,rating.text)">
  					<div class="avatar">
  						<img :src="rating.avatar">
  					</div>
  					<div class="content">
  						<h1 class="name">{{ rating.username }}</h1>
  						<div class="star-wrapper">
  							<star :size="24" :score="rating.score"></star>
  							<span class="delivery" v-show="rating.deliveryTime">{{ rating.deliveryTime }}分钟送达</span>
  						</div>
  						<p class="text">{{ rating.text }}</p>
  						<div class="recommend" v-show="rating.recommend && rating.recommend.length">
  							<span class="icon-thumb_up"></span>
  							<span class="recommendSpan border-1px" v-for="recommend in rating.recommend">{{ recommend }}</span>
  						</div>
  						<div class="time">
  							{{ rating.rateTime | formatDate }}
  						</div>
  					</div>
  				</li>
  			</ul>
  		</div>
  	</div>
  </div>
</template>

<script>
	import star from '../star/star.vue'
	import ratingSelect from '../ratingSelect/ratingSelect.vue'
	import Bscroll from 'better-scroll'
	import {formatDate} from '../../common/js/date.js'

	const ErrOk = 0;
	const POSITIVE = 0;
	const NEGATIVE = 1;
	const ALL = 2;

	export default{
		props: {
			seller:{
				type: Object
			}
		},
		data() {
			return {
				ratings: [],
				selectType: ALL,
				onlyContent: true
			}
		},
		created() {
			this.$http.get('api/ratings').then( (response) => {
				if( response.body.errno === ErrOk ){
					this.ratings = response.body.data;
					this.$nextTick( () => {
						this.initScroll();
					} )
				}
			} )
		},
		components: {
			star,
			ratingSelect
		},
		methods: {
			selectChange(num) {
				this.selectType = num;
			},
			toggleChange(boo){
				this.onlyContent = boo;
			},
			initScroll() {
				this.ratingsScroll = new Bscroll(this.$refs.ratings,{
					click: true,
          bounceTime: 100
				});
			},
			showRatings(type,text){
				this.$nextTick( () => {
					this.ratingsScroll.refresh();
				} )

				if( this.onlyContent && !text ){
					return false;
				}
				if( this.selectType === ALL ){
					return true;
				}else{
					return type === this.selectType;
				}
			}
		},
		filters:{
			formatDate(time) {
				let date = new Date(time);
				return formatDate(date,'yyyy-MM-dd hh:mm')
			}
		}
	}
</script>

<style lang="stylus" rel="stylesheet/stylus">
  @import '../../common/stylus/base.styl'

  .ratings
    position: absolute
    top: 174px
    bottom: 48px
    left: 0
    width: 100%
    background: #f3f5f7
    overflow: hidden
    .overview
      display: flex
      padding: 18px 0
      background: #fff
      border-bottom: 1px solid rgba(7, 17, 27, 0.1)
      margin-bottom: 16px
      .overview-left
        flex: 0 0 137px
        padding: 6px 0
        width: 137px
        border-right: 1px solid rgba(7, 17, 27, 0.1)
        text-align: center
        @media only screen and (max-width: 320px)
          flex: 0 0 120px
          width: 120px
        .score
          line-height: 28px
          font-size: 24px
          margin-bottom: 6px
          color: rgb(255, 153, 0)
        .title
          margin-bottom: 8px
          line-height: 12px
          font-size: 12px
          color: rgb(7, 17, 27)
       	.rank
       	  line-height: 10px
       	  font-size: 10px
       	  color: rgb(147, 153, 159)
       	  padding-bottom: 6px
      .overview-right
      	flex: 1
      	padding: 6px 0 6px 24px
      	@media only screen and (max-width: 320px)
          padding-left: 6px
      	.score-wrapper
          margin-bottom: 8px
          line-height: 18px
          font-size: 0
          .title
            display: inline-block
            vertical-align: top
            font-size: 12px
            color: rgb(7, 17, 27)
      	  .star
      	    margin: 0 12px
      	    display: inline-block
      	    vertical-align: top
      	    @media only screen and (max-width: 320px)
              margin: 0 0 0 6px
      	    .star-item
      	      margin: 0 6px 0 0
      	  .score
      	    display: inline-block
      	    vertical-align: top
      	    font-size: 12px
      	    color: rgb(255, 153, 0)
      	.delivery-wrapper
          .delivery-title
            font-size: 12px
            color: rgb(7, 17, 27)
          .delivery-time
          	margin-left: 12px
          	font-size: 12px
          	color: rgb(147, 153, 159)
    .ratingSelect
      padding: 0 18px
    .ratings-wrapper
      background: #fff
    .ratings-ul
      padding: 0 18px
      .ratings-item
        display: flex
        padding: 18px 0
        border-bottom: 1px solid rgba(7, 17, 27, 0.1)
        &:last-child
          border: none
        .avatar
          flex: 0 0 28px
          width: 28px
          & > img
            width: 28px
            height: 28px
            border-radius: 50%
        .content
          position: relative
          flex: 1
          margin-left: 12px
          .time
            position: absolute
            line-height: 12px
            top: 0
            right: 0
            font-size: 10px
            color: rgb(147, 153, 159)
          .name
            color: rgb(7, 17, 27)
            font-size: 10px
          .star-wrapper
            margin: 4px 6px 6px 0px
            .star              
          	  margin: 0
          	  display: inline-block
          	  vertical-align: top
            .star-item
              margin: 0 3px 0 0
            .delivery
              display: inline-block
              vertical-align: top
              font-size: 10px
              color: rgb(147, 153, 159)
              line-height: 16px
          .text
            margin-bottom: 8px
            line-height: 18px
            font-size: 12px
            color: rgb(7, 17, 27)
          .recommend
            font-size: 0
            .icon-thumb_up
              line-height: 18px
              color: rgb(0, 160, 220)
              font-size: 12px
            .recommendSpan
              display: inline-block
              line-height: 16px
              padding: 0 6px
              color: rgb(147, 153, 159)
              font-size: 9px
              border: 1px solid rgba(7, 17, 27, 0.1)
              margin: 0 4px 4px 4px
</style>