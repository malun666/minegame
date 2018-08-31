<template>
  <td :class="{'mine-clear': cellData.isClear}" @click="cellClick" :style="{width: size.w + 'px', height: size.h + 'px'}" class="cell">
  <template v-if="cellData.isBoom">
    B
  </template>
  <template v-else>
    <span v-if="cellData.data != 0">{{cellData.data}}</span>
  </template>

  </td>
</template>

<script>
export default {
  name: "cell",
  data() {
    return {};
  },
  methods: {
    cellClick(e) {
      // 点击的鼠标左键
      if (e.button === 0) {
        if (this.cellData.isBoom) {
          // 发送 游戏结束的事件。
          console.log("boom");
        } else {
          // 让当前的单元格：显示数字
          // this.cellData.isClear = true;
          this.$set(this.cellData, "isClear", true);
          console.log("clear");
        }
      }
    }
  },
  props: {
    cellData: {
      type: Object,
      required: true
    },
    size: {
      type: Object,
      required: false,
      default: function() {
        return {
          w: 30,
          h: 30
        };
      }
    }
  }
};
</script>

<style lang="scss" scoped>
td {
  text-align: center;
  vertical-align: middle;
}
.mine-clear {
  background-color: #333 !important;
}
</style>
