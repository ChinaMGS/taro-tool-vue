

<template>
  <view class="pathway">
    <view
      id="pathway"
      class="movables"
      @touchEnd="onEnd"
      @touchMove="onMove"
    >
      <view class="tips">
        <view
          v-if="isOk"
          style="color: #FFFFFF;"
        >
          验证通过
        </view>
        <view v-else>
          拖动滑块验证
        </view>
      </view>
      <view
        class="track"
        :style="'transform:translateX('+oldx+'px)'"
      />
      <movable-area>
        <movable-view
          id="track"
          direction="horizontal"  
          :disabled="isOk"
          :x="x"
          :class="isOk ? 'active':''"
        />
      </movable-area>
    </view>
    
    <view
      v-if="isOk"
      class="disabled"
    />
  </view>
</template>

<script>
import './index.scss'
export default {
  components:{
    
  },
  data() {
    return {
        x: 0,
        oldx: 0,
        isOk: false,
        size: {}
    }
  },
  async created () {

  },
  mounted(){

  },
  /**
   * 组件的方法列表
   */
  methods: {
    onMove(){
      this.queryShow('#track').then(res2 => {
        this.x = res2.left
      })
    },
    onEnd(){
      let that = this
      // 移动距离
      let move = 0
      this.queryShow('#pathway').then(res1 => {
        this.size.pathway = res1.width;
        this.queryShow('#track').then(res2 => {
          this.size.track = res2.width;
          move = res2.left
          // 最大距离
          let awary = this.size.pathway - this.size.track
          if (move >= awary) {
            this.isOk = true
            this.x = awary
            this.oldx = awary
            console.log("验证通过");
            this.$emit('verifyResult', true)
          } else {
            this.x = 0
            this.oldx = 0
          }
        })
      })
    },
    // 查询页面元素位置
    queryShow(nodeSelect: string) {
      // this.$taro.createSelectorQuery().in(this.$scope)
      const query =
        process.env.TARO_ENV === "weapp" || "h5"
          ? this.$taro.createSelectorQuery()
          : my.createSelectorQuery();
      return new Promise<Rect>((resolve, reject) => {
        query
          .select(nodeSelect)
          .boundingClientRect()
          .exec(rect => {
            // console.log("rect", rect);
            resolve(rect[0]);
          });
      })
    }
  }
}

</script>
