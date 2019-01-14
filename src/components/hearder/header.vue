<template>
  <div class="header">
  	<div class="content-wrapper">
  		<img class="content-bgi" :src="seller.avatar"></img>
  		<div class="avatar">
  			<img width="64" :src="seller.avatar">
  		</div>
  		<div class="content">
  			<div class="title">
  				<span class="brand"></span>
  				<span class="name">{{ seller.name }}</span>
  			</div>
  			<div class="way">
  				{{ seller.description }}/{{ seller.deliveryTime }}分钟送达
  			</div>
  			<div v-if="seller.supports" class="tips">
				<!-- components typeico -->
  				<typeico :size="1" :typeIco="seller.supports[0].type"></typeico>
  				<span class="tips-text">{{ seller.supports[0].description }}</span>
  			</div>
  		</div>
  		<!-- click -->
  		<div v-if="seller.supports" class="supports-btn" @click="showDet">
  			<span class="sup-text">{{ seller.supports.length }}个</span>
  			<i class="icon-keyboard_arrow_right"></i>
  		</div>
  	</div>
  	<!-- 公告 -->
  	<div class="bulletin-wrapper" @click="showDet">
  		<span class="bulletin-title"></span>
  		<span class="bulletin-text">{{ seller.bulletin }}</span>
  		<i class="icon-keyboard_arrow_right"></i>
  	</div>
  	<!-- 弹层 -->
  	<transition name="slide-fade">
	  	<div class="detial" v-show="detShow">
	  		<div class="detial-wrapper">
	  			<div class="detial-main">
	  				<div class="det-title">{{ seller.name }}</div>
	  			  	<div class="det-score">
	  			  		<star :size="48" :score="seller.score"></star>
	  			  	</div>
	  			  	<div class="favourable">
	  			  		<div class="fav-title">
	  			  			<span class="title-line"></span>
	  			  			<span class="title-text">优惠信息</span>
	  			  			<span class="title-line"></span>
	  			  		</div>
	  			  		<ul class="fav-content" v-if="seller.supports">
	  			  			<li class="fav-item"  v-for="(favItem,index) in seller.supports">
								<typeico class="fav-icon" :size="2" :typeIco="favItem.type"></typeico>
	  			  				<span class="fav-text">{{ favItem.description }}</span>
	  			  			</li>
	  			  		</ul>
	  			  		<!-- 商家公告 -->
	  			  		<div class="fav-title">
	  			  			<span class="title-line"></span>
	  			  			<span class="title-text">商家公告</span>
	  			  			<span class="title-line"></span>
	  			  		</div>
	  			  		<div class="not-content">
	  			  			<span class="not-text">{{ seller.bulletin }}</span>
	  			  		</div>
	  			  	</div>
	  			</div>
	  		</div>
  		  	<div class="detial-close" @click="showDet">
  		  		<i class="icon-close"></i>
  		  	</div>
	  	</div>
  	</transition>
  </div>
</template>

<script>
	import star from '../star/star'
	import typeico from '../../components/typeico/typeico.vue'
	import Bscroll from 'better-scroll'

	export default{
		props: ['seller'],
		data() {
			return{
				icoClass: [],
				detShow: false
			}
		},
		created: function() {
			this.icoClass = ['decrease','discount','guarantee','invoice','special'];
			this.$nextTick( () => {
				// this.initScroll();
			} )
		},
		components: {
			star,
			typeico
		},
		methods: {
			showDet() {
				this.detShow = !this.detShow
			},
			initScroll() {
				this.detialScroll = new Bscroll(this.$refs.detialScroll, {});
			}
		}
	}
</script>

