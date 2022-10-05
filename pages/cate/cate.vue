<template>
  <view>
    <view class="scroll-item">
      <!-- 左侧滑动栏 -->
      <scroll-view scroll-y="true" class="scroll-item-right" :style="{height:whiteScoroll + 'px'}">
        <block>
           <view :class="['scroll-item-raght-ever', index === active ? 'active' :'']" v-for="(item,index) in CateList" :key="index" @click="activeClick(index)">{{item.cat_name}}</view>
        </block>
      </scroll-view>
      <!-- 侧滑动栏 -->
      <scroll-view scroll-y="true"  :style="{height:whiteScoroll + 'px'}" :scroll-top="scrollTop">
      <view class="scroll-item-left" v-for="(item1,index1) in cateLevel1" :key="index1">
        <view class="scroll-item-left-title">
          {{item1.cat_name}}
        </view>
        <!-- 三级分类 -->
        <view class="scroll-item-left-list" >
          <view class="scroll-item-left-item" v-for="(item2,index2) in item1.children" :key="index2" @click="gotgoodslist(item2)">
            <image :src="item2.cat_icon"></image>
            <text>{{item2.cat_name}}</text>
          </view>
        </view>
      </view>
      </scroll-view>
    </view>
  </view>
</template>

<script>
  export default {
    data() {
      return {
        whiteScoroll: '',
        // 列表数据
        CateList:[],
        active:0,
        // 二级分类列表
        cateLevel1:[],
        // 滚动视图区域
        scrollTop:0,
      };
    },
    onLoad() {
      const sysInfo = uni.getSystemInfoSync()
      this.whiteScoroll = sysInfo.windowHeight
      this.getCateList()
    },
    methods:{
     async getCateList() {
       const {data:res} = await uni.$http.get('/api/public/v1/categories')
       if(res.meta.status !== 200) return uni.$showMsg()
        this.CateList = res.message
        this.cateLevel1=res.message[0].children
     },
     activeClick(index) {
      this.active=index
      this.cateLevel1=this.CateList[index].children
      this.scrollTop= this.scrollTop === 0 ? 1 : 0
     },
     gotgoodslist(item) {
       uni.navigateTo({
         url:'/subpkg/goods_list/goods_list?cid=' + item.cat_id
       })
     }
    }
  }
</script>

<style lang="scss">
  .scroll-item {
    display: flex;

    .scroll-item-right {
      width: 120px;

      .scroll-item-raght-ever {
        line-height: 60px;
        background-color: #f7f7f7;
        text-align: center;
        font-size: 12px;

        &.active {
          background-color: #FF9900;
          position: relative;

          &::before {
            content: ' ';
            display: block;
            width: 3px;
            height: 30px;
            background-color: #c00000;
            position: absolute;
            left: 0;
            top: 50%;
            transform: translateY(-50%);
          }
        }

      }
    }
    .scroll-item-left-title{
      font-size: 15px;
      font-weight: bold;
      text-align: center;
      padding: 15px 0;
    }
    
    .scroll-item-left-list{
      display: flex;
      flex-wrap: wrap;
      .scroll-item-left-item{
        width: 33.33%;
        display: flex;
        flex-direction: column;
        justify-content: center;
        align-items: center;
        margin-bottom: 10px;
        image {
          width: 60px;
          height: 60px;
        }
        text {
          font-size: 12px;
        }
      }
    }
  }
</style>
