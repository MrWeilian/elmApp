<template>
  <div class="star" :class="starType">
  	<span class="star-item" v-for="itemC in itemClass" :class="itemC"></span>
  </div>
</template>

<script>
	export default{
		props: ['size','score'],
		computed: {
			starType () {
				return 'star-'+this.size;
			},
			itemClass () {
        // 星星展示
        const score_on = 'on';
        const score_half = 'half';
        const score_off = 'off';

        const full_score = 5;
        let itemClass = [],
            score = Math.floor( this.score* 2 ) / 2,
            hasDecimal = score%1 !== 0,
            integer = Math.floor( score );

        for( let i = 0; i < integer; i++ ){
          itemClass.push(score_on);
        }
        if( hasDecimal ){
          itemClass.push(score_half); 
        }
        while( itemClass.length < full_score ){
          itemClass.push(score_off);
        }
        // console.log(itemClass)
        return itemClass;
			}
		}
	}
</script>

<style lang="stylus" rel="stylesheet/stylus">
  @import '../../common/stylus/mixin.styl'

  .star
    margin: 16px auto 28px;
    display: flex
    justify-content: center
    .star-item
      display: inline-block
      background-repeat: no-repeat
      margin: 0 8px
    &.star-48
      .star-item
        width: 20px
        height: 20px
        background-size: 20px 20px
        &.on
          bg-image('star48_on')
        &.half
          bg-image('star48_half')
        &.off
          bg-image('star48_off')
    &.star-36
      .star-item
        width: 15px
        height: 15px
        background-size: 15px 15px
        &.on
          bg-image('star36_on')
        &.half
          bg-image('star36_half')
        &.off
          bg-image('star36_off')
    &.star-24
      .star-item
        width: 10px
        height: 10px
        background-size: 10px 10px
        &.on
          bg-image('star24_on')
        &.half
          bg-image('star24_half')
        &.off
          bg-image('star24_off') 
</style>