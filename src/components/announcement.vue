<template>
    <div id="announcement">
        <div class="sellerName">{{seller.name}}</div>
        <star :size="48" :score="seller.score" class="announcement-star"></star>
        <div class="content">
          <div class="list-title">
            <span class="left-line"></span>
            <span class="second-title">优惠信息</span>
            <span class="right-line"></span>
          </div>
          <div class="support-list">
            <ul>
              <li v-for="(item, index) in seller.supports" :key="index" class="support-detail">
                <span class="icon bg-image" :class="classMap[item.type]"></span>
                <span class="support-text">{{item.description}}</span>
              </li>
            </ul>
          </div>
          <div class="list-title">
            <span class="left-line"></span>
            <span class="second-title">商家公告</span>
            <span class="right-line"></span>
          </div>
          <div class="description">{{seller.bulletin}}</div>
        </div>
        <div class="close-wrapper" @click="$emit('adShow')">
          <div class="close-btn icon-close"></div>
        </div>
    </div>
</template>

<script>
import star from './star.vue'
export default {
  props: {
    seller: {
      type: Object
    }
  },
  components: {
    star
  },
  created () {
    this.classMap = ['decrease', 'discount', 'special', 'invoice', 'guarantee']
  }
}
</script>

<style lang="stylus" ref="stylesheet/stylus">
@import '../assets/stylus/index.styl'
#announcement
  position absolute
  left 0
  top 0
  z-index 999
  width 100vw
  height 100vh
  background rgba(7,17,27,.6)
  color #ffffff
  .sellerName
    position absolute
    width 100%
    height 16px
    line-height 16px
    top 64px
    left 0
    font-size 16px
    font-weight 700
    text-align center
  .announcement-star
    position absolute
    display flex
    top 96px
    left 0px
    display inline-block
    text-align center
    width 100%
    height 24px
    margin-left -5px
  .content
    position absolute
    top 148px
    left 0
    width 100%
    text-align center
    .list-title
      display flex
      width 100%
      text-align center
      .left-line, .right-line
        flex-grow 1
        height 2px
        background rgba(255,255,255,.2)
      .left-line
        margin-left 36px
        margin-top 7px
      .right-line
        margin-right 36px
        margin-top 7px
      .second-title
        font-size 14px
        font-weight 700
        color rgb(255,255,255)
        line-height 14px
        padding 0 12px
    .support-list
      margin-top 24px
      margin-bottom 28px
      text-align left
      padding 0 48px
      .support-detail
        margin-top 12px
        height 16px
        line-height 16px
        margin-left 12px
        .support-text
          font-size 12px
          font-weight 200
          line-height 12px
          margin-left 6
        .icon
          display inline-block
          vertical-align top
          width 16px
          height 16px
          background-size 16px 16px
          &.decrease
            bg-image('decrease_1')
          &.discount
            bg-image('discount_1')
          &.guarantee
            bg-image('guarantee_1')
          &.invoice
            bg-image('invoice_1')
          &.special
            bg-image('special_1')
    .description
      margin-top 24px
      padding 0 48px
      font-size 12px
      font-weight 200
      line-height 24px
      text-align left
  .close-wrapper
    position absolute
    bottom 32px
    text-align center
    width 100%
    height 32px
    .close-btn
      display inline-block
      font-size 32px
      width 40px
      color rgba(255,255,255,.5)
</style>
