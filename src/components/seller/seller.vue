<template>
 <div class="seller" ref="seller">
   <div class="seller-content">
     <div class="overview">
       <h1 class="title">{{seller.name}}</h1>
       <div class="desc">
         <star :size='36' :score='seller.score' class="star"></star>
         <span class="text">({{seller.ratingCount}})</span>
         <span class="text">月售{{seller.sellCount}}单</span>
       </div>
        <ul class="remark">
          <li class="block">
            <h2 class="title">起送价</h2>
            <div class="content">
              <span class="stress">{{seller.minPrice}}</span>元
            </div>
          </li>
          <li class="block">
            <h2 class="title">商家配送</h2>
            <div class="content">
              <span class="stress">{{seller.deliveryPrice}}</span>元
            </div>
          </li>
          <li class="block">
            <h2 class="title">平均配送时间</h2>
            <div class="content">
              <span class="stress">{{seller.deliveryTime}}</span>分钟
            </div>
          </li>
        </ul>
        <div class="favourite">
          <transition>
            <span class="iconfont" @click="favourite" :class="{'active': favorite}">&#xe601;</span>
          </transition>
          <span class="text" :class="{'active': favorite}">{{favoriteText}}</span>
        </div>
     </div>
     <split></split>
     <div class="bulletin">
       <h1 class="title">公告与活动</h1>
       <div class="content-wrapper">
         <p class="content">{{seller.bulletin}}</p>
       </div>
       <ul v-if="seller.supports" class="supports">
          <li class="support-item" v-for="(item,type) in seller.supports" :key="type">
            <span class="icon" :class="classMap[seller.supports[type].type]"></span>
            <span class="text">{{seller.supports[type].description}}</span>
          </li>
       </ul>
     </div>
     <split></split>
     <div class="pics">
       <h1 class="title">商家实景</h1>
       <div class="pic-wrapper" ref="pic">
         <ul class="pic-list" ref="list">
           <li class="pic-item" v-for="(pic,index) of seller.pics" :key="index">
             <img :src="pic" width="120" height="90">
           </li>
         </ul>
       </div>
     </div>
     <split></split>
     <div class="info">
       <h1 class="title">商家信息</h1>
       <ul>
         <li class="info-item" v-for="(info,index) of seller.infos" :key="index">{{info}}</li>
       </ul>
     </div>
   </div>
 </div>
</template>

<script>
import star from '../star/star'
import split from '../split/split'
import BScroll from 'better-scroll'
import {saveToLocal, loadFromlLocal} from '@/common/js/store.js'
export default {
  name: 'Seller',
  props: {
    seller: {
      type: Object
    }
  },
  data () {
    return {
      favorite: (() => {
        return loadFromlLocal(this.seller.id, 'favorite', false)
      })()
    }
  },
  components: {
    star,
    split
  },
  created () {
    this.classMap = ['decrease', 'discount', 'special', 'invoice', 'guarantee']
  },
  mounted () {
    this.scroll = new BScroll(this.$refs.seller, {click: true})
    this.initPics()
  },
  methods: {
    initPics () { // ul必须要有宽度才能滚动
      if (this.seller.pics) {
        let picWidth = 120
        let margin = 6
        let width = (picWidth + margin) * this.seller.pics.length - margin
        this.$refs.list.style.width = width + 'px'
        this.scroll = new BScroll(this.$refs.pic, {
          scrollX: true,
          eventPassthrough: 'vertical'
        })
      }
    },
    favourite () {
      this.favorite = !this.favorite
      saveToLocal(this.seller.id, 'favorite', this.favorite)
    }
  },
  computed: {
    favoriteText () {
      return this.favorite ? '已收藏' : '收藏'
    }
  }
}
</script>

<style lang="stylus" scoped>
  @import '../../common/stylus/mixin'
  .seller
    position absolute
    top 176px
    bottom 0
    left 0
    width 100%
    overflow hidden
    .overview
      padding 18px
      .title
        margin-bottom 8px
        line-height: 14px
        color: rgb(7, 17, 27)
        font-size: 14px
      .desc
        padding-bottom 18px
        font-size 0
        border-bottom 1px solid rgba(7, 17, 27, 0.1)
        .star
          display inline-block
          vertical-align top
          margin-right 8px
        .text
          display inline-block
          vertical-align top
          line-height 16px
          font-size 10px
          margin-right 12px
          color rgb(77, 85, 93)
      .remark
        display flex
        padding-top 18px
        .block
          flex 1
          text-align center
          border-right 1px solid rgba(7, 17, 27, 0.1)
          &:last-child
            border none
          .title
            margin-bottom 4px
            line-height 10px
            font-size 10px
            color rgb(147, 153, 149)
          .content
            line-height 24px
            font-size 10px
            color rgb(7, 17, 27)
            .stress
              font-size 24px
      .favourite
        position: absolute
        right: 11px
        top: 18px
        width: 50px
        text-align: center
        .iconfont
          display: block
          margin-bottom: 4px
          line-height: 24px
          font-size: 24px
          width: 50px
          color: #d4d6d9
          transform: rotate(0deg)
          transition all 1s
          &.active
            color: rgb(240,20,20)
            transform: rotate(-360deg)
            transition all 1s
        .text
          line-height: 10px
          font-size: 10px
          color: rgb(77,85,93)
          &.active
            color rgb(255,153,0)
    .bulletin
      padding 18px 18px 0 18px
      .title
        margin-bottom 8px
        line-height: 14px
        color: rgb(7, 17, 27)
        font-size: 14px
      .content-wrapper
        padding 0 12px 16px 12px
        border-bottom 1px solid rgba(7, 17, 27, 0.1)
        .content
          line-height 24px
          font-size 12px
          color rgb(240,20,20)
      .supports
        .support-item
          padding 16px 12px
          font-size 0
          border-bottom 1px solid rgba(7, 17, 27, 0.1)
          &:last-child
            border none
          .icon
            display: inline-block
            vertical-align: top
            width: 16px
            height: 16px
            margin-right: 6px
            background-size: 16px 16px
            background-repeat: no-repeat
            &.decrease
              bg-image('decrease_4')
            &.discount
              bg-image('discount_4')
            &.guarantee
              bg-image('guarantee_4')
            &.invoice
              bg-image('invoice_4')
            &.special
              bg-image('special_4')
          .text
            line-height 16px
            font-size 12px
            color rgb(7, 17, 27)
    .pics
      padding 18px
      .title
        margin-bottom 12px
        line-height: 14px
        color: rgb(7, 17, 27)
        font-size: 14px
      .pic-wrapper
        width 100%
        overflow hidden
        white-space nowrap
        .pic-list
          font-size 0
          .pic-item
            display inline-block
            margin-right 6px
            width 120px
            height 90px
            &:last-child
              margin 0
    .info
      padding 18px 18px 0 18px
      .title
        padding-bottom 12px
        line-height: 14px
        color: rgb(7, 17, 27)
        font-size: 14px
        border-bottom 1px solid rgba(7, 17, 27, 0.1)
      .info-item
        padding 16px 12px
        line-height 16px
        border-bottom 1px solid rgba(7, 17, 27, 0.1)
        color rgb(7, 17, 27)
        font-size 12px
        &:last-child
          border none
</style>
