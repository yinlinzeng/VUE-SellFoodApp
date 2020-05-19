<template>
    <div class="ratings-wrapper" ref="ratingWrapper">
      <div class="scroll-wrapper">
        <div class="score-wrapper border-1px">
          <div class="left-score">
            <div class="scoreNum">{{seller.score}}</div>
            <div class="title">综合评分</div>
            <div class="description">高于周边商家{{seller.rankRate}}%</div>
          </div>
          <div class="right-score">
            <ul>
              <li>
                <span class="score-des">服务态度</span>
                <star :size="36" :score="seller.serviceScore" class="star-score"></star>
                <span class="score-point">{{seller.serviceScore}}</span>
              </li>
              <li>
                <span class="score-des">食物评价</span>
                <star :size="36" :score="seller.foodScore" class="star-score"></star>
                <span class="score-point">{{seller.foodScore}}</span>
              </li>
              <li>
                <span class="score-des">送达时间</span>
                <span class="delivery-time">{{seller.deliveryTime}}分钟</span>
              </li>
            </ul>
          </div>
        </div>
        <!-- 以下评价模块的代码为第一次写入，后期增加食物列表后增加了ratingselect和split模块，未更新以下代码，留作学习参考 -->
        <div class="rating-content">
          <div class="filter-box border-1px">
            <div class="all" @click="_filter('all')" :class="{current: all}">
              全部<span class="filter-text">57</span>
            </div>
            <div class="success" @click="_filter('good')" :class="{current: good}">
              推荐<span class="filter-text">47</span>
            </div>
            <div class="fail" @click="_filter('bad')" :class="{current: bad}">
              吐槽<span class="filter-text">10</span>
            </div>
          </div>
          <div class="content-choice border-1px">
            <span class="chart-icon icon-check_circle" @click="contentOnly = !contentOnly" :class="{current: contentOnly === true}"></span><span class="choice-text" :class="{current: contentOnly === true}">只看有内容的评价</span>
          </div>
          <div class="content-list">
            <ul>
              <li class="list-detail border-1px" v-for="(item, index) in discussArr" :key="index">
                <img class="buyer-pic" :src="item.avatar">
                <div class="buyer-info">
                  <div class="buyer-name">{{item.username}}</div>
                  <div class="buyer-star">
                    <star :size="24" :score="item.score" class="rating-score"></star>
                    <div class="buyer-delivery-time">{{item.deliveryTime || 30}}分钟送达</div>
                  </div>
                </div>
                <div class="buyer-buy-time">{{formatDateTime(item.rateTime)}}</div>
                <div class="buyer-rating">{{item.text}}</div>
                <div class="attitude">
                  <div class="icon-attitude" :class="classMap[item.rateType]"></div>
                  <ul class="keyword-wrapper">
                    <li class="keyword" v-for="(item2, index2) in item.recommend" :key="index2" v-if="index2 < 3">{{item2}}</li>
                  </ul>
                </div>
              </li>
            </ul>
          </div>
        </div>
      </div>
    </div>
</template>

<script>
import star from './star'
import shopcart from './shopcart'
import BScroll from 'better-scroll'
import axios from 'axios'
import Vue from 'vue'
export default {
  name: 'ratings',
  props: {
    seller: {
      type: Object
    },
    selectFoods: {
      type: Array
    }
  },
  data () {
    return {
      contentOnly: false,
      ratings: [],
      all: true,
      good: false,
      bad: false,
      discussArr: [],
      goodArr: [],
      badArr: [],
      noEmptyArr: [],
      noEmptyGoodArr: [],
      noEmptyBadArr: []
    }
  },
  components: {
    star,
    shopcart
  },
  watch: {
    seller () {},
    contentOnly () {
      if (this.all) {
        this._filter('all')
      } else if (this.good) {
        this._filter('good')
      } else if (this.bad) {
        this._filter('bad')
      }
    }
  },
  created () {
    axios.get('/good/ratings').then(res => {
      if (res.data.code === 0) {
        this.ratings = res.data.data
        this.discussArr = this.ratings
        this._attitudeFilter()
        Vue.nextTick(() => {
          this._initscroll()
        })
      }
    })
    this.classMap = ['icon-thumb_up', 'icon-thumb_down']
  },
  methods: {
    _initscroll () {
      this.ratingScroll = new BScroll(this.$refs.ratingWrapper, {
        click: true
      })
    },
    _attitudeFilter () {
      this.discussArr.forEach(item => {
        if (item.rateType === 0) {
          if (item.text) {
            this.noEmptyGoodArr.push(item)
          }
          this.goodArr.push(item)
        } else if (item.rateType === 1) {
          if (item.text) {
            this.noEmptyBadArr.push(item)
          }
          this.badArr.push(item)
        }
        if (item.text) {
          this.noEmptyArr.push(item)
        }
      })
    },
    _filter (data) {
      if (data === 'all') {
        this.all = true
        this.good = false
        this.bad = false
        if (this.contentOnly) {
          this.discussArr = this.noEmptyArr
        } else {
          this.discussArr = this.ratings
        }
      } else if (data === 'good') {
        this.all = false
        this.good = true
        this.bad = false
        if (this.contentOnly) {
          this.discussArr = this.noEmptyGoodArr
        } else {
          this.discussArr = this.goodArr
        }
      } else if (data === 'bad') {
        this.all = false
        this.good = false
        this.bad = true
        if (this.contentOnly) {
          this.discussArr = this.noEmptyBadArr
        } else {
          this.discussArr = this.badArr
        }
      }
    },
    formatDateTime (inputTime) {
      var date = new Date(inputTime)
      var y = date.getFullYear()
      var m = date.getMonth() + 1
      m = m < 10 ? ('0' + m) : m
      var d = date.getDate()
      d = d < 10 ? ('0' + d) : d
      var h = date.getHours()
      h = h < 10 ? ('0' + h) : h
      var minute = date.getMinutes()
      var second = date.getSeconds()
      minute = minute < 10 ? ('0' + minute) : minute
      second = second < 10 ? ('0' + second) : second
      return y + '-' + m + '-' + d + ' ' + h + ':' + minute + ':' + second
    }
  }
}
</script>

