<template>
  <div id="app">
    <h1>这是AICODER扫雷游戏！</h1>
    <div class="container">
      <div class="main">
        <table class="mine-table">
          <tr v-for="(rowItem, rIndex) of rows" :key="'row_' + rIndex">
            <cell @clearboom="clearBoom" :cell-data="cellArray[rIndex * cols + cIndex]" :size="{w: cellWidth, h: cellHeight}" v-for="(colItem, cIndex) of cols" :key="'col_' + cIndex"></cell>
          </tr>
        </table>
      </div>
      <div class="aside">
        <el-radio-group @change="changeLevel" v-model="level" size="mini">
          <el-radio-button label="简单"></el-radio-button>
          <el-radio-button label="中级"></el-radio-button>
          <el-radio-button label="高级"></el-radio-button>
        </el-radio-group>
        <div class="block">
          <span class="demonstration">列数：{{cols}}</span>
          <el-slider v-model="cols" :min="5" :max="100"></el-slider>
        </div>
        <div class="block">
          <span class="demonstration">行数：{{rows}}</span>
          <el-slider v-model="rows" :min="5" :max="100"></el-slider>
        </div>
        <div class="block">
          <el-switch style="display: block" v-model="isCellWHSync" active-color="#13ce66" inactive-color="#ff4949" active-text="是否同步宽高">
          </el-switch>
        </div>
          <div class="block">
          <span class="demonstration">行高：{{cellWidth}}</span>
          <el-slider v-model="cellWidth" :min="5"></el-slider>
        </div>
          <div class="block">
          <span class="demonstration">列高：{{cellHeight}}</span>
          <el-slider v-model="cellHeight" :min="5" ></el-slider>
        </div>
        <div class="block">
          <el-button @click="initCellData" type="warning">重置</el-button>
        </div>
      </div>
    </div>
    <play-sound src-sound="/a.mp3"></play-sound>
    <timer></timer>
  </div>
</template>

<script>
import Cell from "./components/Cell";
import PlaySound from "./components/PlaySound";
import Timer from "./components/Timer";
import EventBus from "./eventBus.js";

import "element-ui/lib/theme-chalk/index.css";

export default {
  name: "app",
  data: function() {
    return {
      level: "简单", // 简单 1， 中级： 2  高级： 3     100 * 0.15 * 2
      cols: 10,
      rows: 10,
      cellWidth: 30,
      cellHeight: 30,
      isCellWHSync: true,
      // { isBoom: false, data: 2, isMarked: false, isClear: false}
      cellArray: [],
      // 是否是重置状态
      isReset: true
    };
  },
  created() {
    this.initCellData();
    document.oncontextmenu = () => {
      return false;
    };

    EventBus.$on("click-cell", () => {
      this.isReset && EventBus.$emit("start-timer");
      this.isReset = false;
    });

    EventBus.$on("boom-end", () => {
      this.boomEnd();
    });
  },
  methods: {
    boomEnd() {
      // 把所有的雷显示。另外就是把所有的非类 clear
      this.cellArray.forEach(item => {
        if (item.isBoom) {
          this.$set(item, "isShowBoom", true);
        } else {
          this.$set(item, "isClear", true);
        }
      });
    },
    // 清理雷区
    clearBoom(index) {
      const innerClearBoom = cIndex => {
        if (cIndex >= 0 && cIndex < this.cellArray.length) {
          let cell = this.cellArray[cIndex];
          if (cell.isMarked || cell.isBoom || cell.isClear) {
            return;
          }
          this.$set(this.cellArray[cIndex], "isClear", true);
          // 如果自己也是 0， 继续清理
          this.clearBoom(cIndex);
        }
      };

      // 如果当前自己不是0，那么就直接把自己清理掉。isCear true
      let cell = this.cellArray[index];
      if (cell.data !== 0) {
        this.$set(cell, "isClear", true);
        return;
      }
      // 如果是0， 那么周围的8个都要clear。
      for (let i = -1; i <= 1; i++) {
        // i -1  0  1
        let startIndex = index - this.cols * i - 1;
        if (index % this.cols > 0) {
          innerClearBoom(startIndex); // index 10
        }
        innerClearBoom(startIndex + 1);
        if (index % this.cols < this.cols - 1) {
          innerClearBoom(startIndex + 2);
        }
      }
      // 如果周围的元素也是0， 那么此元素的周围也要清理。
    },
    changeLevel() {
      this.initCellData();
    },
    // 初始化数组数据
    initCellData() {
      // 设置当前重置状态
      this.isReset = true;
      // 动态初始化一下：Cell数组，动态随机生成对应的地雷数据。
      let sum = this.cols * this.rows;
      // 计算一共需要随机设置多少个地雷
      let randomBooms = this.getLevelNum() * 0.15 * sum;
      let randomIndexSet = new Set(); // 它的元素不能重复。
      while (randomIndexSet.size < randomBooms) {
        randomIndexSet.add(Math.trunc(Math.random() * sum));
      }

      this.cellArray = []; // 先进行清空数组中的元素。

      for (let i = 0; i < sum; i++) {
        let isBoom = randomIndexSet.has(i);
        let data = this.getBoomsNum(i, randomIndexSet);
        this.cellArray.push({
          isBoom,
          data,
          isShowBoom: false,
          isMarked: false,
          isClear: false,
          cellIndex: i
        }); // 建议使用这种
        // this.cellArray[i] = {};
        // this.$set(this.cellArray, i, {})  // 这种是可以被监控。
      }
    },
    getBoomsNum(index, boomsSet) {
      let count = 0; // 周围的总共炸弹数据
      for (let i = -1; i <= 1; i++) {
        let startIndex = index - i * this.cols - 1;
        if (index % this.cols > 0) {
          count += boomsSet.has(startIndex);
        }
        count += boomsSet.has(startIndex + 1);
        if (index % this.cols < this.cols - 1) {
          count += boomsSet.has(startIndex + 2);
        }
      }
      return count;
    },
    getLevelNum() {
      if (this.level === "简单") {
        return 1;
      } else if (this.level === "中级") {
        return 2;
      } else {
        return 3; // 高级
      }
    }
  },
  watch: {
    cellWidth(newVal) {
      this.isCellWHSync && (this.cellHeight = newVal);
    },
    cellHeight(newVal) {
      this.isCellWHSync && (this.cellWidth = newVal);
    },
    rows() {
      this.initCellData();
    },
    cols() {
      this.initCellData();
    }
  },
  components: {
    cell: Cell,
    "play-sound": PlaySound,
    timer: Timer
  }
};
</script>

<style lang="scss">
.block {
  border-top: 1px solid #c09;
  margin-top: 5px;
}
#app {
  h1 {
    text-align: center;
  }
  .container {
    display: flex;
    & > div {
      border: 1px solid #ccc;
    }
  }
  .main {
    flex: 5 0 500px;
    min-height: 300px;
  }
  .aside {
    flex: 2 0 200px;
    min-height: 300px;
  }
  .mine-table {
    border-collapse: collapse;

    td {
      width: 30px;
      height: 30px;
      border: 1px solid #c09;
      background-color: aliceblue;
    }
  }
}
</style>
