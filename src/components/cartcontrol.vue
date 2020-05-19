<template>
    <div id="cart-control">
      <transition name="move">
        <span class="decrease icon-remove_circle_outline" v-show="food.count > 0" @click.stop.prevent="remove"></span>
      </transition>
      <span class="count" v-show="food.count > 0">{{food.count}}</span>
      <span class="increase icon-add_circle" @click.stop.prevent="add"></span>
    </div>
</template>

<script>
import Vue from 'vue'
export default {
  props: {
    food: {
      type: Object
    }
  },
  methods: {
    add (event) {
      if (event._constructed) { // 判断是否为bscroll触发的事件，如果为原生dom触发的事件则直接返回
        if (!this.food.count) {
          Vue.set(this.food, 'count')
          this.food.count = 1
        } else {
          this.food.count++
        }
      }
    },
    remove (event) {
      if (event._constructed) {
        if (this.food.count === 0) {
          return
        }
        this.food.count--
      }
    }
  }
}
</script>

<style lang="stylus" ref="stylesheet/stylus">
@import '../assets/stylus/index.styl'

#cart-control
  font-size 0
  .icon-remove_circle_outline, .icon-add_circle
    display inline-block
    width 24px
    height 24px
    line-height 24px
    font-size 24px
  .decrease, .increase
    color #00a0dc
    font-size 24px
    padding 6px
    display inline-block
    line-height 24px
  .move-enter-active, .move-leave-active
    transition all 0.4s linear
  .move-enter
    opacity 0
    transform translate3d(24px, 0, 0)
  .move-enter-to
    opacity 1
    transform rotate(180deg)
  .move-leave
    opacity 1
  .move-leave-to
    opacity 0
    transform translate3d(24px, 0, 0) rotate(180deg)
  .count
    display inline-block
    text-align center
    width 12px
    font-size 10px
    color rgb(147,153,159)
    line-height 24px
    padding-top 6px
    vertical-align top
</style>
