<template>
  <div class="seller" ref="sellerBox">
  	<div class="seller-wrapper">
  		<div class="base-information">
  			<h1 class="title">{{ seller.name }}</h1>
  			<div class="seller-tip">
  				<star :size="36" :score="seller.score"></star>
  				<span class="rating-count">({{ seller.ratingCount }})</span>	
  				<span class="sell-count">月售{{ seller.sellCount }}单</span>
  			</div>
  			<div class="base-show">
  				<div class="pay-time" :class="{'mid-paytime':index===1}" v-for="(item,index) in payTime">
  					<span class="text">{{ item.text }}</span>
  					<div class="number">{{ item.number }}<span class="unit">{{ item.unit }}</span></div>
  				</div>
  			</div>
  			<div class="favorite" @click="toggleFavorite($event)">
  				<span class="icon-favorite" :class="{'active':favorite}" ></span>
  				<span class="text">{{ favoriteText }}</span>
  			</div>
  		</div>
  		<div class="announcement">
  			<h1 class="title">公告与活动</h1>
  			<p class="text">{{ seller.bulletin }}</p>
  			<div class="list" v-if="seller.supports.length > 0" v-for="item in seller.supports">
  				<typeico :size="2" :typeIco="item.type"></typeico>
  				<span class="list-desc">{{ item.description }}</span>
  			</div>
  		</div>
  		<div class="shop-pic">
  			<h1 class="title">商家实景</h1>
  			<div class="pic-wrapper" ref="picWrapper">
  				<ul class="pic-list" ref="picList">
  					<li class="pic-item" v-for="pic in seller.pics">
  						<img :src="pic" width="120" height="90">	
  					</li>
  				</ul>
  			</div>
  		</div>
  		<div class="shop-information">
  			<h1 class="title">商家信息</h1>
  			<ul class="inf-list">
  				<li class="inf-item" v-for="inf in seller.infos">
  					{{ inf }}
  				</li>
  			</ul>
  		</div>
  	</div>
  </div>
</template>

<script>
	import Bscroll from 'better-scroll'
	import star from '../star/star.vue'
	import typeico from '../typeico/typeico.vue'
	import {saveToLocal, loadFromLocal} from '../../common/js/store.js'

	export default{
		props: {
			seller: {
				type: Object
			}
		},
		components: {
			star,
			typeico
		},
		computed:{
			favoriteText() {
				return this.favorite ? '已收藏' : '收藏';
			}
		},
		created() {
			this.$nextTick( () => {
				this.sellerBox = new Bscroll(this.$refs.sellerBox,{
					click: true,
          bounceTime: 100
				});

				if( this.seller.pics ){
					const picWidth = 120,
					      marginR = 6,
					      count = this.seller.pics.length;
					this.$refs.picList.style.width = (picWidth + marginR) * count - marginR + 'px';
					this.picWrapper = new Bscroll(this.$refs.picWrapper,{
						scrollX: true,
						eventPassthrough: 'vertical'
					});
				}

			} )
		},
		data() {
			return {
				payTime: [
					{'text':'起送价','number':this.seller.minPrice,'unit':'元'},
					{'text':'商家配送','number':this.seller.deliveryPrice,'unit':'元'},
					{'text':'平均配送时间','number':this.seller.deliveryTime,'unit':'分钟'}
				],
				favorite: ( () => {
					return loadFromLocal(this.seller.id, 'favorite', false);
				} )()
			}
		},
		methods: {
			toggleFavorite(event) {
				if( !event._constructed ) return;
				this.favorite = !this.favorite;
				saveToLocal(this.seller.id, 'favorite', this.favorite);
			}
		}
	}
</script>

<style lang="stylus" rel="stylesheet/stylus">
  .seller
    position: absolute
    top: 174px
    left: 0
    bottom: 48px
    width: 100%
    overflow: hidden
    background: #f3f5f7
    .seller-wrapper
      .base-information
        position: relative
        padding: 18px
        background: #fff
        border-bottom: 1px solid rgba(7, 17, 27, 0.1)
        .title
          line-height: 14px
          color: rgb(7, 17, 27)
          font-size: 14px
        .seller-tip
          margin: 8px 0px 18px 0
          .star
            display: inline-block
            vertical-align: top
            margin: 0
            .star-item
              vertical-align: middle
          .rating-count
            display: inline-block
            vertical-align: top
            line-height: 18px
            font-size: 10px
            color: rgb(77, 85, 93)
          .sell-count
            display: inline-block
            vertical-align: top
            line-height: 18px
            font-size: 10px
            color: rgb(77, 85, 93)
        .base-show
          display: flex
          justify-content: space-around
          padding: 18px 0
          border-top: 1px solid rgba(7, 17, 27, 0.1)
          border-bottom: 1px solid rgba(7, 17, 27, 0.1)
          .mid-paytime
            border-left: 1px solid rgba(7, 17, 27, 0.1)
            border-right: 1px solid rgba(7, 17, 27, 0.1)
          .pay-time
            flex: 1
            text-align: center
            .text
              line-height: 10px
              font-size: 10px
              color: rgb(147, 153, 159)
            .number
              font-size: 24px
              line-height: 24px
              color: rgb(7, 17, 27)
              .unit
                color: rgba(7, 17, 27, 0.8)
                font-size: 10px
        .favorite
          position: absolute
          width: 50px
          right: 18px
          top: 18px
          text-align: center
          .icon-favorite
            display: block
            line-height: 24px
            font-size: 24px
            color: #d4d6d9
            &.active
              color: rgb(240, 20, 20)
          .text
            line-height: 10px
            font-size: 10px
            color: rgb(77, 85, 93)
      .announcement
        margin: 18px 0
        padding: 18px
        background: #fff
        border-top: 1px solid rgba(7, 17, 27, 0.1)
        border-bottom: 1px solid rgba(7, 17, 27, 0.1)
        .title
          margin-bottom: 8px
          line-height: 14px
          font-size: 14px
          color: rgb(7, 17, 27)
        .text
          margin-bottom: 16px
          line-height: 24px
          color: rgb(240, 20, 20)
          font-size: 12px
        .list
          padding: 16px 12px
          border-top: 1px solid rgba(7, 17, 27, 0.1)
          &:last-child
            padding-bottom: 0
          .icon
            vertical-align: middle
          .list-desc
            display: inline-block
            vertical-align: middle
            line-height: 16px
            font-size: 12px
            color: rgb(7, 17, 27)
      .shop-pic
        padding: 18px
        background: #fff
        border-top: 1px solid rgba(7, 17, 27, 0.1)
        border-bottom: 1px solid rgba(7, 17, 27, 0.1)
        margin-bottom: 18px
        .title
          margin-bottom: 8px
          line-height: 14px
          font-size: 14px
          color: rgb(7, 17, 27)
        .pic-wrapper
          width: 100%
          overflow: hidden
          white-space: nowrap
          .pic-list
            font-size: 0
            .pic-item
              display: inline-block
              margin-right: 6px
              width: 120px
              height: 90px
              &:last-child
                margin: 0
      .shop-information
        padding: 18px
        background: #fff
        border-top: 1px solid rgba(7, 17, 27, 0.1)
        .title
          margin-bottom: 8px
          line-height: 14px
          font-size: 14px
          color: rgb(7, 17, 27)
        .inf-list
          .inf-item
            padding: 16px 12px
            line-height: 16px
            border-top: 1px solid rgba(7, 17, 27, 0.1)
            font-size: 12px
            color: rgb(7, 17, 27)
            &:last-child
              padding-bottom: 0
</style>