<template>
  <div>
    <h2 v-for="i in 100"
        :key="i">
      第 {{i}} 行文字
    </h2>
    <vx-scroll-to-top>
      <!-- 这里面的内容随你定义，是上拉按钮要显示的样子 -->
      <v-btn color="primary"
             fab>
        <v-icon>vertical_align_top</v-icon>
      </v-btn>
    </vx-scroll-to-top>
  </div>
</template>

<script>
import VxScrollToTop from '@/components/common/VxScrollToTop'

export default {
  components: {
    VxScrollToTop
  }
}
</script>