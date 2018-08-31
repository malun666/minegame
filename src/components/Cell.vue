<template>
  <td :style="{width: size.w + 'px', height: size.h + 'px'}" class="cell">
  <template v-if="cellData.isBoom">
    BOOM
  </template>
  <template v-else>
    {{cellData.data}}
  </template>

  </td>
</template>

<script>
export default {
  name: "cell",
  data() {
    return {};
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
</style>
