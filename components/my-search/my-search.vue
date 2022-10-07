<template>
  <view class="my-search-box" :style="{'background-color': bgcolor}" @click="searchBoxHandler">
    <view class="my-search-item" :style="{'border-radius': radius + 'px'}">
      <uni-icons type="search" size="19"></uni-icons>
       <text class="seek">搜索</text>
    </view>
  </view>
</template>

<script>
  export default {
    name:"my-search",
    props:{
      // 背景颜色
      bgcolor:{
        type:String,
        default: '#008B8B'
      },
      // 圆角尺寸
      radius:{
        type:Number,
        default:18
      }
    },
    data() {
      return {
        
      };
    },
    methods:{
      searchBoxHandler(){
        this.$emit('click')
      }
    }
  }
</script>

<style lang="scss">
.my-search-box{
    height: 50px;
    // background-color: #008B8B;
    display: flex;
    align-items: center;
  .my-search-item{
    height: 36px;
    width: 100%;
    margin: 15px;
    // border-radius: 8px;
    background-color: #fff;    
    display: flex;
    align-items: center;
    justify-content: center;
    .seek{
      margin: 10px;
    }
  }
}
</style>