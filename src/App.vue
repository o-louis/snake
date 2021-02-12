<template>
  <div id="app">
    <Board :width="board.width" :height="board.height" :color="board.color" />
    <button @click="clearBoard">clear</button>
  </div>
</template>

<script>
import Board from "@/components/Board";
export default {
  name: "App",
  components: {
    Board,
  },
  data() {
    return {
      ctx: null,
      canvas: "",
      snake: {
        size: 20,
        color: "green",
      },
      board: {
        width: 1200,
        height: 600,
        widthRect: 0,
        heightRect: 0,
        color: "black",
      },
    };
  },
  mounted() {
    this.canvas = document.querySelector("#board");
    this.ctx = this.canvas.getContext("2d");

    this.board.widthRect = this.board.width / this.snake.size;
    this.board.heightRect = this.board.height / this.snake.size;

    this.drawSnake();
  },
  methods: {
    clearBoard() {
      this.ctx.clearRect(0, 0, this.canvas.width, this.canvas.height);
      this.ctx.fillStyle = this.board.color;
      this.ctx.fillRect(0, 0, this.canvas.width, this.canvas.height);
      this.drawSnake();
    },
    drawSnake() {
      const position = this.getRandomPosition();
      this.ctx.fillStyle = this.snake.color;
      this.ctx.fillRect(
        position.x,
        position.y,
        this.snake.size,
        this.snake.size
      );
    },
    getRandomPosition() {
      const x = Math.floor(
        Math.random() * (this.board.width - this.snake.size)
      );
      const y = Math.floor(
        Math.random() * (this.board.height - this.snake.size)
      );
      return { x, y };
    },
  },
};
</script>

<style></style>
