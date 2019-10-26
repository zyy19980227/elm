<template>
  <div>
    <div class="shopcart">
     <div class="content">
         <div class="content-left">
             <div class="logo-wrapper" @click="openlist">
                 <div class="logo" :class="{'highlight': totalcount > 0}">
                     <div class="iconfont icon-shopcart" :class="{'highlight': totalcount > 0}">&#xe605;</div>
                 </div>
                 <div class="num" v-show="totalcount">{{totalcount}}</div>
             </div>
             <div class="price" :class="{'highlight': totalcount > 0}">￥{{totalprice}}</div>
             <div class="desc">另需配送费￥{{deliveryprice}}</div>
         </div>
         <div class="content-right" @click="pay">
             <div class="pay" :class="{'enough': payClass}">{{payDesc}}</div>
         </div>
     </div>
     <div class="ball-container">
        <div v-for="(ball,index) in balls" :key="index" >
          <transition name="drop"
            @before-enter="handleBeforeEnter"
            @enter="handleEnter"
            @after-enter="handleAfterEnter">
            <div class="ball" v-show="ball.show">
              <div class="inner inner-hook"></div>
            </div>
          </transition>
        </div>
     </div>
     <transition name="fold">
      <div class="shopcart-list" v-show="listShow">
        <div class="list-header">
          <h1 class="title">购物车</h1>
          <span class="empty" @click="empty">清空</span>
        </div>
        <div class="list-content" ref="listcontent">
          <ul>
            <li class="food" v-for="(food,index) in selectFoods" :key="index">
              <span class="name">{{food.name}}</span>
              <div class="price">
                <span>￥{{food.price * food.count}}</span>
              </div>
              <div class="control-wrapper">
                <cartcontrol :food='food'></cartcontrol>
              </div>
            </li>
          </ul>
        </div>
      </div>
     </transition>
    </div>
    <fade>
      <div class="listmask" v-show="listShow" @click="openlist"></div>
    </fade>
  </div>
</template>

<script>
import cartcontrol from '../cartcontrol/cartcontrol'
import fade from '../fade/fade'
import BScroll from 'better-scroll'
export default {
  name: 'Shopcart',
  props: { // 顺序必须和传过来的顺序对应，否则无法接受！！！！
    selectFoods: {
      type: Array,
      default () {
        return []
      }
    },
    deliveryprice: {
      type: Number,
      default: 0
    },
    minprice: {
      type: Number,
      default: 0
    }
  },
  components: {
    cartcontrol,
    fade
  },
  data () {
    return {
      balls: [
        {show: false},
        {show: false},
        {show: false},
        {show: false},
        {show: false},
        {show: false}
      ],
      dropBalls: [],
      fold: true
    }
  },
  computed: {
    totalprice () {
      let total = 0
      this.selectFoods.forEach((food) => {
        total += food.price * food.count
      })
      return total
    },
    totalcount () {
      let count = 0
      this.selectFoods.forEach((food) => {
        count += food.count
      })
      return count
    },
    payDesc () {
      if (this.totalprice === 0) {
        return `￥${this.minprice}起送`
      } else if (this.totalprice < this.minprice) {
        let diff = this.minprice - this.totalprice
        return `还差￥${diff}起送`
      } else {
        return '去结算'
      }
    },
    payClass () {
      if (this.totalprice >= this.minprice) {
        return 'enough'
      }
    },
    listShow: {
      get () {
        if (!this.totalcount) {
          return false
        }
        return !this.fold
      },
      set () {
        if (!this.totalcount) {
          this.fold = false
        }
      }
    }
  },
  methods: {
    drop (el) {
      for (let i = 0; i < this.balls.length; i++) { // 遍历这5个小球
        let ball = this.balls[i]
        if (!ball.show) { // 当小球显示状态为隐藏时
          ball.show = true // 将这个小球的显示状态设置为true
          ball.el = el // 将cartControl传过来的对象挂载到ball的el属性上
          this.dropBalls.push(ball) // 将这个小球放入到dropBalls数组中
          return
        }
      }
    },
    handleBeforeEnter (el) {
      let count = this.balls.length
      while (count--) {
        let ball = this.balls[count]
        if (ball.show) {
          let rect = ball.el.getBoundingClientRect() // getBoundingClientRect()获取小球相对于视窗的位置，屏幕左上角坐标为0，0
          let x = rect.left - 32 // 小球x方向位移= 小球距离屏幕左侧的距离-外层盒子距离水平的距离
          let y = -(window.innerHeight - rect.top - 22) // 负数，因为是从左上角向下
          el.style.display = ''
          el.style.webkitTransform = `translate3d(0,${y}px,0)` // 设置外层盒子，即小球垂直方向的位移
          el.style.transform = `translate3d(0,${y}px,0)`
          let inner = el.getElementsByClassName('inner-hook')[0]
          inner.style.webkitTransform = `translate3d(${x}px,0,0)` // 设置内层盒子，即小球水平方向的距离
          inner.style.transform = `translate3d(${x}px,0,0)`
        }
      }
    },
    handleEnter (el, done) {
      /* eslint-disable no-unused-vars */
      // 触发浏览器重绘
      let rf = el.offsetHeight
      this.$nextTick(() => { // 让动画效果异步执行,提高性能
        el.style.webkitTransform = 'translate3d(0, 0, 0)'// 设置小球掉落后最终的位置
        el.style.transform = 'translate3d(0, 0, 0)'
        let inner = el.getElementsByClassName('inner-hook')[0]
        inner.style.webkitTransform = 'translate3d(0, 0, 0)'
        inner.style.transform = 'translate3d(0, 0, 0)'
        el.addEventListener('transitionend', done)
      })
    },
    handleAfterEnter (el) {
      let ball = this.dropBalls.shift() // 完成一次动画就删除一个dropBalls的小球
      if (ball) {
        ball.show = false
        el.style.display = 'none'
      }
    },
    openlist () {
      if (!this.totalcount) {
        return
      }
      this.fold = !this.fold
      if (!this.fold) {
        this.$nextTick(() => {
          if (!this.scroll) {
            this.scroll = new BScroll(this.$refs.listcontent, {
              click: true
            })
          } else {
            this.scroll.refresh()
          }
        })
      }
    },
    empty () {
      this.selectFoods.forEach((food) => {
        food.count = 0
      })
      this.fold = true
    },
    pay () {
      if (this.totalprice < this.minprice) {
        return
      }
      window.alert(`支付${this.totalprice}￥`)
    }
  }
}
</script>