<style lang="stylus" rel="stylesheet/stylus">
	@import '../../common/stylus/mixin.styl'

	.header
	  postion: relative
	  color: #fff
	  overflow: hidden
	  background: rgba(7, 17, 27, 0.5)
	  .content-bgi
	  	position: absolute
	  	display: block
	  	left: 0
	  	top: 0
	  	width: 100%
	  	z-index: -1
	  	filter: blur(10px)
	  .content-wrapper
	  	position: relative
	  	padding: 24px 12px 18px 24px
	  	font-size: 0
	  	.avatar
	  	  display: inline-block
	  	  vertical-align: top
	  	  border-radius: 2px
	  	  overflow: hidden
	  	.content
	  	  display: inline-block
	  	  margin-left: 16px
	  	  font-size: 14px
	  	  .title
	  	    margin: 2px 0 8px 0
	  	  	.brand
	  	  	  display: inline-block
	  	  	  width: 30px
	  	  	  height: 18px
	  	  	  vertical-align: top
	  	  	  bg-image('brand')
	  	  	  background-size: 30px 18px
	  	  	  background-repeat: no-repeat
	  	  	.name
	  	  	  margin-left: 6px
	  	  	  font-size: 16px
	  	  	  line-height: 18px
	  	  	  font-weight: bold
	  	  .way
	  	  	margin-bottom: 10px
	  	  	line-height: 12px
	  	  	font-size: 12px
	  	  .tips
	  	  	.tips-text
	  	  	  line-height: 12px
	  	  	  font-size: 10px
	  	.supports-btn
	  	  position: absolute
	  	  bottom: 16px
	  	  right: 12px
	  	  height: 24px
	  	  line-height: 24px
	  	  padding: 0 8px
	  	  background-color: rgba(0,0,0,0.2)
	  	  text-align: center
	  	  border-radius: 14px
	  	  color: #fff
	  	  font-size: 10px
	  	  .sup-text
	  	  	vertical-align: top
	  	  	margin-right: 2px
	  	  & > i
	  	  	line-height: 24px
	  	  	font-size: 10px
	  .bulletin-wrapper
	  	position: relative
	  	height: 28px
	  	line-height: 28px
	  	padding: 0 22px 0 12px
	  	white-space: nowrap
	  	overflow: hidden
	  	text-overflow: ellipsis
	  	background-color: rgba(7, 17, 27, 0.2)
	  	.bulletin-title
	  	  display: inline-block
	  	  height: 12px
	  	  width: 22px
	  	  vertical-align: middle
	  	  bg-image('bulletin')
	  	  background-size: 22px 12px
	  	  background-repeat: no-repeat
	  	.bulletin-text
	  	  vertical-align: middle
	  	  font-size: 10px
	  	& > i
	  	  position: absolute
	  	  right: 12px
	  	  top: 50%
	  	  transform: translateY(-50%)
	  .detial
	  	position: absolute
	  	top: 0
	  	left: 0
	  	z-index: 300
	  	width: 100%
	  	height: 100%
	  	overflow: auto
	  	background: rgba(7, 17, 27, 0.8)
	  	backdrop-filter: blur(10px)
	  	.detial-wrapper
	  	  min-height: 100%
	  	  .detial-main
	  	  	padding: 64px 36px
		  	.det-title
		  	  height: 16px
		  	  line-height: 16px
		  	  color: #fff
		  	  font-size: 16px
		  	  font-weight: 700
		  	  text-align: center
		  	.favourable
		  	  .fav-title
		  	  	display: flex
		  	  	justify-content: center
		  	  	align-items: center
		  	  	.title-line
		  	  	  display: inline-block
		  	  	  width: 36%
		  	  	  height: 0
		  	  	  vertical-align: middle
		  	  	  border-bottom: 2px solid rgba(225, 225, 225, 0.2)
		  	  	.title-text
		  	  	  margin: 0 2%
		  	  	  font-size: 14px
		  	  	  font-weight: 700
		  	  .fav-content
		  	  	margin: 24px 0 28px
		  	  	.fav-item
		  	  	  margin-bottom: 12px
		  	  	  line-height: 16px
		  	  	  font-size: 0
		  	  	  color: #fff
		  	  	  padding: 0 4%
		  	  	  .fav-icon
		  	  	  	vertical-align: top
		  	  	  	margin-right: 6px
		  	  	  .fav-text
		  	  	  	font-size: 12px
		  	  .not-content
		  	  	margin: 24px 0
		  	  	.not-text
		  	  	  line-height: 24px
		  	  	  font-size: 12px
		  	  	  color: #fff
		.detial-close
		  display: block
		  width: 32px
		  height: 32px
		  line-height: 32px
		  font-size: 32px
		  margin: -64px auto
		  color: #fff
		  clear: both
		  
	.slide-fade-enter-active {
	  transition: all .3s ease;
	}
	.slide-fade-leave-active {
	  transition: all .3s cubic-bezier(1.0, 0.5, 0.8, 1.0);
	}
	.slide-fade-enter, .slide-fade-leave-to
	/* .slide-fade-leave-active for below version 2.1.8 */ {
	  // transform: translateY(20px);
	  transform: scale(0.9)
	  opacity: 0;
	}
</style>