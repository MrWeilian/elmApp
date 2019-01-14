<template>
  <div class="app-wrapper">
    <v-header :seller="seller"></v-header>
    <div class="tab border-1px">
      <div class="tab-item">
        <router-link to="/goods">商品</router-link>
      </div>
      <div class="tab-item">
        <router-link to="/ratings">评价</router-link>
      </div>
      <div class="tab-item">
        <router-link to="/seller">商家</router-link>
      </div>
    </div>
    <keep-alive>
      <router-view :seller="seller" @foodSelected="foodSelected" @sendBall="sendBall"></router-view>
    </keep-alive>
    <shopcar :sellerMinprice="seller.minPrice" :sellerDeliveryPrice="seller.deliveryPrice" :selectFoods="selectFoods" :thisBall="thisBall"></shopcar>
  </div>
</template>

<script>
  import header from './components/hearder/header.vue'
  import shopcar from './components/shopcar/shopcar.vue'
  import {urlParse} from './common/js/util.js'

  const ErrOk = 0;

  export default {
    data() {
      return {
        seller: {
          id: ( () => {
            let urlObj = urlParse();
            console.log(urlObj)
            return urlObj.id;
          } )()
        },
        selectFoods: [],
        thisBall: {}
      }
    },
    created() {
      this.$http.get('/api/seller?'+this.seller.id).then( (response) => {
        response = response.body;
        if( response.errno === ErrOk ){
          this.seller = Object.assign({},this.seller,response.data);
        }
      } )
    },
    methods: {
      foodSelected(data) {
        this.selectFoods = data;
      },
      sendBall(el){
        this.thisBall = el;
      }
    },
    components: {
      'v-header': header,
      shopcar
    }
  }

</script>

<style lang="stylus" rel="stylesheet/stylus">
  @import './common/stylus/mixin.styl'
  
  .app-wrapper
    .tab
      display: flex
      height: 40px
      line-height: 40px
      z-index: 8
      border-1px( rgba(7, 17, 27, 0.1) )
      .tab-item
        flex: 1
        text-align: center
        font-size: 14px
        & > a
          display: block
          color: rgb(77, 85, 93)
        .router-link-active
          color: rgb(240, 20, 20)
          
  .router-change-enter-active,.router-change-leave-active
    transtion: all .5s ease
  .router-change-enter,.router-change-leave-to
    transform: translate3d(100, 0, 0)
</style>
