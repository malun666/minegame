<template>
  <td :class="{'mine-clear': cellData.isClear, 'marked': cellData.isMarked}" @mousedown="cellClick" :style="{width: size.w + 'px', height: size.h + 'px'}" class="cell">
    <span :style="{color: getNumColor(cellData.data)}" v-if="cellData.data != 0 && cellData.isClear">{{cellData.data}}</span>
  </td>
</template>

<script>
import EventBus from "../eventBus.js";
export default {
  name: "cell",
  data() {
    return {};
  },
  methods: {
    getNumColor(num) {
      switch (num) {
        case 1:
          return "#fff";
        case 2:
          return "#c6a";
        case 3:
          return "#08a";
        case 4:
          return "#0a2";
        case 5:
          return "#a20";
        case 6:
          return "#f30";
        default:
          return "#f90";
      }
    },
    cellClick(e) {
      if (this.cellData.isClear) {
        return; // 误操作直接结束
      }

      // 如果是点击了鼠标的邮件，标记为marked状态
      if (e.button === 2) {
        EventBus.$emit("click-cell");
        this.$set(this.cellData, "isMarked", !this.cellData.isMarked);
        return;
      }
      // 点击的鼠标左键
      if (e.button === 0) {
        if (this.cellData.isBoom) {
          EventBus.$emit("boom-end");
          // 发送 游戏结束的事件。
        } else {
          // 如果标志了小旗子
          if (this.cellData.isMarked) return;
          EventBus.$emit("click-cell");
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
