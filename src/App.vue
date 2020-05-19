<template>
  <div id="app">
    <div class="app-wrapper" :class="{blur: showIs}">
      <v-header :seller="seller" @adShow="adShow()"></v-header>
      <div class="tab border-1px">
        <div class="tab-item"><router-link to='goods'>商品</router-link></div>
        <div class="tab-item"><router-link to='ratings'>评价</router-link></div>
        <div class="tab-item"><router-link to='sellers'>商家</router-link></div>
      </div>
      <keep-alive>
          <router-view :seller="seller"></router-view>
      </keep-alive>
    </div>
    <announcement :seller="seller" v-show="showIs" @adShow="adShow()"></announcement>
  </div>
</template>

<script>
import header from './components/header'
import axios from 'axios'
import announcement from './components/announcement'

export default {
  name: 'App',
  data () {
    return {
      seller: {},
      showIs: false
    }
  },
  components: {
    'v-header': header,
    announcement
  },
  created () {
    axios.get('/good/seller').then(res => {
      if (res.data.code === 0) {
        this.seller = res.data.data
      }
    })
  },
  methods: {
    adShow () {
      this.showIs = !this.showIs
    }
  }
}
</script>

<style lang='stylus' ref='stylesheet/stylus'>
// 引入stylus公共方法
@import 'assets/stylus/index.styl'
*
  pading 0
  margin 0
#app
  .app-wrapper
    &.blur
      filter blur(10px)
    .tab
      display flex
      height 40px
      line-height 40px
      border-1px (rgba(7,17,27,0.1))
      z-index 1
      .tab-item
        flex 1
        text-align center
        font-size 14px
        color rgb(77,85,93)
        a
          width 100%
          height 100%
          display block
        a.active
          color rgb(240,20,20) !important
</style>
