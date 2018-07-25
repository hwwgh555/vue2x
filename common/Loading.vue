<template>
  <div></div>
</template>

<script>
import { Indicator } from 'mint-ui'
// 用于：用于除了列表无限滚动外的Loading
// 使用：通过上级组件传递isLoading，触发loading计时，当超过2s后，出现loading图标
export default {
  name: 'Loading',
  data () {
    return {
      timer: null
    }
  },
  props: {
    isLoading: {
      type: Boolean,
      default: false
    }
  },
  watch: {
    isLoading (n, o) {
      if (!n) {
        this.closeLoadingTimer()
      } else {
        // 页面内其它按钮操作时，返回超时时调用 to do
        // 2s之内设置父级isLoding为true时，如果不清除原来的定时器，会导致出现两个定时器，导致其中一个无法清除，如果跳转页面的话，会导致后面下一个页面直接出现Loading
        this.closeLoadingTimer()
        this.setLoadingTimer()
      }
    }
  },
  methods: {
    setLoadingTimer (timer) {
      let self = this
      let loadingTimer = timer || 2000
      self.timer = setTimeout(function () {
        if (self.isLoading) {
          Indicator.open()
        }
      }, loadingTimer)
    },
    closeLoadingTimer () {
      const self = this
      clearTimeout(self.timer)
      Indicator.close()
    }
  },
  created () {
    // 首次页面进入--只要引入会自动运行一个定时器，是否显示，取决于isLoading的值
    this.setLoadingTimer()
  },
  destroyed () {
    // setTimeout在路由销毁后，并不会被销毁，而会仍然执行；
    // 作用：防止，回退或退出，请求数据结果未得出，从而导致另一个页面，无故出现loading
    let self = this
    clearTimeout(self.timer)
    // 也可能当前已经loading，这时候直接清除Loading
    Indicator.close()
  }
}
</script>
