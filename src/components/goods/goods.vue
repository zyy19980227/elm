<template>
  <div class="goods">
    <div class="menu-wrapper" ref="wrapper1">
      <ul>
        <li v-for='(item,index) of goods' :key="index" class="menu-item" :class="{'current': currentIndex === index}" @click="selectMenu(index)">
          <span class="text">
            <span v-show="item.type > 0" class="icon" :class="classMap[item.type]"></span>
            {{item.name}}
          </span>
        </li>
      </ul>
    </div>
    <div class="foods-wrapper" ref="wrapper2">
      <ul>
        <li v-for='(item,index) of goods' :key="index" class="food-list food-list-hook">
          <h1 class="title">{{item.name}}</h1>
          <ul>
            <li v-for='(food,index) of item.foods' :key="index" class="food-item" @click="selectFood(food,$event)">
              <div class="icon">
                <img width="57" height="57" :src="food.icon">
              </div>
              <div class="content">
                <h2 class="name">{{food.name}}</h2>
                <p class="desc">{{food.description}}</p>
                <div class="extra">
                  <span class="count">月售{{food.sellCount}}份</span><span>好评率{{food.rating}}%</span>
                </div>
                <div class="price">
                  <span class="now">￥{{food.price}}</span>
                  <span class="old" v-show="food.oldPrice">￥{{food.oldPrice}}</span>
                </div>
                <div class="control-wrapper">
                  <cartcontrol :food='food' @increment="_drop"></cartcontrol>
                </div>
              </div>
            </li>
          </ul>
        </li>
      </ul>
    </div>
    <shopcart :select-foods='selectFoods' :deliveryprice='seller.deliveryPrice' :minprice='seller.minPrice' ref="shopcart"></shopcart>
    <food :food='selectedFood' ref="food" @increment="_drop"></food>
  </div>
</template>

<script>
import BScroll from 'better-scroll'
import shopcart from '../shopcart/shopcart'
import food from '../food/food'
import cartcontrol from '../cartcontrol/cartcontrol'
export default {
  name: 'Goods',
  props: {
    seller: Object,
    goods: Array
  },
  data () {
    return {
      listHeight: [],
      scrollY: 0,
      selectedFood: {}
    }
  },
  components: {
    shopcart,
    cartcontrol,
    food
  },
  computed: {
    currentIndex () {
      for (let i = 0; i < this.listHeight.length; i++) {
        let height1 = this.listHeight[i]
        let height2 = this.listHeight[i + 1]
        if (!height2 || (this.scrollY >= height1 && this.scrollY < height2)) {
          return i
        }
      }
      return 0
    },
    selectFoods () {
      let foods = []
      this.goods.forEach((good) => {
        good.foods.forEach((food) => {
          if (food.count) {
            foods.push(food)
          }
        })
      })
      return foods
    }
  },
  methods: {
    selectMenu (index) {
      let foodList = this.$refs.wrapper2.getElementsByClassName('food-list-hook')
      let el = foodList[index]
      this.foodscroll.scrollToElement(el, 300)
    },
    _calculateHeight () {
      let foodList = this.$refs.wrapper2.getElementsByClassName('food-list-hook')
      let height = 0
      this.listHeight.push(height)
      for (let i = 0; i < foodList.length; i++) {
        let item = foodList[i]
        height += item.clientHeight
        this.listHeight.push(height)
      }
    },
    _initscroll () {
      this.menuscroll = new BScroll(this.$refs.wrapper1, {click: true})
      this.foodscroll = new BScroll(this.$refs.wrapper2, {probeType: 3, click: true})
      this.foodscroll.on('scroll', (pos) => {
        this.scrollY = Math.abs(Math.round(pos.y))
      })
    },
    _drop (target) { // 在goods.vue定义 _drop方法将cartcontrol的传递过来target对象再传递给shopCart
      this.$nextTick(() => { // 使用$nextTick优化体验
        this.$refs.shopcart.drop(target) // 父组件goods通过.$refs属性访问shopCart子组件的drop方法
      })
    },
    selectFood (food, event) {
      this.selectedFood = food
      this.$refs.food.show()
    }
  },
  created () {
    this.classMap = ['decrease', 'discount', 'special', 'invoice', 'guarantee']
    setTimeout(() => {
      this._calculateHeight()
    }, 500)
  },
  mounted () {
    this._initscroll()
  }
}
</script>

<style lang="stylus" scoped>
  @import '../../common/stylus/mixin'
  .goods
    display flex
    position absolute
    top 176px
    bottom 46px
    width 100%
    overflow hidden
    .menu-wrapper
      flex 0 0 80px
      width 80px
      background #f3f5f7
      .menu-item
        display table
        height 54px
        width 56px
        line-height 14px
        padding 0 12px
        &.current
          position relative
          margin-top -1px
          z-index 10
          background #fff
          font-weight 700
          .text
            border-bottom 1px solid rgba(7,17,27,0)
        .text
          display table-cell
          width 56px
          vertical-align middle
          font-size 12px
          border-bottom 1px solid rgba(7,17,27,0.1)
          .icon
            display: inline-block;
            vertical-align: top;
            width: 12px;
            height: 12px;
            margin-right: 4px;
            background-size: 12px 12px;
            background-repeat: no-repeat;
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
  .foods-wrapper
    flex 1
    .title
      padding-left 14px
      height 26px
      line-height 26px
      border-left 2px solid #d9dde1
      font-size 12px
      color rgb(147,153,159)
      background #f3f5f7
    .food-item
      display flex
      margin  18px
      padding-bottom 18px
      border-bottom 1px solid rgba(7,17,27,0.1)
      &:last-child
        border-bottom 1px solid rgba(7,17,27,0)
        margin-bottom 0
      .icon
        flex 0 0 57px
        margin-right 10px
      .content
        flex 1
        position relative
        .name
          margin 2px 0 8px 0
          height 14px
          line-height 14px
          font-size 14px
          color rgb(7,17,27)
        .desc,.extra
          line-height 10px
          font-size 10px
          color rgb(147,153,159)
        .desc
          margin-bottom 8px
          line-height 12px
        .extra
          .count
            margin-right 12px
        .price
          font-weight 700
          line-height 24px
          .now
            margin-right 4px
            font-size 14px
            color rgb(240,20,20)
          .old
            text-decoration line-through
            font-size 10px
            color rgb(147,153,159)
        .control-wrapper
          position absolute
          right 0
          bottom -7px
</style>
