<template>
    <div class="ratingselect">
        <div class="rating-type">
            <span @click="select(2)" class="block positive" :class="{'active': selectType === 2}">{{desc.all}}<span class="count">{{ratings.length}}</span></span>
            <span @click="select(0)" class="block positive" :class="{'active': selectType === 0}">{{desc.positive}}<span class="count">{{positives.length}}</span></span>
            <span @click="select(1)" class="block negative" :class="{'active': selectType === 1}">{{desc.negative}}<span class="count">{{negatives.length}}</span></span>
        </div>
        <div @click='switchclick()' class="switch" :class="{'on': onlyContent}">
            <span class="iconfont">&#xe607;</span>
            <span class="text">只看有内容的评价</span>
        </div>
    </div>
</template>

<script>
const positive = 0
const negative = 1
const all = 2
export default {
  name: 'RatingSelect',
  props: {
    ratings: {
      type: Array,
      default () {
        return []
      }
    },
    selectType: {
      type: Number,
      default: all
    },
    onlyContent: {
      type: Boolean,
      default: false
    },
    desc: {
      type: Object,
      default () {
        return {
          all: '全部',
          positive: '满意',
          negative: '不满意'
        }
      }
    }
  },
  computed: {
    positives () {
      return this.ratings.filter((rating) => {
        return rating.rateType === positive
      })
    },
    negatives () {
      return this.ratings.filter((rating) => {
        return rating.rateType === negative
      })
    }
  },
  data () {
    return {
      selectTypeUpdate: this.selectType,
      onlyContentUpdate: this.onlyContent
    }
  },
  methods: {
    select (type) {
      this.selectTypeUpdate = type
      this.$emit('increment', 'selectType', this.selectTypeUpdate)
    },
    switchclick () {
      this.onlyContentUpdate = !this.onlyContentUpdate
      this.$emit('increment', 'onlyContent', this.onlyContentUpdate)
    }
  }
}
</script>

<style lang="stylus" scoped>
    .ratingselect
        .rating-type
            padding 18px 0
            margin 0 18px
            border-bottom 1px solid rgba(7,17,27,0.1)
            font-size 0
            .block
                display inline-block
                padding 8px 12px
                border-radius 2px
                margin-right 8px
                line-height 16px
                color rgb(77,85,93)
                font-size 12px
                &.active
                    color #fff
                .count
                    margin-left 2px
                    font-size 8px
                    vertical-align top
                &.positive
                    background rgba(0,160,220,0.2)
                    &.active
                        background rgb(0,160,220)
                &.negative
                    background rgba(77,85,93,0.2)
                    &.active
                        background rgb(77,85,93)
        .switch
            padding 12px 18px
            line-height 24px
            font-size 0
            border-bottom 1px solid rgba(7,17,27,0.1)
            color rgb(147,153,159)
            &.on
                .iconfont
                    color #00c850
            .iconfont
                display inline-block
                vertical-align top
                margin-right 4px
                font-size 24px
            .text
                display inline-block
                vertical-align top
                font-size 12px
</style>
