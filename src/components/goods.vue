<template>
  <div>
    <div id="goods">
      <div class="menu-wrapper" ref="menuWrapper">
        <div class="menu-content">
          <div v-for="(item,index) in goods" :key="index" ref="menuList" class="menu-item" @click="selectIndex(index, $event)" :class="{current : currentIndex === index}">
            <div class="text-wrapper border-1px">
              <span class="icon" v-show="item.type > 0" :class="classMap[item.type]"></span>
              <span class="text">{{item.name}}</span>
            </div>
          </div>
        </div>
      </div>
      <div class="foods-wrapper" ref="foodWrapper">
        <ul>
          <li v-for="(items, index) in goods" :key="index" class="foods-list-hook">
            <div class="items-title">{{items.name}}</div>
            <ul>
              <li v-for="(food,index) in items.foods" @click="selectFood(food, $event)" :key="index" class="item-content-wrapper">
                <div class="items-icon">
                  <img :src="food.icon">
                </div>
                <div class="items-content border-1px">
                  <div class="items-name">{{food.name}}</div>
                  <div class="items-description" v-show="food.description">{{food.description}}</div>
                  <div class="ratings">
                    <span>月售{{food.sellCount}}份</span>
                    <span style="margin-left:12px">好评率{{food.rating}}%</span>
                  </div>
                  <div class="prices">
                    <span class="price">{{food.price}}</span><span v-if="food.oldPrice" class="oldPrice" style="margin-left:8px">{{food.oldPrice}}</span>
                  </div>
                  <div class="cart-control-wrapper">
                    <cartcontrol :food="food"></cartcontrol>
                  </div>
                </div>
              </li>
            </ul>
          </li>
        </ul>
      </div>
      <shopcart :selectFoods="selectFoods" :deliveryPrice="seller.deliveryPrice" :minPrice="seller.minPrice"></shopcart>
    </div>
    <food :food="selectedFood" ref="food"></food>
  </div>

</template>

<script>
import axios from 'axios'
import Vue from 'vue'
import BScroll from 'better-scroll'
import shopcart from './shopcart'
import cartcontrol from './cartcontrol'
import food from './food'

export default {
  name: 'goods',
  props: {
    seller: {
      type: Object
    }
  },
  data () {
    return {
      goods: [],
      heightList: [],
      scrollY: 0,
      selectedFood: {}
    }
  },
  computed: {
    currentIndex () {
      for (let i = 0; i < this.heightList.length; i++) {
        let height1 = this.heightList[i]
        let height2 = this.heightList[i + 1]
        if (!height2 || (this.scrollY >= height1 && this.scrollY < height2)) {
          this._followScroll(i)
          return i
        }
      }
      return 0
    },
    selectFoods () {
      let foods = []
      if (this.goods.length !== 0) {
        this.goods.forEach(good => {
          good.foods.forEach(food => {
            if (food.count) {
              foods.push(food)
            }
          })
        })
      }
      return foods
    }
  },
  components: {
    shopcart,
    cartcontrol,
    food
  },
  created () {
    this.classMap = ['decrease', 'discount', 'special', 'invoice_1', 'guarantee']
    axios.get('/good/goods').then(res => {
      if (res.data.code === 0) {
        this.goods = res.data.data
        Vue.nextTick(() => {
          this._initscroll() // 给dom绑定scroll事件
          this._caculateHeight() // 计算foods的高度
        })
      }
    })
  },
  methods: {
    selectFood (food, event) {
      if (!event._constructed) {
        return
      }
      this.selectedFood = food
      this.$refs.food.show()
    },
    selectIndex ($index, $event) {
      // 忽略滚动事件
      if (!$event._constructed) {
        return
      }
      let foodList = this.$refs.foodWrapper.getElementsByClassName('foods-list-hook')
      this.foodScroll.scrollToElement(foodList[$index], 300)
    },
    _initscroll () {
      this.menuScroll = new BScroll(this.$refs.menuWrapper, {
        click: true
      })
      this.foodScroll = new BScroll(this.$refs.foodWrapper, {
        probeType: 3,
        click: true
      })
      this.foodScroll.on('scroll', (pos) => {
        if (pos.y <= 0) {
          this.scrollY = Math.abs(Math.round(pos.y)) + 1
        }
      })
    },
    _caculateHeight () {
      let foodList = this.$refs.foodWrapper.getElementsByClassName('foods-list-hook')
      let height = 0
      this.heightList.push(height)
      for (let i = 0; i < foodList.length; i++) {
        let item = foodList[i]
        height += item.clientHeight
        this.heightList.push(height)
      }
    },
    _followScroll (index) {
      let menuList = this.$refs.menuList
      this.menuScroll.scrollToElement(menuList[index], 300, 0, -100)
    }
  }
}
</script>

<style lang="stylus" ref="stylesheet/stylus">
@import '../assets/stylus/index.styl'
  #goods
    display flex
    position absolute
    top 174px
    bottom 48px
    left 0
    right 0
    width 100%
    overflow hidden
    .icon
      display inline-block
      vertical-align top
      width 14px
      height 14px
      background-size 14px 14px
      &.decrease
        bg-image('decrease_3')
      &.discount
        bg-image('discount_3')
      &.guarantee
        bg-image('guarantee_3')
      &.invoice
        bg-image('invoice_3')
      &.special
        bg-image('special_3')
    .menu-wrapper
      flex 0 0 80px
      width 80px
      background #f3f5f7
      .menu-item
        padding 0 12px
        font-size 0
        line-height 16px
        &.current
          position relative
          margin-top -1px
          background-color #ffffff
          &:after
            border-top 1px solid #ffffff
          .text
            font-size 12px
            line-height 14px
            font-weight 500
        .text-wrapper
          display table-cell
          vertical-align middle
          height 54px
          width 56px
          border-1px(rgba(7,17,27,0.1))
          .text
            font-size 12px
            line-height 14px
            font-weight 200
    .foods-wrapper
      flex 1
      background #fff
      .items-title
        height 26px
        padding-left 14px
        font-size 12px
        color rgb(147,153,159)
        line-height 26px
        background-color #f3f5f7
        border-left 4px solid #d9dde1
      .item-content-wrapper
        display flex
        margin 18px
        padding-bottom 18px
        position relative
        background #fff
        border-1px rgba(7,17,27,0.1)
        &:last-child
          border-1px(rgb(255,255,255))
          margin-bottom 0
        .items-icon
          flex 0 0 57px
          img
            width 57px
            height 57px
        .items-content
          flex 1
          padding-left 10px
          .items-name
            font-size 14px
            color rgb(7,17,27)
            line-height 14px
            margin-top 2px
          .items-description, .ratings
            margin-top 8px
            font-size 10px
            color rgb(147,153,159)
            line-height 16px
          .ratings
            span
              display inline-block
          .prices
            font-size 20px
            color rgb(240,20,20)
            font-weight 700
            line-height 24px
          .oldPrice
            font-size 10px
            vertical-align top
            color rgb(147,153,159)
            font-weight 700
            line-height 20px
            text-decoration line-through
          .cart-control-wrapper
            position absolute
            right 0
            bottom 12px
</style>
