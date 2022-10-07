<template>
 <view class="goods-item">
   <!-- 左侧盒子 -->
   <view class="goods-left">
     <image :src="goods.goods_small_logo || defaultPic" mode="" class="goods-pic"></image>
   </view>
   <!-- 右侧盒子 -->
   <view class="goods-right">
     <view class="goods-name">
       {{goods.goods_name}}
     </view>
     <view class="goods-money-box">
       <view class="goods-price">
         ￥{{goods.goods_price | tofixed}}
       </view>
     </view>
   </view>
 </view>
</template>

<script>
 export default {
   props:{
    // 商品的信息对象
         goods: {
           type: Object,
           default: {},
         },
   },
   data() {
     return {
        defaultPic: 'https://wx3.sinaimg.cn/mw2000/007zf8TBly1h64wmro149j315s0vcjtb.jpg'
     }
   },
   filters:{
     tofixed(num){
     return  Number(num).toFixed(2)
     }
   },
  
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