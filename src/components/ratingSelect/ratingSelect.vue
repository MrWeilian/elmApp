<template>
  <div class="ratingSelect">
  	<div class="rating-type">
  		<span class="type1" @click="select(2,$event)" :class="{'active': selectType === 2}">{{ desc.all }}<span class="rat-count">{{ ratings.length }}</span></span>
  		<span class="type2" @click="select(0,$event)" :class="{'active': selectType === 0}">{{ desc.positive }}<span class="rat-count">{{ positive.length }}</span></span>
  		<span class="type3" @click="select(1,$event)" :class="{'active': selectType === 1}">{{ desc.negative }}<span class="rat-count">{{ negative.length }}</span></span>
  	</div>
  	<div class="switch" @click="toggleContent($event)">
  		<span class="icon-check_circle" :class="{'on':onlyContent}"></span>
  		<span class="switch-text">只看有内容的评价</span>
  	</div>
  </div>
</template>

<script>
	const POSITIVE = 0;
	const NEGATIVE = 1;
	const ALL =2;

	export default{
		props: {
			ratings:{
				type: Array,
				default() {
					return [];
				}
			},
			selectType:{
				type: Number,
				default: ALL
			},
			onlyContent:{
				type: Boolean,
				default: false
			},
			desc:{
				type: Object,
				default() {
					return {
						all: '全部',
						positive: '满意',
						negative: '不满意'
					}
				}
			}
		},
		methods: {
			select( type, event ) {

				if( !event._constructed ) return;
        
				this.$emit("selectTypeChange",type);

			},
			toggleContent(event) {
				if( !event._constructed ) return;

				var boolean = this.onlyContent;

				this.$emit("toggleContent",!boolean);	
			}
		},
		computed: {
			positive() {
				return this.ratings.filter( (rating) => {
					return rating.rateType === POSITIVE;
				} )
			},
			negative() {
				return this.ratings.filter( (rating) => {
					return rating.rateType === NEGATIVE;
				} )
			}
		}
	}
</script>

<style lang="stylus" rel="stylesheet/stylus">
  .ratingSelect
    background: #fff
    border-top: 1px solid rgba(7, 17, 27, 0.1)
    border-bottom: 1px solid rgba(7, 17, 27, 0.1)
    .rating-type
      font-size: 0
      .type1,.type2,.type3
        display: inline-block
        line-height: 16px
        padding: 8px 12px
        border-radius: 3px
        margin: 10px 8px 18px 0
        font-size: 12px
        background: rgba(0, 160, 220, 0.2)
        color: rgb(77, 85, 93)
        .rat-count
          display: inline-block
          vertical-aling: top
          margin: 0 0 0 6px
          font-size: 10px
      .type1
        &.active
      	  color: #fff
      	  background: rgb(0, 160, 220)
      .type2
        &.active
      	  color: #fff
      	  background: rgb(0, 160, 220)
      .type3
      	background: rgba(77, 85, 93, 0.2)
      	&.active
      	  color: #fff
      	  background: rgb(77, 85, 93)
    .switch
      color: rgb(147, 153, 159)
      .icon-check_circle
        display: inline-block
        vertical-align: top
        line-height: 24px
        margin-right: 4px
        font-size: 24px
        &.on
          color: #00c850
      .switch-text
      	display: inline-block
      	line-height: 24px
      	font-size: 12px
</style>