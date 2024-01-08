<template>
  <div class="game-container">
    <div class="score">
      <h1>Score :: {{ Score }}</h1>
    </div>
    <div class="winner" v-if="WithOutBricks()">
      <h1>YOU WIN!!!</h1>
    </div>
    <div class="game-board">
      <div class="board-row" v-for="(row, rowIdx) in Board" :key="rowIdx">
        <div class="board-cell" :class="{
          'bricks': cell == 1,
          'ball': cell == 2,
          'rocket': cell == 3
        }" v-for="(cell, cellIdx) in row" :key="cellIdx">
          &nbsp;
        </div>
      </div>
    </div>
    <div v-if="GameOver">
      <button class="restart-button" @click="RestartGame">Restart</button>
    </div>
  </div>
</template>

<script setup>
  import { onBeforeMount, onMounted, ref } from 'vue'

  const Board = ref([]);
  
  const Rocket = ref({
    x: 23,
    y: 29,
    length: 8,
    height: 1
  })

  const Ball = ref({
    x: 19 ,
    y: 12,
    r: 1,
    dx: 1,
    dy: -1 
  })

  const Bricks = ref([])

  let interval

  onBeforeMount (()=>
  {
    InitBoard()
    InitBricks()
    window.addEventListener("keydown", onKeyDown)
  })

  onMounted(()=>
  {
    interval = setInterval(() => MoveBall(), 80) 
  })

  function onKeyDown(evt) {
    switch(evt.key) {
      case 'ArrowRight':
        MoveRocket(1)
        break
      case 'ArrowLeft':
        MoveRocket(-1)
        break
    }
  }

  function InitBoard() {
    Board.value = [];

    for (let y = 0; y < 30; y++) {
      let row = [];
      for (let x = 0; x < 39; x++) {
        row.push(0);
      }
      Board.value.push(row);
    }
    DrawRocket()
  }

  function DrawRocket() {
    for (let i = 0; i < Rocket.value.length; i++) {
      const cellX = Rocket.value.x + i
      if (cellX >= 0 && cellX < 39) {
        Board.value [Rocket.value.y][cellX] = 3
      }
    }
  }

  function ClearRocket() {
    for (let i = 0; i < Rocket.value.length; i++) {
      const cellX = Rocket.value.x + i
      if (cellX >= 0 && cellX < 39) {
        Board.value [Rocket.value.y][cellX] = 0
      }
    }
  }

  function MoveRocket(drctn) {
    const newX = Rocket.value.x + drctn
    for (let i = 0; i < Rocket.value.length; i++) {
      if (newX >= 0 && newX + Rocket.value.length <= 39) {
        ClearRocket()
        Rocket.value.x = newX
        DrawRocket()
      }
    }
  }

  function DrawBricks() {
    for (let y = 0; y < Bricks.value.length; y++) {
      for (let x = 0; x < Bricks.value[y].length; x++) {
        if (Bricks.value[y][x] === 1) {
          Board.value[y][x] = 1
        }
      }
    }
  }

  function InitBricks() {
    Bricks.value = [
      [0, 1, 0, 0, 0, 1, 0, 0, 0, 1, 0, 0, 0, 1, 0, 0, 0, 1, 0, 0, 0, 1, 0, 0, 0, 1, 0, 0, 0, 1, 0, 0, 0, 1, 0, 0, 0, 1, 0],
      [1, 0, 1, 0, 1, 0, 1, 0, 1, 0, 1, 0, 1, 0, 1, 0, 1, 0, 1, 0, 1, 0, 1, 0, 1, 0, 1, 0, 1, 0, 1, 0, 1, 0, 1, 0, 1, 0, 0],
      [0, 1, 0, 0, 0, 1, 0, 0, 0, 1, 0, 0, 0, 1, 0, 1, 0, 1, 0, 0, 0, 1, 0, 0, 0, 1, 0, 0, 0, 1, 0, 0, 0, 1, 0, 0, 0, 1, 0],
      [1, 0, 1, 0, 1, 0, 1, 0, 1, 0, 1, 0, 1, 0, 1, 0, 1, 0, 1, 0, 1, 0, 1, 0, 1, 0, 1, 0, 1, 0, 1, 0, 1, 0, 1, 0, 1, 0, 0],
      [0, 1, 0, 0, 0, 1, 0, 0, 0, 1, 0, 0, 0, 1, 0, 1, 0, 1, 0, 0, 0, 1, 0, 0, 0, 1, 0, 0, 0, 1, 0, 0, 0, 1, 0, 0, 0, 1, 0],
      [1, 0, 1, 0, 1, 0, 1, 0, 1, 0, 1, 0, 1, 0, 1, 0, 1, 0, 1, 0, 1, 0, 1, 0, 1, 0, 1, 0, 1, 0, 1, 0, 1, 0, 1, 0, 1, 0, 0],
      [0, 1, 0, 0, 0, 1, 0, 0, 0, 1, 0, 0, 0, 1, 0, 1, 0, 1, 0, 0, 0, 1, 0, 0, 0, 1, 0, 0, 0, 1, 0, 0, 0, 1, 0, 0, 0, 1, 0]
    ]
    DrawBricks()
  }

  function DrawBall() {
    const BallX = Math.floor(Ball.value.x)
    const BallY = Math.floor(Ball.value.y)
    if (BallX >= 0 && BallX < 39 && BallY >= 0 && BallY < 30) {
      Board.value[BallY][BallX] = 2
    }
  }

  function ClearBall() {
    const BallX = Math.floor(Ball.value.x)
    const BallY = Math.floor(Ball.value.y)
    if (BallX >= 0 && BallX < 39 && BallY >= 0 && BallY < 30) {
      Board.value[BallY][BallX] = 0
    }
  }

  function HandleBricksCollision(BrickY, BrickX) {
    Bricks.value[BrickY][BrickX] = 0
    onScore()
    if (Ball.value.dx > 0 && Ball.value.dy > 0) {
      Ball.value.dx = -Ball.value.dx;
    } else if (Ball.value.dx < 0 && Ball.value.dy > 0) {
      Ball.value.dy = -Ball.value.dy;
    } else if (Ball.value.dx > 0 && Ball.value.dy < 0) {
      Ball.value.dy = -Ball.value.dy;
    } else if (Ball.value.dx < 0 && Ball.value.dy < 0) {
      Ball.value.dx = -Ball.value.dx;
    }
  }

  function WithOutBricks() {
    for (let y = 0; y < Bricks.value.length; y++) {
      for (let x = 0; x < Bricks.value[y].length; x++) {
        if (Bricks.value[y][x] === 1) {
          return false
        }
      }
    }
    return true
  }

  const GameOver = ref(false)

  function MoveBall() {
    ClearBall()

    const nextX = Ball.value.x + Ball.value.dx
    const nextY = Ball.value.y + Ball.value.dy

    if (nextX < 0 || nextX >= 38) {
      Ball.value.dx = -Ball.value.dx
    }

    if (nextY < 0) {
      Ball.value.dy = -Ball.value.dy
    }

    if (nextY > 30) {
      GameOver.value = true
    }

    if (WithOutBricks()) {
      GameOver.value = true
    }

    if (
      nextY === Rocket.value.y &&
      nextX >= Rocket.value.x &&
      nextX < Rocket.value.x + Rocket.value.length
    ) {
      Ball.value.dy = -Ball.value.dy
    }

    const BrickX = Math.floor(nextX)
    const BrickY = Math.floor(nextY)

    if (
      BrickY >= 0 &&
      BrickX >= 0 &&
      BrickY < Bricks.value.length &&
      BrickX < Bricks.value[BrickY].length &&
      Bricks.value[BrickY][BrickX] === 1
    ) {
      HandleBricksCollision(BrickY, BrickX)
    }

    if (GameOver.value) {
      clearInterval(interval)
      return
    }

    Ball.value.x = nextX
    Ball.value.y = nextY

    DrawBall()
    DrawRocket()
    DrawBricks()
  }

  const Score = ref(0)

  function onScore () {
    Score.value++
  }

  function RestartGame() {
    clearInterval(interval)
    Score.value = 0
    GameOver.value = false
    InitBoard()
    InitBricks()
    ClearBall()
    ClearRocket()
    Ball.value = {
      x: 19,
      y: 12,
      r: 1,
      dx: 1,
      dy: -1 
    }
    Rocket.value = {
      x: 23,
      y: 29,
      length: 8,
      height: 1
    }
    interval = setInterval(MoveBall, 80)
}
</script>

<style>
body {
  display: flex;
  justify-content: center;
  align-items: center;
  height: 100vh;
  margin-top: 0px;
  }

.game-board {
  border: 2px solid black;
  margin-bottom: 30px;
}

.game-container {
  text-align: center;
  margin-bottom: 50px;
}

.board-cell {
  height: 15px;
  width: 15px; 
  border: none;
  display: inline-block;
}

.ball {
  border-radius: 50%;
  background-color: red;
}

.bricks {
  background-color: green;
}

.rocket {
  background-color: blue;
}

.restart-button {
    font-size: 3em;
    padding: 40px 50px;
    margin-top: 30px;
  }

.score {
    font-size: 1.5em;
  }

.winner {
  font-size: 1.5em;
}
</style>