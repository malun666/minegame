<template>
  <td :class="{'mine-clear': cellData.isClear, 'marked': cellData.isMarked}" @mousedown="cellClick" :style="{width: size.w + 'px', height: size.h + 'px'}" class="cell">
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
      if (this.cellData.isClear) {
        return; // 误操作直接结束
      }
      // 如果是点击了鼠标的邮件，标记为marked状态
      if (e.button === 2) {
        this.$set(this.cellData, "isMarked", !this.cellData.isMarked);
        return;
      }
      // 点击的鼠标左键
      if (e.button === 0) {
        if (this.cellData.isBoom) {
          // 发送 游戏结束的事件。
          console.log("boom");
        } else {
          // 如果标志了小旗子
          if (this.cellData.isMarked) return;

          // 让当前的单元格：显示数字
          // this.cellData.isClear = true;
          // this.$set(this.cellData, "isClear", true);
          // console.log("clear");
          // 把要清理的坐标index发送给父容器。
          this.$emit("clearboom", this.cellData.cellIndex);
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
.marked {
  background-image: url(/flag.png) !important;
  background-size: cover;
}
</style>
