<template>
  <view>
    <view class="search-box">
      <uni-search-bar @input="input" bgColor="#fff" :radius="18" cancelButton="none" />
    </view>
    <!-- 搜索建议 -->
    <view class="search-item" v-if="searchkeywords.length !== 0">
      <view class="search-item-left" v-for="(item,index) in searchkeywords" :key="index" @click="gotoDetail(item)">
        <view class="goods-name">
          {{item.goods_name}}
        </view>
        <uni-icons type="arrowright" size="16"></uni-icons>
      </view>
    </view>
    <!-- 搜索建议end -->
    <!-- 搜索历史 -->
    <view class="history-box" v-else>
      <!-- 标题区域 -->
      <view class="history-title">
        <text>搜索历史</text>
        <uni-icons type="trash" size="17" @click="goodsDelete(item)"></uni-icons>
      </view>
      <!-- 列表区域 -->
      <view class="history-list">
        <!-- <uni-tag :text="item" v-for="(item,index) in historyList" :key="index"></uni-tag> -->
        <uni-tag :text="item" :inverted="true"   v-for="(item,index) in historyList" :key="index"/>
      </view>
    </view>
  </view>
</template>

<script>
  export default {
    data() {
      return {
        time: null,
        // 搜索关键词
        keywords: '',
        // 搜索建议
        searchkeywords: [],
        // 搜索历史数据
        historyList: [],
      };
    },
    onLoad() {
     this.historyList= JSON.parse(uni.getStorageSync('keywords') || '[]')
    },
    methods: {
      // 输入框处理事件的处理函数
      input(e) {
        clearTimeout(this.time)
        this.time = setTimeout(() => {
          this.keywords = e
          // console.log(this.keywords);
          this.getsearchkeywords()
        }, 500)
      },
      async getsearchkeywords() {
        if (this.keywords === '') {
          this.searchkeywords = []
          return
        }
        const {
          data: res
        } = await uni.$http.get('/api/public/v1/goods/qsearch', {
          query: this.keywords
        })
        if (res.meta.status !== 200) return uni.$showMsg()
        this.searchkeywords = res.message
        this.storageHistory()
      },
      gotoDetail(item) {
        uni.navigateTo({
          url: '/subpkg/good_detail/good_detail?goods_id=' + item.goods_id
        })
      },
      storageHistory(){
        const set =new Set(this.historyList);
        set.delete(this.keywords)
        set.add(this.keywords)
        this.historyList=Array.from(set)
        // this.historyList.unshift(this.keywords)
        // 对搜索历史持久化存储
        uni.setStorageSync('keywords',JSON.stringify( this.historyList))
      }
      // 点击清空历史和本地数据
      goodsDelete(keywords){
        this.historyList=[]
        uni.setStorageSync('keywords', '[]')
      }
    },
   computed: {
     historys() {
       return [...this.historyList].reverse()
     }
   }
  }
</script>

<style lang="scss">
  .search-box {
    position: sticky;
    top: 0;
    z-index: 66;
  }

  .search-item {
    padding: 0 5px;

    .search-item-left {
      display: flex;
      // margin: 15px 0;
      padding: 15px 5px;
      border: 1rpx solid #efefef;
      justify-content: space-between;

      .goods-name {
        // 文字不允许换行
        white-space: nowrap;
        // 超出隐藏
        overflow: hidden;
        // 文本溢出后使用 ... 代替
        text-overflow: ellipsis;
        margin-right: 3px;
      }
    }
  }
  .history-box{
     padding:  0 15px;
    .history-title{
      display: flex;
      justify-content: space-between;
      height: 40px;
      align-items: center;
      font-size: 13px;
      border-bottom: 1px solid #efefef;
    }
    .history-list{
      display: flex;
      flex-wrap: wrap;
      .uni-tag{
       display: block;
       margin-top: 15px;
       margin-right: 15px;
      }
    }
  }
</style>
