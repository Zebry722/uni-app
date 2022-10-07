<template>
  <view>
    <!-- 搜索组件 -->
    <view class="search-box">
          <my-search @click='gotoSearch'></my-search>
    </view>
    <!-- 轮播图区域 -->
    <swiper :indicator-dots="true" :autoplay="true" :interval="3000" :duration="1000" :circular="true">
      <swiper-item v-for="(item , index) in swiperList" :key="index">
        <navigator class="swiper-item" :url="'/subpkg/good_detail/good_detail?goods_id=' + item.goods_id">
          <image :src="item.image_src"></image>
        </navigator>
      </swiper-item>
    </swiper>
    <!-- 轮播图区域end -->
    <!-- 分类导航区域 -->
    <view class="navClassify">
      <view class="navClassify1" v-for="(item , index) in navList" :key="index" @click="navSkip(item)">
        <image :src="item.image_src"></image>
      </view>
    </view>
    <!-- 分类导航区域end -->
    <!-- 楼层区域 -->
    <view class="floor-list">
      <view class="floot-item" v-for="(item,index) in floorList" :key="index">
        <image :src="item.floor_title.image_src" class="floor-title"></image>
        <!-- 多图片区域 -->
        <view class="floor-img-box">
          <!-- 大图片 -->
          <navigator class="floor-img-big" :url="item.product_list[0].url">
            <image :src="item.product_list[0].image_src" :style="{width: item.product_list[0].image_width + 'rpx'}"
              mode="widthFix"></image>
          </navigator>
          <!-- 小图片 -->
          <view class="floor-img-mini">
            <navigator class="floor-img-tittle" v-for="(item1,index1) in item.product_list" :key="index1"
              v-if="index1 !==0" :url="item1.url">
              <image :src="item1.image_src" mode="widthFix" :style="{width: item1.image_width + 'rpx'}"></image>
            </navigator>
          </view>
        </view>
        <!-- 多图片区域end -->
      </view>
    </view>
    <!-- 楼层区域end -->
  </view>
</template>

<script>
  export default {
    data() {
      return {
        // 轮播图数据
        swiperList: [],
        // 分类导航数据
        navList: [],
        // 楼层数据
        floorList: []
      };
    },
    onLoad() {
      this.getSwiperList()
      this.getnavList()
      this.getFloor()
    },
    methods: {
      async getSwiperList() {
        const {
          data: res
        } = await uni.$http.get('/api/public/v1/home/swiperdata')
        if (res.meta.status !== 200) return uni.$showMsg()
        this.swiperList = res.message
        // uni.$showMsg('请求成功')
      },

      async getnavList() {
        const {
          data: res
        } = await uni.$http.get('/api/public/v1/home/catitems')
        if (res.meta.status !== 200) return uni.$showMsg()
        this.navList = res.message
        // this.navList=res
      },
      // 跳转
      navSkip(item) {
        if (item.name === '分类') {
          uni.switchTab({
            url: '/pages/cart/cart'
          })
        }
      },

      async getFloor() {
        const {
          data: res
        } = await uni.$http.get('/api/public/v1/home/floordata')
        // 对楼层数据进行处理
        res.message.forEach(floor => {
          floor.product_list.forEach(plord => {
            plord.url = '/subpkg/goods_list/goods_list?' + plord.navigator_url.split('?')[1]
          })
        })
        if (res.meta.status !== 200) return uni.$showMsg()
        this.floorList = res.message
        // console.log(this.floorList)
      },
      gotoSearch() {
        uni.navigateTo({
          url: '/subpkg/search/search'
        })
      }

    }
  }
</script>

<style lang="scss">
  swiper {
    height: 330rpx;

    .swiper-item,
    image {
      width: 100%;
    }
  }

  .navClassify {
    display: flex;
    justify-content: space-around;
    margin: 15rpx 0;

    image {
      width: 128rpx;
      height: 140rpx;
    }
  }

  .floor-title {
    height: 60rpx;
    width: 100%;
    display: flex;
  }

  .floor-img-box {
    display: flex;
    padding-left: 10rpx;

    .floor-img-mini {
      display: flex;
      flex-wrap: wrap;
      justify-content: space-around;
    }
  }
  .search-box{
    position: sticky;
    top: 0;
    z-index: 66;
  }
</style>
