<template>
  <div id="app">
    <h1>SNAKE</h1>

    <ul class="colors">
      <li><p class="color-title">Change the color of the food:</p></li>
      <li
        v-for="(color, index) in availableColors"
        :key="index"
        @click="updateColor(color)"
      >
        <span :style="{ background: color }"></span>
      </li>
    </ul>
    <section>
      <p class="best score">Best Score: {{ bestScore }}</p>
      <p class="score">Score: {{ score }}</p>
    </section>
    <Board :width="board.width" :height="board.height" :color="board.color" />
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
      speed: 300,
      speedCoefficient: 50,
      nbFoodEaten: 0,
      score: 0,
      scores: [0],
      snake: {
        size: 20,
        color: "#ff9a00",
        pos: [],
      },
      food: {
        color: "#ff5700",
        pos: {},
      },
      board: {
        width: 1000,
        height: 400,
        widthRect: 0,
        heightRect: 0,
        color: "#032535",
      },
      direction: "ArrowRight",
      timer: null,
      start: false,
      availableColors: ["red", "yellow", "orange", "brown"],
    };
  },
  mounted() {
    this.canvas = document.querySelector("#board");
    this.ctx = this.canvas.getContext("2d");

    this.scores = this.getLocalScores();
    this.board.widthRect = this.board.width / this.snake.size;
    this.board.heightRect = this.board.height / this.snake.size;

    this.initGame();
    this.getDirection();
    this.setSpeed(this.game);
  },
  computed: {
    bestScore() {
      return Math.max(...this.scores);
    },
  },
  methods: {
    initGame() {
      this.snake.pos = [{ x: 0, y: 0 }];
      this.food.pos = { x: 0, y: 0 };
      this.initSnake();
      this.initFood();
    },
    initSnake() {
      this.snake.pos[0] = this.getRandomPosition();
      this.drawSnake();
    },
    initFood() {
      this.food.pos = this.getRandomPosition();
      this.drawfood();
    },
    drawSnake() {
      this.snake.pos.forEach((pos) => {
        this.ctx.fillStyle = this.snake.color;
        this.ctx.fillRect(pos.x, pos.y, this.snake.size, this.snake.size);

        this.ctx.strokeStyle = "black";
        this.ctx.strokeRect(pos.x, pos.y, this.snake.size, this.snake.size);
      });
    },
    drawfood() {
      const radius = this.snake.size / 2;
      this.ctx.beginPath();
      this.ctx.arc(
        this.food.pos.x + radius,
        this.food.pos.y + radius,
        radius,
        0,
        2 * Math.PI,
        false
      );
      this.ctx.fillStyle = this.food.color;
      this.ctx.fill();
    },
    clearBoard() {
      this.ctx.clearRect(0, 0, this.canvas.width, this.canvas.height);
      this.ctx.fillStyle = this.board.color;
      this.ctx.fillRect(0, 0, this.canvas.width, this.canvas.height);
    },
    game() {
      if (this.start) {
        const lastPos = this.snake.pos[0];
        const { x, y } = this.updatePos(lastPos);

        if (lastPos.x !== x || lastPos.y !== y) {
          this.snake.pos.unshift({ x, y });
          this.clearBoard();
          if (this.isFoodEaten({ x, y })) {
            this.score += 20;
            this.nbFoodEaten++;
            this.speedUp();
            this.initFood();
          } else {
            this.drawfood();
            this.snake.pos[0] = { x, y };
            this.snake.pos.pop();
          }
          this.drawSnake();
        } else {
          this.restartGame();
        }
      }
    },
    updatePos({ x, y }) {
      switch (this.direction) {
        case "ArrowLeft":
          x = x - this.snake.size >= 0 ? x - this.snake.size : 0;
          break;
        case "ArrowRight":
          x = x + this.snake.size < this.board.width ? x + this.snake.size : x;
          break;
        case "ArrowUp":
          y = y - this.snake.size >= 0 ? y - this.snake.size : 0;
          break;
        case "ArrowDown":
          y = y + this.snake.size < this.board.height ? y + this.snake.size : y;
          break;
      }
      return { x, y };
    },
    getDirection() {
      const _this = this;
      document.addEventListener("keydown", (e) => {
        if (!_this.start) _this.start = true;
        _this.direction = e.key;
      });
    },
    getRandomPosition() {
      const x =
        Math.floor(Math.random() * this.board.widthRect) * this.snake.size;
      const y =
        Math.floor(Math.random() * this.board.heightRect) * this.snake.size;

      if (this.snake.pos[0].x === x && this.snake.pos[0].y === y)
        this.getRandomPosition();
      if (this.food.pos.x === x && this.food.pos.y === y)
        this.getRandomPosition();

      return { x, y };
    },
    setSpeed(fn) {
      this.timer = setInterval(fn, this.speed);
    },
    speedUp() {
      if (this.nbFoodEaten === 3 && this.speed - this.speedCoefficient >= 0) {
        this.speed -= this.speedCoefficient;
        if (this.speed >= 101) this.speedCoefficient = 20;
        this.nbFoodEaten = 0;
        clearInterval(this.timer);
        this.setSpeed(this.game);
      }
    },
    isFoodEaten({ x, y }) {
      return x === this.food.pos.x && y === this.food.pos.y;
    },
    restartGame() {
      this.saveScore();
      this.start = false;
      this.score = 0;
      this.speed -= 300;
      this.nbFoodEaten = 0;
      clearInterval(this.timer);
      this.clearBoard();
      this.initGame();
      this.setSpeed(this.game);
    },
    getLocalScores() {
      const scores = localStorage.scores;
      if (scores) return JSON.parse(scores);
      return [0];
    },
    saveScore() {
      this.scores.push(this.score);
      localStorage.setItem("scores", JSON.stringify([...new Set(this.scores)]));
    },
    updateColor(color) {
      this.food.color = color;
      this.drawfood();
    },
  },
};
</script>

<style>
@import url("https://fonts.googleapis.com/css2?family=Open+Sans:wght@300;400&display=swap");
* {
  padding: 0;
  margin: 0;
  font-family: "Open Sans", sans-serif;
  box-sizing: border-box;
}

li {
  list-style: none;
  list-style-type: none;
}

body {
  background: #032535;
  padding: 30px 0;
  margin: 0 auto;
  max-width: 1000px;
  width: 100%;
  overflow: hidden;
}

h1 {
  color: #ffb515;
  text-align: center;
  margin-bottom: 50px;
  font-size: 50px;
  letter-spacing: 13px;
}

section {
  display: flex;
  justify-content: space-between;
}

.score {
  font-size: 30px;
  color: white;
  margin-bottom: 24px;
}

.best {
  color: #ffb515;
  margin-bottom: 12px;
}

ul {
  display: flex;
}

.colors li {
  margin-right: 10px;
  cursor: pointer;
}

.colors span {
  width: 20px;
  height: 20px;
  display: block;
}

.color-title {
  color: white;
}
</style>
