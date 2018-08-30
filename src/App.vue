<template>
  <div id="app">
    <h1>这是AICODER扫雷游戏！</h1>
    <div class="container">
      <div class="main">main</div>
      <div class="aside">aside
        <el-radio-group v-model="level">
          <el-radio-button label="简单"></el-radio-button>
          <el-radio-button label="中级"></el-radio-button>
          <el-radio-button label="高级"></el-radio-button>
        </el-radio-group>
      </div>
    </div>
  </div>
</template>

<script>
import "element-ui/lib/theme-chalk/index.css";

export default {
  name: "app",
  data: function() {
    return {
      level: "简单"
    };
  },
  components: {}
};
</script>

<style lang="scss">
#app {
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
}
</style>
