<template>
  <div class="seller-wrapper" ref="sellerWrapper">
    <div class="scroll-wrapper">
      <div class="seller-base border-1px">
        <div class="seller-name">{{seller.name}}</div>
        <div class="score-box border-1px">
          <star :size="36" :score="seller.score" class="star"></star>
          <span class="text first">({{seller.ratingCount}})</span>
          <span class="text">月售{{seller.sellCount}}单</span>
        </div>
        <div class="heart-box" @click="toggleFavorite">
          <div class="heart-icon icon-favorite" :class="{'ok':favorite}"></div>
          <p class="heart-text">{{favoriteText}}</p>
        </div>
        <div class="price-box border-1px">
          <div class="base-price-box">
            <div class="price-text">起送价</div>
            <div class="base-price">
              <span class="base-price-num">{{seller.minPrice}}</span><span>元</span>
            </div>
          </div>
          <div class="base-price-box">
            <div class="price-text">商家配送</div>
            <div class="base-price">
              <span class="base-price-num">{{seller.deliveryPrice}}</span><span>元</span>
            </div>
          </div>
          <div class="base-price-box">
            <div class="price-text">平均配送时间</div>
            <div class="base-price">
              <span class="base-price-num">{{seller.deliveryTime}}</span><span>分钟</span>
            </div>
          </div>
        </div>
      </div>
      <div class="bulletin-box border-1px">
        <div class="bulletin-title">公告与活动</div>
        <div class="bulletin-des">{{seller.bulletin}}</div>
        <ul class="bulletin-list">
          <li class="bulletin-detail" v-for="(item, index) in seller.supports" :key="index">
            <span class="detail-icon bg-image" :class="classMap[item.type]"></span>
            <span class="detail-text">{{item.description}}</span>
          </li>
        </ul>
      </div>
      <div class="seller-image-box border-1px">
        <div class="seller-image-title">商家实景</div>
        <div class="image-scroll-wrapper" ref="imageScrollWrapper">
          <ul class="seller-image-list" v-if="seller" :style="computedWidth(seller.pics.length)">
            <li class="seller-image-detail" v-for="(item, index) in seller.pics" :key="index">
              <img class="seller-image" :src="item"/>
            </li>
          </ul>
        </div>
      </div>
      <div class="seller-info-box border-1px">
        <div class="seller-info-title">商家信息</div>
        <ul>
          <li class="seller-info-detail" v-for="(item, index) in seller.infos" :key="index">{{item}}</li>
        </ul>
      </div>
    </div>
  </div>
</template>

<script>
import star from './star'
import BScroll from 'better-scroll'
import Vue from 'vue'
import shopcart from './shopcart'

export default {
  name: 'sellers',
  props: {
    seller: {
      type: Object
    }
  },
  data () {
    return {
      favorite: true
    }
  },
  components: {
    star,
    shopcart
  },
  computed: {
    favoriteText () {
      return this.favorite ? '已收藏' : '收藏'
    }
  },
  created () {
    this.classMap = ['decrease', 'discount', 'special', 'invoice', 'guarantee']
    Vue.nextTick(() => {
      this._initscroll()
    })
  },
  methods: {
    computedWidth (length) {
      return `width: ${130 * length - 10}px`
    },
    _initscroll () {
      this.imageScroll = new BScroll(this.$refs.imageScrollWrapper, {
        click: true,
        scrollX: true,
        scrollY: false
      })
      this.sellerScroll = new BScroll(this.$refs.sellerWrapper, {
        click: true
      })
    },
    toggleFavorite () {
      if (event._constructed) {
        this.favorite = !this.favorite
      }
    }
  }
}
</script>

<style lang="stylus" ref="stylesheet/stylus">
@import '../assets/stylus/index.styl'
.seller-wrapper
  position absolute
  bottom 0
  top 174px
  left 0
  right 0
  overflow hidden
  background-color rgba(7,17,27,.1)
  .seller-base
    background #fff
    width 100%
    height 100%
    position relative
    border-1px rgba(7,17,27,.1)
    padding-top 18px
    .seller-name
      display block
      font-size 14px
      color rgb(7,17,27)
      line-height 14px
      padding 0 18px
    .score-box
      margin 8px 18px 0
      border-1px rgba(7,17,27,.1)
      padding-bottom 18px
      .star
        display: inline-block
        margin-right: 8px
        vertical-align: top
      .text
        margin-left 12px
        display inline-block
        font-size 10px
        color rgb(77,85,93)
        line-height 18px
        vertical-align top
        &.first
          margin-left 0px
    .heart-box
      position absolute
      right 18px
      top 18px
      text-align center
      width 50px
      .heart-icon
        font-size 24px
        color #d4d6d9
        line-height 24px
        &.ok
          color rgb(240,20,20)
      .heart-text
        font-size 10px
        color rgb(77,85,93)
        line-height 10px
        margin-top 4px
    .price-box
      padding 18px
      border-1px rgba(7,17,27,.1)
      display flex
      .base-price-box
        flex 1
        border-left 1px solid rgba(7,17,27,.1)
        &:first-child
          border-left 0
        .price-text
          text-align center
          font-size 10px
          color rgb(147,153,159)
          line-height 10px
        .base-price
          text-align center
          font-size 10px
          margin-top 4px
          .base-price-num
            font-size 24px
            font-weight 200
            color rgb(7,17,27)
            line-height 24px

  .bulletin-box
    padding 18px 18px 0
    border-1px rgba(7,17,27,.1)
    background #fff
    margin-top 16px
    .bulletin-title
      font-size 14px
      color rgb(7,17,27)
      line-height 14px
    .bulletin-des
      padding 8px 12px 16px
      font-size 12px
      font-weight 200
      color rgb(240,20,20)
      line-height 24px
    .bulletin-list
      .bulletin-detail
        border-top 1px solid rgba(7,17,27,.1)
        padding 16px 12px
        font-size 0
        .detail-icon
          display inline-block
          vertical-align bottom
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
        .detail-text
          margin-left 6px
          font-size 12px
          font-weight 200
          color rgb(7,17,27)
          line-height 16px
  .seller-image-box
    padding 18px
    border-1px rgba(7,17,27,.1)
    // overflow hidden
    background #fff
    margin-top 16px
    .seller-image-title
      font-size 14px
      color rgb(7,17,27)
      line-height 14px
      // margin-left 18px
    .image-scroll-wrapper
      overflow hidden
      width 100%
      .seller-image-list
        margin-top 12px
        display inline-block
        .seller-image-detail
          width 120px
          height 90px
          display inline-block
          margin-left 6px
          .seller-image
            width 120px
            height 90px
  .seller-info-box
    padding 18px 18px 0 18px
    border-1px rgba(7,17,27,.1)
    background #fff
    margin-top 16px
    // margin-bottom 48px
    .seller-info-title
      font-size 14px
      color rgb(7,17,27)
      line-height 14px
      padding-bottom 12px
    ul
      .seller-info-detail
        padding 16px 12px
        font-size 12px
        font-weight 200
        color rgb(7,17,27)
        line-height 16px
        border-top 1px solid rgba(7,17,27,.1)
</style>
