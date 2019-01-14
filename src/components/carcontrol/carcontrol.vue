<template>
  <div class="carcontrol">
  	<transition name="reduce-trans">
  		<div class="reduce icon-remove_circle_outline" v-show="foodsItem.count > 0" @click="reduceShop($event)"></div>
  	</transition>
  	<div class="count" v-show="foodsItem.count > 0">{{ foodsItem.count }}</div>
  	<div class="add icon-add_circle" ref="addxy" @click="addShop($event)"></div>
  </div>
</template>

<script>
	import Vue from 'vue'

	export default{
		props: ['foodsItem'],
		data() {
			return{
				
			}
		},
		/*created() {
			console.log( this.foodsItem )
		},*/
		methods: {
			addShop(event) {
				event.stopPropagation();
				if( !event._constructed ) return;
				
				if( !this.foodsItem.count ){
					Vue.set( this.foodsItem, 'count', 1 );
				}else{
					this.foodsItem.count ++;
				}
				this.$emit("foodsItem", this.foodsItem)

				// console.log(event.target)

				this.$emit("ballDom", {ball:event.target})
			},
			reduceShop(event){
				event.stopPropagation();
				if( !event._constructed ) return;

				if( this.foodsItem.count ) this.foodsItem.count --;
			}
		}
	}
</script>

<style lang="stylus" rel="stylesheet/stylus">
  .carcontrol
    font-size: 0
  	.reduce
  	  display: inline-block
  	  vertical-align: middle
  	  padding: 6px;
  	  color: rgb(0, 160, 220)
  	  font-size: 24px
  	  font-weight: 700
  	.count
  	  display: inline-block
  	  vertical-align: middle
  	  line-height: 24px
  	  color: rgb(147, 153, 159)
  	  margin: 0 6px
  	  font-size: 10px
  	  font-weight: 700
  	.add
  	  display: inline-block
  	  vertical-align: middle
  	  padding: 6px;
  	  font-size: 24px
  	  font-weight: 100
  	  color: rgb(0, 160, 220)
  	  

  .reduce-trans-enter-active, .reduce-trans-leave-active
  	transition: all .3s ease;
  .reduce-trans-enter, .reduce-trans-leave-to
  	transform: translate3d(20px, 0, 0) rotate(360deg)
  	opacity: 0
  	
</style>