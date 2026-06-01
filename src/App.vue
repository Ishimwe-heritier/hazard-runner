<script setup lang="ts">
import { ref, onUnmounted } from 'vue'
import GameBoard from './components/GameBoard.vue'
import GameHUD from './components/GameHUD.vue'
import GameOver from './components/GameOver.vue'

interface Hazard {
  id: number
  x: number
  y: number
  vx: number
  vy: number
  size: number
}

const score = ref(0)
const health = ref(100)
const isGameOver = ref(false)
const difficulty = ref(1)
const survivalTime = ref(0)
const highScore = ref(0)
const hazards = ref<Hazard[]>([])
const playerX = ref(400)
const playerY = ref(300)

let gameInterval: ReturnType<typeof setInterval> | null = null
let moveInterval: ReturnType<typeof setInterval> | null = null
let hazardIdCounter = 0

const canvasWidth = 800
const canvasHeight = 600

function spawnHazard() {
  const size = 12 + Math.random() * 10
  const speed = 0.8 + difficulty.value * 0.3
  const edge = Math.floor(Math.random() * 4)
  let x: number, y: number, vx: number, vy: number

  switch (edge) {
    case 0:
      x = Math.random() * canvasWidth; y = -size
      vx = (Math.random() - 0.5) * speed; vy = speed * (0.6 + Math.random() * 0.4)
      break
    case 1:
      x = canvasWidth + size; y = Math.random() * canvasHeight
      vx = -speed * (0.6 + Math.random() * 0.4); vy = (Math.random() - 0.5) * speed
      break
    case 2:
      x = Math.random() * canvasWidth; y = canvasHeight + size
      vx = (Math.random() - 0.5) * speed; vy = -speed * (0.6 + Math.random() * 0.4)
      break
    default:
      x = -size; y = Math.random() * canvasHeight
      vx = speed * (0.6 + Math.random() * 0.4); vy = (Math.random() - 0.5) * speed
      break
  }

  hazards.value.push({ id: ++hazardIdCounter, x, y, vx, vy, size })
}

function updateHazardPositions() {
  const alive: Hazard[] = []
  for (const h of hazards.value) {
    h.x += h.vx
    h.y += h.vy
    const margin = 50
    if (h.x > -margin && h.x < canvasWidth + margin && h.y > -margin && h.y < canvasHeight + margin) {
      alive.push(h)
    }
  }
  hazards.value = alive
}

function checkPlayerHit(): boolean {
  const px = playerX.value
  const py = playerY.value
  const playerSize = 12
  for (const h of hazards.value) {
    const dx = px - h.x
    const dy = py - h.y
    const dist = Math.sqrt(dx * dx + dy * dy)
    if (dist < playerSize + h.size / 2) {
      return true
    }
  }
  return false
}

let lastHitTime = 0
const invulnerabilityMs = 500

function startGame() {
  score.value = 0
  health.value = 100
  isGameOver.value = false
  difficulty.value = 1
  survivalTime.value = 0
  hazards.value = []
  hazardIdCounter = 0
  playerX.value = 400
  playerY.value = 300
  lastHitTime = 0

  gameInterval = setInterval(() => {
    if (isGameOver.value) return
    score.value++
    survivalTime.value++

    const newDifficulty = Math.floor(score.value / 10) + 1
    if (newDifficulty !== difficulty.value) {
      difficulty.value = newDifficulty
    }

    const spawnCount = Math.min(difficulty.value, 5)
    for (let i = 0; i < spawnCount; i++) {
      spawnHazard()
    }
  }, 1000)

  moveInterval = setInterval(() => {
    if (isGameOver.value) return
    updateHazardPositions()

    const now = Date.now()
    if (now - lastHitTime > invulnerabilityMs && checkPlayerHit()) {
      lastHitTime = now
      health.value = Math.max(0, health.value - 10)
      if (health.value <= 0) {
        isGameOver.value = true
        if (score.value > highScore.value) {
          highScore.value = score.value
        }
        clearInterval(gameInterval!)
        clearInterval(moveInterval!)
        gameInterval = null
        moveInterval = null
      }
    }
  }, 50)
}

function handleRestart() {
  if (gameInterval) clearInterval(gameInterval)
  if (moveInterval) clearInterval(moveInterval)
  gameInterval = null
  moveInterval = null
  startGame()
}

onUnmounted(() => {
  if (gameInterval) clearInterval(gameInterval)
  if (moveInterval) clearInterval(moveInterval)
})

startGame()
</script>

<template>
  <div class="game-container">
    <GameHUD
      :score="score"
      :health="health"
      :survival-time="survivalTime"
      :difficulty="difficulty"
    />
    <GameBoard
      :hazards="hazards"
      :player-x="playerX"
      :player-y="playerY"
      :is-game-over="isGameOver"
      :canvas-width="canvasWidth"
      :canvas-height="canvasHeight"
      @update:playerX="playerX = $event"
      @update:playerY="playerY = $event"
      @restart="handleRestart"
    />
    <GameOver
      v-if="isGameOver"
      :score="score"
      :high-score="highScore"
      @restart="handleRestart"
    />
  </div>
</template>

<style lang="scss">
.game-container {
  position: relative;
  width: 800px;
  height: 600px;
}
</style>
