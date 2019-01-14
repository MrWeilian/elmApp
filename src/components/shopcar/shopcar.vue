<template>
  <div class="shop-car">
  	<div class="car-content" @click="isShowList">
  		<div class="car-left">
	  		<div class="logo-wrapper">
	  			<div class="logo" :class="{ 'highlight': totalNum > 0 }">
	  				<i class="icon-shopping_cart"></i>
	  			</div>
	  			<div class="num" v-if="totalNum > 0">{{ totalNum }}</div>
	  		</div>
	  		<div class="car-price" :class="{ 'highlight': totalNum > 0 }">￥{{ totalPrice }}</div>
	  		<div class="description">另需配送费￥{{ sellerDeliveryPrice }}元</div>
	  	</div>
	  	<div class="car-right" :class="{ 'highlight': minprice.islight }">{{ minprice.tip }}</div>
  	</div>
  	<div class="ball-wrapper">
  		<transition v-for="ballItem in balls" name="ball" @before-enter="ballBeforeEnter" @enter="ballEnter" @after-enter="ballAfterEnter">
  			<div class="ball" v-show="ballItem.show">
  				<div class="inner icon-add_circle"></div>
  			</div>
  		</transition>
  	</div>
  	<div class="listmask" v-show="listShow"></div>
  	<transition name="carList">
  		<div class="shop-car-list clearfix" v-show="listShow" ref="shopCarList">
	  		<div class="car-list-head">
	  			<h1 class="title">购物车</h1>
	  			<span class="empty" @click="emptyAll">清空</span>
	  		</div>
	  		<div class="car-list-content" ref="carListContent">
	  			<ul>
		  			<li class="evlist" v-for="selected in selectFoods">
		  				<div class="car-list-food">{{ selected.name }}</div>
		  				<div>
		  					<div class="car-list-pay">￥{{ selected.price }}</div>
				  			<div class="car-list-count">
				  				<carcontrol :foodsItem="selected"></carcontrol>
				  			</div>
		  				</div>
		  			</li>
	  			</ul>
	  		</div>
	  	</div>
  	</transition>
  </div>
</template>

<script>
	import carcontrol from '../carcontrol/carcontrol.vue'
	import Bscroll from 'better-scroll'

	export default{
		props: ['sellerMinprice','sellerDeliveryPrice','selectFoods','thisBall'],
		data() {
			return {
				balls: [
					{
						show: false
					},
					{
						show: false
					},
					{
						show: false
					},
					{
						show: false
					},
					{
						show: false
					}
				],
				ballRun: [],
				flod: true
			}
		},
		computed: {
			totalPrice() {
				let total = 0;
				this.selectFoods.forEach( (food) => {
					total += food.price * food.count;
				} )
				return total;
			},
			totalNum() {
				let total = 0;
				this.selectFoods.forEach( (food) => {
					total += food.count;
				} )
				return total;
			},
			minprice() {
				let backbuy = {
					tip: '',
					islight: false
				};
				if( this.selectFoods.length == 0 ){
					backbuy.tip = '￥'+ this.sellerMinprice + '起送';
					backbuy.islight = false;
				}else{
					if( this.sellerMinprice <= this.totalPrice ){
						backbuy.tip = '去结算';
						backbuy.islight = true;
					}else{
						backbuy.tip = '还差￥'+ (this.sellerMinprice - this.totalPrice) +'起送';
						backbuy.islight = false;
					}
				}
				return backbuy;
			},
			listShow() {
				if( !this.totalNum ){
					this.flod = false;
					return;
				}
				let show = this.flod;
				if( show ){
					this.$nextTick( () => {
						if( !this.scroll ){
							this.scroll = new Bscroll(this.$refs.carListContent,{
								click: true
							})
						}else{
							this.scroll.refresh();
						}
						
					} )

				}
				return show;
			}
		},
		methods: {
			ballBeforeEnter(el) {
				let count = this.balls.length;
				while( count-- ) {
					let ballnow = this.balls[count];
					if( ballnow.show ){
						this.ballRun.push(ballnow);
						
						/* 计算小球位置 */
						const ball = { left: 32, bottom: 21 };

						let clientHeight = document.documentElement.clientHeight,
						transformY = clientHeight - this.thisBall.ball.getBoundingClientRect().top - ball.bottom - 8 - 18,
						transformX = this.thisBall.ball.getBoundingClientRect().left - ball.left + 10;
						/* 计算小球位置 */

						el.style.transform = 'translate3d(0,'+ -transformY +'px,0)';
						let inner = el.getElementsByClassName('inner')[0];
						inner.style.transform = 'translate3d('+ transformX +'px,0,0)';
					}
				}
			},
			ballEnter(el) {
				/*el.style.transition = 'all .8s ease';
				setTimeout(function(){
					el.style.transform = 'translate3d(0,0,0)';
					setTimeout(function(){
						el.style.transform = 'translate3d(0,0,0)';
					},0)
					'+ this.ballXY.x +'px
				},0)*/

				/* eslint-disable no-unused-vars */
                let refresh = el.offsetHeight;
                this.$nextTick( () => {
                	el.style.transform = 'translate3d(0,0,0)';
                	// el.style.transition = 'all .3s cubic-bezier(.43, -0.8, .75, .4)';
                	let inner = el.getElementsByClassName('inner')[0];
                	inner.style.transform = 'translate3d(0,0,0)';
                	// inner.style.transition = 'all .3s linear';
                } )
			},
			ballAfterEnter(el) {
				el.style.display = 'none';
				this.ballRun.shift().show = false;

				// el.style.transform = 'translate3d(0,0,0)';
			},
			isShowList() {
				if( this.totalNum <= 0 ) return;
				this.flod = !this.flod
			},
			emptyAll() {
				this.selectFoods.forEach( (food) => {
					food.count = 0;
				} )
			}
		},
		watch: {
			thisBall() {
				console.log(this)
				for( let i = 0; i < this.balls.length; i++ ){
					if( !this.balls[i].show ){
						this.balls[i].show = true;
						return;
					}
				}
			}
		},
		components: {
			carcontrol
		}
	}
