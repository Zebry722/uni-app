<template>
  <view v-if="goodsInfo.goods_name" class="goods-detail-container">
    <swiper :indicator-dots="true" :autoplay="true" :interval="3000" :duration="1000" :circular="true">
      <swiper-item v-for="(item,index) in goodsInfo.pics" :key="index">
        <image :src="item.pics_big" @click="preview(index)"></image>
      </swiper-item>
    </swiper>
    <!-- 商品内容显示区域 -->
    <view class="goods-info-box">
      <view class="price">
        ￥{{goodsInfo.goods_price}}
      </view>
      <view class="goods-info-body">
        <view class="goods-name">
          {{goodsInfo.goods_name}}
        </view>
        <view class="favi">
          <uni-icons type="star" size="18" color="gray"></uni-icons>
          <text>收藏</text>
        </view>
      </view>
      <view class="yf">
        快递：免运费
      </view>
    </view>
    <rich-text :nodes="goodsInfo.goods_introduce"></rich-text>
    
    <!-- 商品导航组件 -->
    <view class="goods_nav">
    <uni-goods-nav :options="options" :fill="true" :button-group="buttonGroup" @click="onClick" @buttonClick="buttonClick" style="height: 800px;"></uni-goods-nav>
      </view>
  </view>
</template>

<script>
  export default {
    data() {
      return {
        goodsInfo: {}, // 商品详情数据
        options:[{
          icon:'shop',
          text:'店铺',
        },{
          icon:'cart',
          text:'购物车'
        }],
        buttonGroup:[{
          text:'加入购物车',
          backgroundColor:'linear-gradient(90deg, #FE6035, #EF1224)',
          color:'#fff'
        },{
          text:'立即购买',
          backgroundColor:'linear-gradient(90deg, #FE6035, #EF1224)',
          color:'#fff'
        }]
      }
    },
    onLoad(options) {
      const goods_id = options.goods_id
      this.getGoodsDetail(goods_id)
    },
    methods: {
      async getGoodsDetail(goods_id) {
        const {
          data: res
        } = await uni.$http.get('/api/public/v1/goods/detail', {
          goods_id
        })
        if (res.meta.status !== 200) return uni.$showMsg()
        res.message.goods_introduce = res.message.goods_introduce.replace(/<img /g, '<img style="display:block;" ').replace(/webp/g,'jpg')
        this.goodsInfo = res.message
      },
      // 实现轮播图的预览效果
      preview(index) {
        // 调用 uni.previewImage() 方法预览图片
        uni.previewImage({
          // 预览时，默认显示图片的索引
          current: index,
          // 所有图片 url 地址的数组
          urls: this.goodsInfo.pics.map(x => x.pics_big)
        })
      },
      onClick(e){
        if(e.content.text === '购物车') {
          uni.switchTab({
            url:'/pages/cart/cart'
          })
        }
      },
    }
  }
</script>

<style lang="scss">
  .goods-detail-container{
    padding-bottom: 50px;
  }
  swiper {
    height: 600rpx;

    image {
      height: 100%;
      width: 100%;
    }
  }

  .goods-info-box {
    padding: 10px;
    padding-right: 0;

    .price {
      font-size: 18px;
      margin: 10px 0;
      color: #c00000;
    }

    .goods-info-body {
      display: flex;
      justify-content: space-between;

      .goods-name {
        font-size: 13px;
        margin-right: 10px;
      }

      .favi {
        width: 120px;
        font-size: 12px;
        display: flex;
        flex-direction: column;
        align-items: center;
        justify-content: center;
        border-left: 1px solid #efefef;
        color: gray;
      }
    }

    .yf {
      font-size: 12px;
      color: gray;
      margin: 10px 0;
    }
  }
  .goods_nav{
    position: fixed;
    bottom: 0;
    left: 0;
    width: 100%;
  }
</style>
