/**
一个 Vue 的滚动到顶部的容器组件（不提供 UI 显示）
使用：
1. 引入自定义文件上传组件: import VxScrollToTop from '@/components/common/VxScrollToTop'
2. 声明它
export default {
  components: {
    VxScrollToTop: VxScrollToTop
  }
}
3. 在 template 中使用
<vx-scroll-to-top>
  <!-- 这里面的内容随你定义，是上拉按钮要显示的样子 -->
  <v-btn color="primary"
          fab>
    <v-icon>vertical_align_top</v-icon>
  </v-btn>
</vx-scroll-to-top>
 */
<template>
  <div :style="scrollToTopStyle"
       v-show="showScrollToTop"
       @click="scrollToTop">
    <slot></slot>
  </div>
</template>
<script>
export default {
  props: {
    // 定义上拉按钮容器的位置
    top: {
      type: [Number, String],
      default: undefined
    },
    bottom: {
      type: [Number, String],
      default: undefined
    },
    left: {
      type: [Number, String],
      default: undefined
    },
    right: {
      type: [Number, String],
      default: undefined
    }
  },
  data: () => ({
    // 是否显示，初始默认不显示
    showScrollToTop: false,
    // 定时器
    timer: null,
    scrollToTopStyle: {
      position: 'fixed',
      top: '',
      bottom: '',
      left: '',
      right: ''
    }
  }),
  methods: {
    isNumber (str) {
      if (!new RegExp('^[0-9]+([.]{1}[0-9]+)?$').test(str)) {
        return false
      }
      return true
    },
    watchPosition () {
      if (![this.top, this.bottom, this.left, this.right].find(i => i !== undefined)) {
        this.scrollToTopStyle.bottom = this.scrollToTopStyle.right = '14px'
      }
    },
    watchTop () {
      if (this.top !== undefined) {
        this.scrollToTopStyle.top = this.isNumber(this.top) ? parseFloat(this.top) + 'px' : this.top
      }
    },
    watchBottom () {
      if (this.bottom !== undefined) {
        this.scrollToTopStyle.bottom = this.isNumber(this.bottom) ? parseFloat(this.bottom) + 'px' : this.bottom
      }
    },
    watchLeft () {
      if (this.left !== undefined) {
        this.scrollToTopStyle.left = this.isNumber(this.left) ? parseFloat(this.left) + 'px' : this.left
      }
    },
    watchRigth () {
      if (this.right !== undefined) {
        this.scrollToTopStyle.right = this.isNumber(this.right) ? parseFloat(this.right) + 'px' : this.right
      }
    },
    /**
     * 初始化按钮的位置
     */
    initBtnPosition () {
      this.watchTop()
      this.watchBottom()
      this.watchLeft()
      this.watchRigth()
      this.watchPosition()
    },
    initBindScroll () {
      // 监听窗口滚动
      document.onscroll = ((oldScrollTopLength) => {
        const clientHeight = document.documentElement.clientHeight
        return () => {
          const scrollTopLength = document.documentElement.scrollTop || document.body.scrollTop
          // 如果定时器不存在的话就正常计算滚动到顶部的图标是否存在
          if (!this.timer) {
            // 滚动到第二屏就显示
            this.showScrollToTop = scrollTopLength > clientHeight
          }
          // 向下滚动时判断判断是否正在向上滚动，是的话就清除定时器，停在当前位置
          if (scrollTopLength > oldScrollTopLength && this.timer) {
            // 设置这个是因为有时候 clearInterval() 并不能清除这个属性，或许是 vuejs 组件中的属性特殊一点？
            this.timer = clearInterval(this.timer)
          }
          oldScrollTopLength = scrollTopLength
        }
      })(0)
    },
    /**
     * 回到顶部
     */
    scrollToTop () {
      this.timer = setInterval(() => {
        const scrollTopLength = document.documentElement.scrollTop || document.body.scrollTop
        if (scrollTopLength <= 0) {
          this.timer = clearInterval(this.timer)
          this.showScrollToTop = false
        }
        const spend = scrollTopLength / 5
        document.documentElement.scrollTop = document.body.scrollTop = scrollTopLength - spend
      }, 30)
    }
  },
  mounted () {
    this.initBtnPosition()
    this.initBindScroll()
  }
}
</script>
<style scoped>
#vx-scroll-to-top-btn {
  position: fixed;
}
</style>