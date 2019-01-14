<template>
  <!-- foodSelected：挂载已选择商品传值父组建 -->
  <div class="goods" :foodSelected="foodSelected">
  	<div class="menu-wrapper" ref="menuWrapper">
  		<ul class="menu" v-if="goods">
  			<li @click="oppositeClick(index,$event)" class="menu-item" v-for="(menuItem,index) in goods" :class="{'current': currentIndex === index}">
  				<typeico v-show="menuItem.type > 0" :size="1" :typeIco="menuItem.type"></typeico>{{ menuItem.name }}
  			</li>
  		</ul>
  	</div>
  	<div class="foods-wrapper" ref="foodsWrapper">
  		<ul>
  			<li v-for="foodTitleItem in goods" class="foods-type-hook">
  				<h1 class="foods-type">{{ foodTitleItem.name }}</h1>
	  			<ul class="foods-type-wrapper">
	  				<li class="food-item border-1px" v-for="foodsItem in foodTitleItem.foods" @click="detialClick($event,foodsItem)">
	  					<img :src="foodsItem.icon" class="food-img">
	  					<div class="food-information">
	  						<div class="inf-title">{{ foodsItem.name }}</div>
	  						<div class="inf-description"><p>{{ foodsItem.description }}</p></div>
	  						<div class="inf-tips">
	  							<span class="sell">月售{{ foodsItem.sellCount }}</span>
	  							<span class="rating">好评率{{ foodsItem.rating }}%</span>
	  						</div>
	  						<div class="price">
	  							￥{{ foodsItem.price }}
	  							<span class="old-price" v-show="foodsItem.oldPrice">
	  								￥{{ foodsItem.oldPrice }}&nbsp;
	  							</span>
	  						</div>
	  					</div>
	  					<div class="carcontrol-wrapper">
  							<carcontrol :foodsItem="foodsItem" @ballDom="getBall"></carcontrol>
  						</div>
	  				</li>
	  			</ul>
  			</li>
  		</ul>
  	</div>
  	<detialFood :detialInfo="detialInfo" ref="detialFood"></detialFood>
  </div>
</template>

<script>
	import typeico from '../../components/typeico/typeico.vue'
	import Bscroll from 'better-scroll'
	import carcontrol from '../carcontrol/carcontrol.vue'
	import detialFood from '../detial/detial.vue'

	const ErrOk = 0;

	export default{
		props: ['seller'],
		data() {
			return{
				goods: [],
				// 存放列表高度区间
				listHeight: [],
				scrollY: 0,
				detialInfo: {}
			}
		},
		computed:{
			currentIndex() {
				for( let i = 0; i < this.listHeight.length; i++ ){
					let height1 = this.listHeight[i],
						height2 = this.listHeight[i + 1];
					if( !height2 || (this.scrollY >= height1 && this.scrollY < height2) ){
						return i;
					}
				}
				return 0;
			},
			foodSelected() {
				let foods = [];
				this.goods.forEach( (good) => {
					good.foods.forEach( (food) => {
						if( food.count ) foods.push( food )
					} )
				} )
				this.$emit('foodSelected', foods);
			}
		},
		created() {
			this.$http.get('api/goods').then( (response) => {
				if( response.body.errno === ErrOk ){
					this.goods = response.body.data;
					// console.log(this.goods)
					this.$nextTick( () => {
						this.initScroll();
						this.calculator();
					} )
				}
			} )
		},
		components: {
			typeico,
			carcontrol,
			detialFood
		},
		methods: {
			initScroll() {
				this.menuScroll = new Bscroll(this.$refs.menuWrapper,{
					click: true,
					bounceTime: 100
				})
				this.foodScroll = new Bscroll(this.$refs.foodsWrapper,{
					probeType: 3,
					click: true,
					bounceTime: 100
				})
				this.foodScroll.on('scroll',(pos) => {
					this.scrollY = Math.abs( Math.round( pos.y ) );
				})
			},
			calculator() {
				let list = this.$refs.foodsWrapper.getElementsByClassName('foods-type-hook');
				let height = 0;
				this.listHeight.push(height);

				for( let i = 0; i < list.length; i++ ){
					let itemheight = list[i].clientHeight;
					height += itemheight;
					this.listHeight.push( height );
				}
			},
			oppositeClick(index,event) {
				if( !event._constructed ){
					return;
				}
				// 不懂_constructed就浏览器打印
				// console.log(event)
				let list = this.$refs.foodsWrapper.getElementsByClassName('foods-type-hook');
				let el = list[index];
				this.foodScroll.scrollToElement(el,300);
			},
			getBall( el ) {
				this.$emit("sendBall", el);
			},
			detialClick(event,food) {
				if( event.view ) return;
				this.detialInfo = food;
				this.$refs.detialFood.showDetial();
			}
		}
	}
