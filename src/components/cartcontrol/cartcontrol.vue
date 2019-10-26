<template>
    <div class="cartcontrol">
        <transition>
            <div class="cart-dec" v-show="food.count > 0" @click.stop.prevent="decCart">
                <span class="iconfont">&#xe726;</span>
            </div>
        </transition>
        <div class="cart-count" v-show="food.count > 0">{{food.count}}</div>
        <div class="iconfont cart-add" @click.stop.prevent="addCart($event)">&#xe6ab;</div>
    </div>
</template>

<script>
import Vue from 'vue'
export default {
  name: 'cartcontrol',
  props: {
    food: {
      type: Object
    }
  },
  methods: {
    addCart (event) {
      if (!event._constructed) {
        return
      }
      if (!this.food.count) {
        Vue.set(this.food, 'count', 1)
      } else {
        this.food.count++
      }
      this.$emit('increment', event.target)
    },
    decCart () {
      if (this.food.count) {
        this.food.count--
      }
    }
  }
}
</script>

<style lang="stylus" scoped>
    .cartcontrol
        font-size 0
        .cart-add
            display inline-block
            padding 6px
            line-height 24px
            font-size 24px
            color rgb(0,160,220)
        .cart-count
            display inline-block
            vertical-align top
            width 12px
            padding-top 6px
            line-height 24px
            text-align center
            font-size 10px
            color rgb(147,153,159)
        .cart-dec
            display inline-block
            padding 6px
            transform: translate3d(0, 0, 0)
            opacity: 1
            .iconfont
                display inline-block
                color rgb(0,160,220)
                font-size 24px
                line-height 24px
                transition: all 0.5s
                transform: rotate(0)
            &.v-enter, &.v-leave-to
                transform: translate3d(24px,0,0)
                opacity 0
                .iconfont
                    transform: rotate(180deg)
            &.v-enter-active, &.v-leave-active
                transition all 0.5s linear
</style>