<style lang="stylus" ref="stylesheet/stylus">
@import '../assets/stylus/index.styl'
.ratings-wrapper
  border-1px (rgba(7,17,27,.1))
  position absolute
  bottom 0
  top 174px
  left 0
  right 0
  overflow hidden
  background rgba(7,17,27,.1)
  .scroll-wrapper
    .score-wrapper
      width 100%
      display flex
      text-align center
      padding 18px 0px
      background #fff
      .left-score
        flex 3
        .scoreNum
          font-size 24px
          color rgb(255,153,0)
          line-height 28px
        .title
          font-size 12px
          color rgb(7,17,27)
          line-height 12px
          margin-top 6px
        .description
          font-size 10px
          color rgb(147,153,159)
          line-height 10px
          margin-top 8px
          padding-bottom 6px
      .right-score
        flex 5
        text-align left
        padding-left 24px
        border-left 1px solid rgba(7,17,27,.1)
        li
          margin-top 8px
          height 19px
          display flex
          &:first-child
            margin-top 0
          .score-des
            display inline-block
            font-size 12px
            color rgb(7,17,27)
            line-height 18px
          .star-score
            // border 1px solid black
            margin-left 12px
          .score-point
            margin-left 12px
            text-align center
            font-size 12px
            color rgb(255,153,0)
            line-height 18px
          .delivery-time
            font-size 12px
            color rgb(147,153,159)
            line-height 18px
            margin-left 12px

    .rating-content
      width 100%
      margin-top 12px
      background #fff
      .filter-box
        padding 18px 0
        margin 0 18px
        border-1px rgba(7,17,27,.1)
        color rgb(7,17,27)
        .all
          display inline-block
          width 60px
          font-size 14px
          line-height 32px
          text-align center
          background rgb(0,160,220)
          &.current
            color #ffffff
          .filter-text
            font-size 10px
            margin-left 4px
        .success
          display inline-block
          width 60px
          font-size 14px
          line-height 32px
          text-align center
          background rgba(0,160,220,.4)
          &.current
            color #ffffff
          .filter-text
            font-size 10px
            margin-left 4px
        .fail
          display inline-block
          width 60px
          font-size 14px
          line-height 32px
          text-align center
          background gray
          &.current
            color #ffffff
          .filter-text
            font-size 10px
            margin-left 4px
      .content-choice
        padding 18px
        border-1px (rgba(7,17,27,.1))
        .chart-icon
          font-size 24px
          color rgb(147,153,159)
          &.current
            color green
        .choice-text
          position relative
          top -5px
          left 3px
          font-size 12px
          line-height 24px
          color rgb(147,153,159)
          &.current
            color rgb(7,17,27)
      .content-list
        // margin-bottom 48px
        .list-detail
          border-1px rgba(7,17,27,.1)
          padding 18px 0
          margin 0 18px
          .buyer-pic
            width 28px
            height 28px
            display inline-block
            border-radius 100%
          .buyer-info
            // flex 1
            // height 28px
            display inline-block
            margin-left 12px
            // border 1px solid black
            .buyer-name
              font-size 10px
              line-height 12px
              display block
            .buyer-star
              margin-top 4px
              height 12px
              overflow hidden
              .rating-score
                display inline-block
                height 12px
                position relative
                top -4px
                // padding-top 4px
              .buyer-delivery-time
                display inline-block
                position relative
                top -4px
                font-size 10px
                corlor rgb(147,153,159)
                line-height 12px
                // padding-top 4px
          .buyer-buy-time
            float right
            font-size 10px
            color rgb(147,153,159)
          .buyer-rating
            margin-left 40px
            margin-top 6px
            font-size 12px
            color rgb(7,16,27)
            line-height 18px
          .attitude
            margin-top 8px
            margin-left 40px
            .icon-attitude
              display inline-block
              position relative
              top -5px
              font-size 12px
              line-height 16px
              &.icon-thumb_up
                color rgb(0,160,220)
              &.icon-thumb_down
                color rgb(183,187,191)
            .keyword-wrapper
              display inline-block
              .keyword
                display inline-block
                margin-left 8px
                border 1px solid rgba(7,17,27,.1)
                border-radius 2px
                font-size 9px
                color rgb(147,153,159)
                line-height 16px
                padding 0 6px
                width 55px
                text-overflow ellipsis
                overflow hidden
                white-space nowrap
                text-align center
</style>
