<template>
  <div class="more-loading-wrapper">
    <mt-spinner v-show="queryLoading&&!allLoaded" class="more-loading-spinner" type="snake" color="rgb(188,122,255)" :size="20"></mt-spinner>
    <span v-if="allLoaded" class="more-loading-loaded-txt" v-show="allLoaded">翻完了，没有更多了~</span>
  </div>
</template>

<script>
// 用于：滚动加载更多Loading 或者 加载完成后显示“没有更多了”
// 使用：通过传递queryLoading、allLoaded控制显示状态
export default {
  name: 'MoreLoading',
  props: {
    allLoaded: {
      type: Boolean,
      default: false
    },
    queryLoading: {
      type: Boolean,
      default: false
    }
  }
}
</script>

<style>
/* 当前拿的是我的-订单样式 */
/* 更多加载 */
.more-loading-wrapper{
  font-size: 0;
  /* background-color: rgb(245,245,245); */
  text-align: center;
}
.more-loading-spinner {
  display: inline-block;
}
.more-loading-loaded-txt{
  font-size: .28rem;
  color:rgb(140, 140, 140);
}
</style>
