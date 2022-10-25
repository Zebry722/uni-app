<template>
  <view>
    <view class="goods-list">
      <view v-for="(item,index) in goodsList" :key="index" @click="mustDetail(item)">
        <my-goods :goods="item"></my-goods>
      </view>
    </view>
  </view>
</template>

<script>
  export default {
    data() {
      return {
        // 请求参数对象
        queryObj: {
          query: '',
          cid: '',
          pagenum: 1,
          pagesize: 10,
        },
        // 搜索数据
        goodsList: [],
        // 搜索总条数
        total: 0,
        // 节流阀
        isLoading: false,
      }
    },

    onLoad(options) {
      this.queryObj.query = options.query || ''
      this.queryObj.cid = options.cid || ''
      this.getGoodsList()
    },

    methods: {
      async getGoodsList(cb) {
        this.isLoading = true
        const {
          data: res
        } = await uni.$http.get('/api/public/v1/goods/search', this.queryObj)
        this.isLoading = false
        cb && cb()
        if (res.meta.status !== 200) return uni.$showMsg()
        this.goodsList = [...this.goodsList, ...res.message.goods]
        this.total = res.message.total
      },
      mustDetail(must) {
        uni.navigateTo({
          url: '/subpkg/good_detail/good_detail?goods_id=' + must.goods_id
        })
        // uni.navigateTo({
        //   url: '/subpkg/good_detail/good_detail?goods_id=' + must.goods_id
        // })
      },
    },

    onReachBottom() {
      if (this.queryObj.pagenum * this.queryObj.pagesize >= this.total) return uni.$showMsg('数据加载完毕')
      // 让页面自增1
      if (this.isLoading) return
      this.queryObj.pagenum++
      this.getGoodsList()
    },
    // 下拉刷新
    onPullDownRefresh() {
      // 重置数据请求
      this.queryObj.pagenum = 1
      // 搜索数据
      this.goodsList = []
      // 搜索总条数
      this.total = 0
      // 节流阀
      this.isLoading = false
      // 重新发起请求
      this.getGoodsList(() => uni.stopPullDownRefresh())
    }
  }
</script>

<style lang="scss">
  .goods-item {
    display: flex;
    padding: 10px 5px;
    border-bottom: 1px solid #f0f0f0;

    .goods-left {
      margin-right: 10px;

      .goods-pic {
        width: 100px;
        height: 100px;
        display: block;

      }
    }

    .goods-right {
      display: flex;
      flex-direction: column;
      justify-content: space-between;

      .goods-name {
        font-size: 13px
      }

      .goods-money-box {
        .goods-price {
          color: #C00000;
          font-size: 16px
        }
      }
    }
  }
</style>