<style lang="stylus" scoped>
    .shopcart
        position fixed
        left 0
        bottom 0
        z-index 50
        width 100%
        height 48px
        .content
            display flex
            background #141d27
            font-size 0
            color rgba(255,255,255,0.4)
            .content-left
                flex 1
                .logo-wrapper
                    display inline-block
                    position relative
                    top -10px
                    margin 0 12px
                    padding  6px
                    width 56px
                    height 56px
                    box-sizing border-box
                    vertical-align top
                    border-radius 50%
                    background #141d27
                    .logo
                        width 100%
                        height 100%
                        border-radius 50%
                        text-align center
                        background #2b343c
                        &.highlight
                            background rgb(0,160,220)
                        .icon-shopcart
                            line-height 44px
                            font-size 24px
                            color #80858a
                            &.highlight
                                color #fff
                    .num
                        position absolute
                        top 0
                        left 35px
                        width 24px
                        height 16px
                        line-height 16px
                        text-align center
                        border-radius 16px
                        font-size 9px
                        font-weight 700
                        color #fff
                        background rgb(240,20,20)
                        box-shadow  0 4px 8px 0 rgba(0,0,0,0.4)
                .price
                    display inline-block
                    vertical-align top
                    line-height 24px
                    margin-top 12px
                    padding-right 12px
                    box-sizing border-box
                    border-right 1px solid rgba(255,255,255,0.1)
                    font-size 16px
                    font-weight 700
                    &.highlight
                        color #fff
                .desc
                    display inline-block
                    vertical-align top
                    line-height 24px
                    margin 12px 0 0 12px
                    font-size 10px
            .content-right
                flex 0 0 105px
                width 105px
                .pay
                    height 48px
                    line-height 48px
                    text-align center
                    font-size 12px
                    font-weight 700
                    background #2b333b
                    &.enough
                        background #00b43c
                        color #fff
        .ball-container
          .ball
            position fixed
            left 32px
            bottom 22px
            z-index 200
            .inner
              width 16px
              height 16px
              border-radius 50%
              background-color #00A0DC
              transition all 1s linear
            &.drop-enter-active
              transition: all 1s cubic-bezier(0.49, -0.29, 0.75, 0.41)
        .shopcart-list
          position absolute
          left 0
          top 0
          z-index -1
          width 100%
          transform translate3d(0, -100%, 0)
          &.fold-enter, &.fold-leave-to
            transform: translate3d(0, 0, 0)
          &.fold-enter-active, &.fold-leave-active
            transition all 0.5s linear
          .list-header
            height 40px
            line-height 40px
            padding 0 18px
            background #f3f5f7
            border-bottom 1px solid rhba(7,17,27,0.1)
            .title
              float left
              font-size 14px
              color rgb(7,17,27)
            .empty
              float right
              font-size 12px
              color rgb(0,160,220)
          .list-content
            overflow hidden
            padding 0 18px
            max-height 217px
            background #fff
            .food
              position relative
              padding 12px
              box-sizing border-box
              border-bottom 1px solid rgba(7,17,27,0.1)
              .name
                line-height 24px
                font-size 14px
                color rgb(7,17,27)
              .price
                position absolute
                right 90px
                bottom 12px
                line-height 24px
                font-size 14px
                font-weight 700
                color rgb(240,20,20)
              .control-wrapper
                position absolute
                right 0
                bottom 6px
    .listmask
      position fixed
      top 0
      left 0
      width 100%
      height 100%
      z-index 40
      background rgba(7,17,27,0.6)
      backdrop-filter blur(10px)
</style>
