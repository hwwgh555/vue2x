<template>
  <header class="header-bar header-bar-nav">
      <a @click="back" class="header-action-back header-icon-left-nav header-icon  fl"></a>
      <h1 class="header-title omit-one">{{header.title}} <slot></slot></h1>
      <a v-if="isActionShow" @click="btnAction" class="float-right text-btn flex-v-center" :style="styleObject">
        <img v-if="'write'===icon" class="text-btn-img" src="./icon-write.png">
        <!-- 这里存在缺陷必须，通过修改原代码来添加icon，而不能通过上层直接传递新的图片来替代 -->
        <img v-if="'home'===icon" class="text-btn-img" src="./icon-home-active.png">
        <span class="text-btn-text">{{header.action}}</span>
      </a>
      <!-- <a @click="switchState" class="header-icon header-icon-compose fr"></a> -->
      <!-- <a class="header-icon header-icon-navigate fr"></a> -->
  </header>
</template>

<script>
// 对应图标表
const IconType = {
  0: 'write',
  1: 'home'
}
export default {
  name: 'HeaderBack',
  props: {
    header: {
      type: Object,
      default () {
        return {
          title: '标题',
          action: '操作'
        }
      }
    },
    isActionShow: { // 整个右侧部分是否存在
      type: Boolean,
      default: true
    },
    iconType: { // 存在这个值才可能显示图标
      type: Number,
      default: -1
    },
    color: {
      type: String
    }
  },
  computed: {
    styleObject () {
      if (this.color) {
        return {
          color: this.color
        }
      }
    },
    icon () {
      let iconType = this.iconType
      if (this.iconType === -1) {
        return false
      }
      if (IconType[iconType] !== undefined) {
        return IconType[iconType]
      } else {
        return false
      }
    }
  },
  methods: {
    btnAction () {
      this.$emit('headerAction', true)
    },
    back () {
      this.$router.go(-1)
    }
  }
}
</script>

<style scoped>
 /* header样式 */
 /* 使用的cart-header的样式， 命名可以考虑调整下 */
    header {
        display: block;
    }
    .header-bar-nav {
        top: 0;
        -webkit-box-shadow: 0 1px 6px #ccc;
        box-shadow: 0 1px 6px #ccc;
    }
    .header-bar {
        position: fixed;
        z-index: 10;
        right: 0;
        left: 0;
        height: .88rem;
        padding-right: .2rem;
        padding-left: .2rem;
        border-bottom: 0;
        background-color: #fff;
        -webkit-box-shadow: 0 0 1px ;
        box-shadow: 0 0 1px rgba(0, 0, 0, .35);
        -webkit-backface-visibility: hidden;
        backface-visibility: hidden;
    }
    /* 头部文字 */
    .header-title {
        margin: 0;
        padding: 0;
        position: absolute;
        top:0; /* 这里必须加，不加会下掉*/
        right: .8rem;
        left: .8rem;
        text-align: center;
        font-size: .34rem;
        font-weight: 500;
        line-height: .88rem;
        color: #000;
    }
    /* icon */
    .header-bar-nav.header-bar .header-icon {
        /* 图标外层大小与位置 */
        /* 左侧图标位置 */
        margin-right: -.2rem;
        /* 右侧图标位置  */
        margin-left: -.2rem;
        padding:.2rem;
        /* 图标大小 */
        font-size: .48rem;
        position: relative;
        z-index: 20;
    }
    /* 控制图标大小显示 */
    .header-icon {
        font-family: Muiicons;
        font-size: .48rem;
        font-weight: 400;
        font-style: normal;
        line-height: 1;
        display: inline-block;
        text-decoration: none;
        -webkit-font-smoothing: antialiased;
    }
    /* 点击效果：马上触发，马上消失 */
    .header-icon:active {
        opacity: .3;
    }
    .header-icon-back:before,
    .header-icon-left-nav:before {
        content: '\e471';
    }
    .text-btn {
        position: relative;
        display: inline-block;
        margin-right: -.2rem;
        margin-left: -.2rem;
        padding:0 .2rem;
        height: .88rem;
        line-height: .88rem;
        text-align: center;
        font-size: .26rem;
        z-index: 20;
        font-weight: 400;
        color: rgb(116, 116, 116)
    }
    .flex-v-center {
      display: flex;
      align-items: center;
      font-size: 0;
    }
    .text-btn-img {
      margin-right: .12rem;
      display: inline-block;
      width: .24rem;
      height: .24rem;
    }
    .text-btn-text {
      font-size: .26rem;
    }
    .text-btn:active {
        color: rgba(214,83,228,.3)
    }
</style>
