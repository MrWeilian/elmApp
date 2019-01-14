<template>
  <transition name="detialMove">
  	<div class="detials-wrapper" v-show="ifShow" ref="detialWrapper">
	  <div class="detials">
	  	<span class="detials-close icon-keyboard_arrow_right" @click="closeDetial"></span>
	  	<div class="detail-img">
	  		<img :src="detialInfo.image">
	  	</div>
	  	<div class="detial-description">
	  		<h1 class="name">{{ detialInfo.name }}</h1>
	  		<div class="sell-information">
	  			<span class="amount">月售{{ detialInfo.sellCount }}份</span>
	  			<span class="lovely">好评率{{ detialInfo.rating }}%</span>
	  		</div>
			<div class="shop">
				<span class="pay">￥{{ detialInfo.price }}</span>
				<span class="oldpay" v-if="detialInfo.oldPrice">￥{{ detialInfo.oldPrice }}</span>
				<carcontrol class="carcontrol" v-if="detialInfo.count" :foodsItem="detialInfo"></carcontrol>
				<span v-else class="button" @click="addone($event)">加入购物车</span>
			</div>
	  	</div>
	  	<div class="detial-tip" v-if="detialInfo.info">
	  		<h1 class="shop-name">商品介绍</h1>
	  		<p class="tip-text">{{ detialInfo.info }}</p>
	  	</div>
	  	<div class="detial-ratings">
	  		<h1 class="rating-name">商品评价</h1>
	  		<ratingSelect :select-type="selectType" :only-content="onlyContent" :desc="desc" :ratings="detialInfo.ratings" @selectTypeChange="selectChange" @toggleContent="toggleChange"></ratingSelect>

	  		<div class="rating-wrapper">
	  			<ul v-if="detialInfo.ratings && detialInfo.ratings.length">
	  				<li class="rating-item" v-show="needShow(rating.rateType,rating.text)" v-for="rating in detialInfo.ratings">
	  					<div class="timeUser">
	  						<div class="rating-time">{{ rating.rateTime | formatDate }}</div>
	  						<div class="rating-user">{{ rating.username }}<img class="avatar" :src="rating.avatar"></div>
	  					</div>
	  					<div class="rating-text"><span :class="{'icon-thumb_up':rating.rateType === 0, 'icon-thumb_down': rating.rateType === 1}"></span>{{ rating.text }}
	  					</div>
	  				</li>
	  			</ul>
	  			<div v-else class="no-rating">暂无评价</div>
	  		</div>
	  	</div>
	  </div>
  	</div>
  </transition>
</template>

<script>
	import Vue from 'vue'
	import Bscroll from 'better-scroll'
	import carcontrol from '../carcontrol/carcontrol.vue'
	import ratingSelect from '../ratingSelect/ratingSelect.vue'
	import {formatDate} from '../../common/js/date.js'


	const POSITIVE = 0;
	const NEGATIVE = 1;
	const ALL = 2;

	export default{
		props: ['detialInfo'],
		data() {
			return {
				ifShow: false,
				selectType: ALL,
				onlyContent: true,
				desc: {
					all: '全部',
					positive: '推荐',
					negative: '吐槽'
				}
			}
		},
		methods: {
			showDetial() {
				this.ifShow = true;
				this.selectType = ALL;
				this.onlyContent = true;
				this.$nextTick( () => {
				if( !this.scroll ){
					this.scroll = new Bscroll(this.$refs.detialWrapper,{
						click: true
					});
				}else{
					this.scroll.refresh();
				}
			} )
			},
			closeDetial() {
				this.ifShow = false;
			},
			addone(event) {
				if( event.view ) return;
				Vue.set(this.detialInfo, 'count', 1)
			},
			selectChange(num) {
				this.selectType = num;
			},
			toggleChange(boo){
				this.onlyContent = boo;
			},
			needShow(type, text) {
				this.$nextTick( () => {
					this.scroll.refresh();
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
		},
		components: {
			carcontrol,
			ratingSelect
		}
	}
</script>

<style lang="stylus" rel="stylesheet/stylus">
    .detials-wrapper
       position: fixed
       width: 100%
       top: 0
       bottom: 48px
       background: #f3f5f7
       z-index: 100
       .detials
       	 .detials-close
       	   position: absolute
       	   z-index: 88
       	   padding: 18px
       	   font-size: 18px
       	   line-height: 24px
       	   font-weight: 700
       	   color: #fff
       	   transform: rotateY(180deg)
         .detail-img
           position: relative
           width: 100%
           height: 0
           padding-top: 100%
           & > img
           	 position: absolute
           	 top: 0
           	 left: 0
             width: 100%
             height: 100%
         .detial-description
           background: #fff
           padding: 18px
           border-bottom: 1px solid rgba(7, 17, 27, 0.1)
           .name
            line-height: 14px
            color: rgb(7, 17, 27)
            font-size: 14px
            font-weight: 700
           .sell-information
             margin: 8px 0 18px 0
             .amount,.lovely
               margin-right: 12px
               line-height: 10px
               font-size: 10px
               color: rgb(147, 153, 159)
           .shop
             .pay
               color: rgb(240, 20, 20)
               line-height: 24px
               font-weight: 700
               font-size: 14px
             .oldpay
               display: inline-block
               position: relative
               line-height: 24px
               color: rgb(147, 153, 159)
               font-size: 10px
               font-weight: 700
           	   &:after
           	     position: absolute
           	     left: 0
           	     top: 11px
           	     content: ""
           	     height: 1px
           	     width: 100%
           	     background: rgb(147, 153, 159)
             .button
               display: inline-block
               padding: 12px
               float: right
               line-height: 12px
               background: rgb(0, 160, 220)
               border-radius: 24px
               color: #fff
               font-size: 10px
             .carcontrol
               float: right
         .detial-tip
           background: #fff
           margin: 16px 0
           padding: 18px
           border-top: 1px solid rgba(7, 17, 27, 0.1)
           border-bottom: 1px solid rgba(7, 17, 27, 0.1)
           .shop-name
           	 margin-bottom: 8px
             color: rgb(7, 17, 27)
             font-size: 14px
           .tip-text
             color: rgb(77, 85, 93)
             line-height: 24px
             font-size: 12px
         .detial-ratings
           margin: 16px 0
           padding: 18px
           background: #fff
           border-top: 1px solid rgba(7, 17, 27, 0.1)
           .rating-name
             margin-bottom: 8px
             color: rgb(7, 17, 27)
             font-size: 14px
           .rating-wrapper
             .rating-item
               .timeUser
                 display: flex
                 justify-content: space-between
                 .rating-time,.rating-user
                   color: rgb(147, 153, 159)
                   line-height: 24px
                   font-size: 10px
                   .avatar
                   	 display: inline-block
                   	 line-height: 24px
                     width: 12px
                     height: 12px
                     margin-left: 6px
                     border-radius: 50%
               .rating-text
                 line-height: 24px
               	 color: rgb(7, 17, 27)
                 font-size: 12px
                 .icon-thumb_down,.icon-thumb_up
                   line-height: 24px
                   margin-right: 4px
                   color: rgb(147, 153, 159)
                   font-size: 12px
                 .icon-thumb_up
                   color: rgb(0, 160, 220)
             .no-rating
               padding: 16px 0
               font-size: 12px
               color: rgb(147, 153, 159)
               

  .detialMove-enter-active,.detialMove-leave-active
    transition: all .3s ease
    transform: translate3d(0, 0, 0)
  .detialMove-enter,.detialMove-leave-to
    transform: translate3d(100%, 0, 0)
</style>