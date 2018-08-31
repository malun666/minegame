<template>
  <div id="app">
    <h1>这是AICODER扫雷游戏！</h1>
    <div class="container">
      <div class="main">
        <table class="mine-table">
          <tr v-for="(rowItem, rIndex) of rows" :key="'row_' + rIndex">
            <cell :cell-data="cellArray[rIndex * cols + cIndex]" :size="{w: cellWidth, h: cellHeight}" v-for="(colItem, cIndex) of cols" :key="'col_' + cIndex"></cell>
          </tr>
        </table>
      </div>
      <div class="aside">
        <el-radio-group v-model="level" size="mini">
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
      </div>
    </div>
  </div>
</template>

<script>
import Cell from "./components/Cell";
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
      cellArray: []
    };
  },
  created() {
    // 动态初始化一下：Cell数组，动态随机生成对应的地雷数据。
    let sum = this.cols * this.rows;
    // 计算一共需要随机设置多少个地雷
    let randomBooms = this.getLevelNum() * 0.15 * sum;
    let randomIndexSet = new Set(); // 它的元素不能重复。
    while (randomIndexSet.size < randomBooms) {
      randomIndexSet.add(Math.trunc(Math.random() * sum));
    }

    for (let i = 0; i < sum; i++) {
      let isBoom = randomIndexSet.has(i);
      this.cellArray.push({ isBoom, data: 0, isMarked: false, isClear: false }); // 建议使用这种
      // this.cellArray[i] = {};
      // this.$set(this.cellArray, i, {})  // 这种是可以被监控。
    }
  },
  methods: {
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
    }
  },
  components: {
    cell: Cell
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