</script>

<style lang="stylus" rel="stylesheet/stylus">
  .shop-car
    position: absolute
    left: 0
    bottom: 0
    width: 100%
    height: 48px
    background: #141d27
    z-index: 100
    .car-content
      display: flex
      flex-wrap: nowrap
	    .car-left
	      position: absolute	      
	      width: 100%
	      z-index: 100
	      flex: 1
	      display: flex
	      font-size: 0
	      background: #141d27
	      flex-wrap: nowrap
	      .logo-wrapper
	        position: absolute
	        transform: translateY(-10px)
	        width: 44px
	        height: 42px
	        line-height: 46px
	        z-index: 100
	        margin: 0 10px
	        background: #141d27
	        border: 8px solid #141d27
	        border-radius: 50%
	        font-size: 24px
	        color: rgba(255, 255, 255, 0.4)
	        .logo
	          width: 100%
	          height: 100%
	          text-align: center
	          border-radius: 50%
	          background: #2b343c
	          color: rgba(255, 255, 255, 0.4)
	          &.highlight
	          	background: rgb(0, 160, 220)
	          	color: #fff
	        .num
	          position: absolute
	          top: -8px
	          left: 26px
	          width: 24px
	          background: rgb(240, 20, 20)
	          text-align: center
	          color: #fff
	          border-radius: 12px
	          box-shadow: 0 4px 8px 0 rgba(0, 0, 0, 0.4)
	          line-height: 16px
	          font-size: 9px
	          font-weight: 700
	      .car-price	        
	        line-height: 24px
	        margin: 12px 6px 12px 80px
	        box-sizing: border-box
	        padding-right: 6px
	        border-right: 1px solid rgba(255, 255, 255, 0.1)
	        color: rgba(255, 255, 255, 0.4)
	        font-size: 16px
	        font-weight: 700
	        &.highlight
	          color: #fff
	      .description
	        margin-top: 12px
	        max-width: 100px
	        line-height: 24px
	        font-size: 10px
	        font-weight: 400
	        white-space: nowrap
	        color: rgba(255, 255, 255, 0.4)
	    .car-right
	      position: absolute
	      right: 0
	      z-index: 100
	   	  width: 105px
	      flex: 0 0 105px
	      background: #2b343c
	      color: rgba(255, 255, 255, 0.4)
	      text-align: center
	      line-height: 48px
	      font-weight: 700
	      font-size: 12px
	      &.highlight
	      	height: 48px
	      	background: #00b43c
	      	color: #fff
    .ball-wrapper
      .ball
        transition: all .4s cubic-bezier(.43, -.5, .75, .4)
	    .inner
          position: fixed
          z-index: 80
          left: 32px
          bottom: 21px
          color: rgb(0, 160, 220)
          border-radius: 50%
          font-size: 16px
          transition: all .4s linear
    .listmask
      position: fixed
      width: 100%
      height: 100%
      left: 0;
      top: 0px
      z-index: -1
      backdrop-filter: blur(10px)
      background: rgba(7, 17, 27, 0.6)
    .shop-car-list
      width: 100%
      position: absolute
      max-height: 305px
      bottom: 48px
      z-index: 10
      .car-list-head
        height: 40px
        padding: 0 18px
        background: #f3f5f7
        border-bottom: 1px solid rgba(7, 17, 27, 0.1)
        .title
          display: inline-block
          line-height: 40px
          font-size: 14px
          color: rgb(7, 17, 27)
        .empty
          display: inline-block
          float: right
          line-height: 40px
          padding: 0 8px
          color: rgb(0, 160, 220)
          font-size: 12px
      .car-list-content
      	background: #fff
      	overflow: hidden
      	padding: 0 18px
      	max-height: 265px
      	.evlist
      	  padding: 12px 0
      	  font-size: 0
      	  display: flex
      	  align-items: center
      	  justify-content: space-between
      	  .car-list-food
      	  	line-height: 24px
      	  	font-size: 14px
      	  	color: rgb(7, 17, 27)
      	  .car-list-pay
      	  	display: inline-block
      	  	line-height: 24px
      	  	vertical-align: middle
      	  	color: rgb(240, 20, 20)
      	  	font-weight: 700
      	  	font-size: 14px
      	  .car-list-count
      	  	display: inline-block
      	  	line-height: 24px
      	  	vertical-align: middle
	  	
  .carList-enter-active,.carList-leave-active,.mask-enter-active,.mask-leave-active
    transition: all .3s ease
  .carList-enter,.carList-leave-to
  	transform: translate3d(0, 100%, 0)
  	opacity: 0
</style>