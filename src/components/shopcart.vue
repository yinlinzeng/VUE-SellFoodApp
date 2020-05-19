<template>
    <div class="shopcart">
        <div class="content" @click="listToggle">
            <div class="chart-icon-wrapper" @click="goList">
                <div class="chart-icon icon-shopping_cart" :class="{noZChart: totalCount != 0}"></div>
                <div class="total-count" v-show="totalCount != 0">{{totalCount}}</div>
            </div>
            <div class="deliver-fee" @click="goList">
                <div class="price border-1px-right" :class="{noZPrice: totalPrice != 0}">￥{{totalPrice}}</div>
                <div class="deliveryPrice">另需配送费￥{{deliveryPrice}}元</div>
            </div>
            <div class="deliver-base" :class="{ok: totalPrice >= minPrice}" @click="pay">{{inform}}</div>
        </div>
        <div class="cart-list-wrapper" v-show="showList">
            <div class="cart-gray" @click="goList"></div>
            <div class="cart-list">
                <div class="cart-list-header">
                    <span class="cart-title">购物车</span>
                    <span class="clear" @click="clearAll">清空</span>
                </div>
                <div class="list-scroll-wrapper" ref="cartScroll">
                  <ul class="food-all">
                    <li class="food-item" v-for="food in selectFoods" :key="food.id">
                      <span class="food-title">{{food.name}}</span>
                      <span class="food-price">￥{{food.price * food.count}}</span>
                      <cartcontrol class="cart-control" :food="food"></cartcontrol>
                    </li>
                  </ul>

                </div>
            </div>
        </div>
    </div>
</template>

<script>
import cartcontrol from './cartcontrol'
import BScroll from 'better-scroll'

export default {
  name: 'shopcart',
  data () {
    return {
      fold: true
    }
  },
  props: {
    deliveryPrice: {
      type: Number
    },
    minPrice: {
      type: Number
    },
    selectFoods: {
      type: Array,
      default () {
        return []
      }
    }
  },
  computed: {
    totalCount () {
      let totalCount = 0
      this.selectFoods.forEach((food) => {
        totalCount += food.count
      })
      return totalCount
    },
    totalPrice () {
      let totalPriceNum = 0
      this.selectFoods.forEach((food) => {
        totalPriceNum += food.price * food.count
      })
      return totalPriceNum
    },
    inform () {
      if (this.totalPrice === 0) {
        return `￥${this.minPrice}`
      } else if (this.totalPrice > 0 && this.totalPrice < this.minPrice) {
        this._initScroll()
        return `还差￥${this.minPrice - this.totalPrice}起送`
      } else {
        return `去结算`
      }
    },
    showList: {
      get: function () {
        if (!this.totalCount) {
          // eslint-disable-next-line
          this.fold = true
          return false
        }
        return !this.fold
      },
      set: function (a) {
        // 占位，如果不写会报错
      }
    }
  },
  components: {
    cartcontrol
  },
  methods: {
    _initScroll () {
      this.shopcartScroll = new BScroll(this.$refs.cartScroll, {
        click: true
      })
    },
    goList () {
      if (!this.selectFoods.length) {
        return
      }
      this.fold = !this.fold
    },
    clearAll () {
      this.selectFoods.forEach((food) => {
        food.count = 0
      })
    },
    listToggle () {
      if (!this.selectFoods.length) {
        return
      }
      this.showList = !this.showList
      if (this.showList) {
        this.$nextTick(() => {
          if (!this.shopcartScroll) {
            this._initScroll()
          } else {
            this.shopcartScroll.refresh()
          }
        })
      }
    },
    pay () {
      if (this.totalPrice >= this.minPrice) {
        alert(`您已成功支付${this.totalPrice}元，欢迎再次订购`)
        this.clearAll()
      }
    }
  }
}
</script>

<style lang="stylus" ref="stylesheet/stylus">
@import '../assets/stylus/index.styl'
.shopcart
    position fixed
    width 100%
    height 48px
    bottom 0
    left 0
    z-index 300
    font-size 0
    .content
        width 100%
        display flex
        background #141d27
        .chart-icon-wrapper
            flex 0 0 80px
            position relative
            .chart-icon
                position relative
                width 44px
                height 44px
                border-radius 44px
                border 6px solid #141d27
                background-color #2b333b
                margin-left 18px
                margin-top -10px
                font-size 24px
                color rgba(255,255,255,.4)
                line-height 44px
                text-align center
                &.noZChart
                    background-color #00a0dc
                    color #ffffff
            .total-count
                position absolute
                right 0
                top -6px
                text-align center
                background-color red
                color #ffffff
                width 24px
                height 16px
                font-size 8px
                font-weight 700
                line-height 16px
                box-shadow 0 4px 8px 0 rgba(0,0,0,0.4)
                border-radius 16px
        .deliver-fee
            flex 1
            padding 12px 0 12px 12px
            height 24px
            .price
                display inline-block
                line-height 24px
                color rgba(255,255,255,.3)
                border-1px-right (rgba(255,255,255,.4))
                padding-right 12px
                font-size 16px
                &.noZPrice
                    color #ffffff
            .deliveryPrice
                display inline-block
                padding-left 12px
                font-size 12px
                color rgba(255,255,255,.4)
                line-height 24px
                font-weight 700
        .deliver-base
            flex 0 0 105px
            padding 0 8px
            line-height 48px
            font-size 12px
            text-align center
            color rgba(255,255,255,.1)
            font-weight 700
            background-color #2b333b
            &.ok
                background-color green
                color #ffffff
.cart-list-wrapper
    position fixed
    top 0
    bottom 48px
    width 100%
    display flex
    flex-direction column
    z-index -1
    .cart-gray
        flex 1
        background-color rgba(7,17,27,.6)
    .cart-list
        width 100%
        position fixed
        bottom 48px
        left 0
        background-color #ffffff
        // overflow auto
        max-height 217px
        .cart-list-header
            height 40px
            line-height 40px
            padding 0 18px
            border-1px(rgba(7,17,27,0.1))
            background-color #f3f5f7
            .cart-title
                font-size 14px
                font-weight 200
                color rgb(7,17,27)
            .clear
                position absolute
                right 18px
                font-size 12px
                color rgb(0,160,220)
        .list-scroll-wrapper
            position relative
            width 100%
            height 100%
            max-height 177px
            // bottom 48px
            overflow hidden
            .food-all
                width 100%
                .food-item
                    width 100%
                    height 48px
                    line-height 48px
                    margin 0 18px
                    border-1px(rgba(7,17,27,.1))
                    .food-title
                        font-size 14px
                        color rgb(7,17,27)
                    .food-price
                        position absolute
                        right 120px
                        font-size 12px
                        color rgb(240,20,20)
                        font-weight 500
                    .cart-control
                        position absolute
                        right 18px
                        top 6px
                        display inline-block
</style>
