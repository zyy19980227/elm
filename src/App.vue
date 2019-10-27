<template>
  <div id="app">
    <v-header :seller='seller'></v-header>
    <div class="tab" border-1px>
      <router-link tag="div" class="tab-item" to='/goods'>
      <div class="content">商品</div>
      </router-link>
      <router-link tag="div" class="tab-item" to="/ratings">
      <div class="content">评论</div>
      </router-link>
      <router-link tag="div" class="tab-item" to="/seller">
      <div class="content">商家</div>
      </router-link>
    </div>
    <keep-alive>
      <router-view :seller='seller' :goods='goods' :ratings='ratings'/>
    </keep-alive>
  </div>
</template>

<script>
import Header from './components/header/header'
import axios from 'axios'
export default {
  name: 'App',
  data () {
    return {
      seller: {},
      goods: [],
      ratings: []
    }
  },
  components: {
    'v-header': Header
  },
  methods: {
    getInfo () {
      axios.get('/api/data.json')
        .then(this.getInfoSucc)
    },
    getInfoSucc (res) {
      res = res.data
      if (res.ret && res.data) {
        const data = res.data
        this.seller = data.seller
        this.goods = data.goods
        this.ratings = data.ratings
      }
    }
  },
  created () {
    this.getInfo()
  }
}
</script>

<style lang="stylus" scoped>
  .tab
    display flex
    width 100%
    height 40px
    line-height 40px
    border-bottom 1px solid rgba(7,17,27,0.1)
    .tab-item
      flex 1
      text-align center
      .content
        font-size 14px
        color rgb(77,85,93)
    .active
      .content
        color rgb(240,20,20)
</style>
