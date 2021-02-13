<template>
  <canvas id="board" ref="board" :width="width" :height="height"></canvas>
</template>

<script>
export default {
  name: "Board",
  props: {
    width: Number,
    height: Number,
    color: String,
  },
  mounted() {
    const canvas = this.$refs.board;
    const ctx = canvas.getContext("2d");
    ctx.fillStyle = this.color;
    ctx.fillRect(0, 0, this.width, this.height);
  },
};
</script>

<style>
#board {
  border: 1px solid#ffb515;
}
</style>
