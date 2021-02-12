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
        pos: [
          {
            x: 0,
            y: 0,
          },
        ],
      },
      apple: {
        size: 20,
        color: "red",
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
    this.initGame();
    this.initSnake();
    this.initFood();
    this.handleDirection();
  },
  methods: {
    initGame() {
      this.canvas = document.querySelector("#board");
      this.ctx = this.canvas.getContext("2d");

      this.board.widthRect = this.board.width / this.snake.size;
      this.board.heightRect = this.board.height / this.snake.size;
    },
    initSnake() {
      this.snake.pos[0] = this.getRandomPosition();
      this.drawSnake();
    },
    initFood() {
      this.apple.pos = this.getRandomPosition();
      this.drawApple();
    },
    clearBoard() {
      this.ctx.clearRect(0, 0, this.canvas.width, this.canvas.height);
      this.ctx.fillStyle = this.board.color;
      this.ctx.fillRect(0, 0, this.canvas.width, this.canvas.height);
    },
    drawSnake() {
      this.snake.pos.forEach((pos) => {
        this.ctx.fillStyle = this.snake.color;
        this.ctx.fillRect(pos.x, pos.y, this.snake.size, this.snake.size);
      });
    },
    drawApple() {
      this.ctx.fillStyle = this.apple.color;
      this.ctx.fillRect(
        this.apple.pos.x,
        this.apple.pos.y,
        this.apple.size,
        this.apple.size
      );
    },
    handleDirection() {
      const _this = this;
      document.addEventListener("keydown", (e) => {
        let { x, y } = _this.snake.pos[0];
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

        _this.clearBoard();
        _this.snake.pos.unshift({ x, y });
        if (_this.isFoodEaten({ x, y })) {
          this.initFood();
        } else {
          _this.drawApple();
          _this.snake.pos[0] = { x, y };
          _this.snake.pos.pop();
        }
        _this.drawSnake();
      });
    },
    isFoodEaten({ x, y }) {
      return x === this.apple.pos.x && y === this.apple.pos.y;
    },
    getRandomPosition() {
      const x =
        Math.floor(Math.random() * this.board.widthRect) * this.snake.size;
      const y =
        Math.floor(Math.random() * this.board.heightRect) * this.snake.size;

      if (this.snake.pos[0] === { x, y }) this.getRandomPosition();
      if (this.apple.pos === { x, y }) this.getRandomPosition();

      return { x, y };
    },
  },
};
</script>

<style></style>
