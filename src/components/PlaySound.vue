<template>
  <div class="palysound">
    <audio ref="sd" :src="srcSound"></audio>
  </div>
</template>

<script>
import EventBus from "../eventBus.js";
export default {
  name: "palysound",
  props: {
    srcSound: {
      type: String,
      required: true
    }
  },
  created() {
    EventBus.$on("click-cell", () => {
      this.$refs.sd.play();
    });
  },
  data() {
    return {};
  }
};
</script>

<style lang="scss" scoped>
.palysound {
  height: 0;
  width: 0;
  visibility: hidden;
  position: absolute;
}
</style>
