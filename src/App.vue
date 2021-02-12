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
        pos: {
          x: 0,
          y: 0,
        },
      },
      board: {
        width: 1200,
        height: 500,
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

    this.snake.pos = this.getRandomPosition();
    this.drawSnake();

    this.handleDirection();
  },
  methods: {
    clearBoard() {
      this.ctx.clearRect(0, 0, this.canvas.width, this.canvas.height);
      this.ctx.fillStyle = this.board.color;
      this.ctx.fillRect(0, 0, this.canvas.width, this.canvas.height);
    },
    drawSnake() {
      this.clearBoard();
      this.ctx.fillStyle = this.snake.color;
      this.ctx.fillRect(
        this.snake.pos.x,
        this.snake.pos.y,
        this.snake.size,
        this.snake.size
      );
    },
    handleDirection() {
      const _this = this;
      document.addEventListener("keydown", (e) => {
        let { x, y } = _this.snake.pos;
        switch (e.key) {
          case "ArrowLeft":
            x = x - this.snake.size >= 0 ? x - this.snake.size : 0;
            break;
          case "ArrowRight":
            x =
              x + this.snake.size < this.board.width ? x + this.snake.size : x;
            break;
          case "ArrowUp":
            y = y - this.snake.size >= 0 ? y - this.snake.size : 0;
            break;
          case "ArrowDown":
            y =
              y + this.snake.size < this.board.height ? y + this.snake.size : y;
            break;
          default:
        }
        _this.snake.pos = { x, y };
        _this.drawSnake();
      });
    },
    getRandomPosition() {
      const x =
        Math.floor(Math.random() * this.board.widthRect) * this.snake.size;
      const y =
        Math.floor(Math.random() * this.board.heightRect) * this.snake.size;
      return { x, y };
    },
  },
};
</script>

<style></style>