</script>

<style lang="stylus" rel="stylesheet/stylus">
	@import '../../common/stylus/mixin.styl'

	.goods
	  display: flex
	  position: absolute
	  width: 100%
	  top: 174px
	  bottom: 48px
	  overflow: hidden
	  .menu-wrapper
	  	flex: 0 0 80px
	  	width: 80px
	  	background: #f3f5f7
	  	.menu-item
	  	  padding: 20px 12px
	  	  display: block
	  	  max-height: 54px
	  	  color: #4d555d
	  	  line-height: 14px
	  	  font-size: 12px
	  	  &.current
	  	  	background: #fff
	  	  	font-weight: 700
	  	  .icon
	  	  	vertical-align: top
	  .foods-wrapper
	  	flex: 1
	  	overflow: hidden
	  	.foods-type
	  	  height: 26px
	  	  line-height: 26px
	  	  padding-left: 14px
	  	  background: #f3f5f7
	  	  color: rgb(147, 153, 159)
	  	  font-size: 12px
	  	  font-weight: 700
	  	  border-left: 2px solid #d9dde1
	  	.foods-type-wrapper
	  	  .food-item
	  	    display: flex
	  	    padding-bottom: 16px
	  	    margin: 18px 18px 0 18px
	  	    border-1px( rgba(7, 17, 27, 0.1) )
	  	    &:last-child
	  	      border-none()
	  	    .food-img
	  	  	  width: 58px
	  	  	  height: 58px
	  	    .food-information
	  	  	  overflow: hidden
	  	  	  margin-left: 10px
	  	  	  font-size: 10px
	  	  	  color: rgb(147, 153, 159)
	  	  	  .inf-title
	  	  	    line-height: 14px
	  	  	    font-size: 14px
	  	  	    color: rgb(7, 17,27)
	  	  	  .inf-description
	  	  	    line-height: 12px
	  	  	    margin: 8px 0
	  	  	    & > p
	  	  	      overflow: hidden
	  	  	      white-space: nowrap
	  	  	      text-overflow: ellipsis
	  	  	  .inf-tips
	  	  	    margin-bottom: 8px
	  	  	    line-height: 10px
	  	  	    .sell
	  	  	  	  display: inline-block
	  	  	  	  margin-right: 6px
	  	  	  .price
	  	  	    line-height: 24px
	  	  	    color: rgb(240, 20, 20)
	  	  	    font-size: 14px
	  	  	    font-weight: 700
	  	  	    .old-price
	  	  	  	  display: inline-block
	  	  	  	  position: relative
	  	  	  	  color: rgb(147, 153, 159)
	  	  	  	  font-size: 10px
	  	  	  	  line-height: 24px
	  	  	  	  &:after
	  	  	  	    display: block
	  	  	  	    position: absolute
	  	  	  	    top: 11px
	  	  	  	    content: " "
	  	  	  	    height: 1px
	  	  	  	    width: 100%
	  	  	  	    background: rgb(147, 153, 159)
	  	  	.carcontrol-wrapper
	  	  	    position: absolute
	  	  	    right: 0
	  	  	    bottom: 16px
</style>