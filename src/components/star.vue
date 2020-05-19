<template>
    <div class="star" :class="starType">
        <span class="star-item" v-for="(itemClass,index) in itemClasses" :key="index" :class="itemClass"></span>
    </div>
</template>

<script>
/* eslint-disable */
export default {
  props: {
    score: {
      type: Number
    },
    size: {
      type: Number
    }
  },
  computed: {
    itemClasses () {
        let result = []
        let numInt = Math.floor(this.score)
        let numFloar = Math.abs((this.score - numInt).toFixed(1))
        for (let i = 0; i < numInt; i++) {
            result.push('on')
        }
        if (numFloar >= 0.5) {
            result.push('half')
        }
        while (result.length < 5) {
            result.push('off')
        }
        return result
    },
    starType () {
        return 'star-' + this.size
    }
  }
}
</script>

<style lang="stylus" ref="stylesheet/stylus">
@import '../assets/stylus/index.styl'
  .star
    font-size: 0
    .star-item
      display: inline-block
      background-repeat: no-repeat
    &.star-48
      .star-item
        width: 20px
        height: 20px
        margin-right: 22px
        background-size: 20px 20px
        &:last-child
          margin-right: 0
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
        margin-right: 6px
        background-size: 15px 15px
        &:last-child
          margin-right: 0
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
        margin-right: 3px
        background-size: 10px 10px
        &:last-child
          margin-right: 0
        &.on
          bg-image('star24_on')
        &.half
          bg-image('star24_half')
        &.off
          bg-image('star24_off')
</style>
