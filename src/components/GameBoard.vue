<script setup lang="ts">
import { ref, onMounted, onUnmounted } from 'vue'

interface Hazard {
  id: number
  x: number
  y: number
  vx: number
  vy: number
  size: number
}

const props = defineProps<{
  hazards: Hazard[]
  playerX: number
  playerY: number
  isGameOver: boolean
  canvasWidth: number
  canvasHeight: number
}>()

const emit = defineEmits<{
  'update:playerX': [value: number]
  'update:playerY': [value: number]
  restart: []
}>()

const canvasRef = ref<HTMLCanvasElement | null>(null)
const playerSize = 24
const keys: Record<string, boolean> = {}
let animFrameId: number | null = null

function handleKeyDown(e: KeyboardEvent) {
  keys[e.key] = true
  if (props.isGameOver && (e.key === ' ' || e.key === 'Space' || e.key === 'Enter')) {
    emit('restart')
  }
}

function handleKeyUp(e: KeyboardEvent) {
  keys[e.key] = false
}

function gameLoop() {
  const canvas = canvasRef.value
  if (!canvas) return
  const ctx = canvas.getContext('2d')
  if (!ctx) return

  if (!props.isGameOver) {
    const speed = 4
    let newX = props.playerX
    let newY = props.playerY

    if (keys['ArrowLeft'] || keys['a'] || keys['A']) newX -= speed
    if (keys['ArrowRight'] || keys['d'] || keys['D']) newX += speed
    if (keys['ArrowUp'] || keys['w'] || keys['W']) newY -= speed
    if (keys['ArrowDown'] || keys['s'] || keys['S']) newY += speed

    newX = Math.max(playerSize / 2, Math.min(props.canvasWidth - playerSize / 2, newX))
    newY = Math.max(playerSize / 2, Math.min(props.canvasHeight - playerSize / 2, newY))

    if (newX !== props.playerX) emit('update:playerX', newX)
    if (newY !== props.playerY) emit('update:playerY', newY)
  }

  ctx.clearRect(0, 0, props.canvasWidth, props.canvasHeight)

  const gradient = ctx.createRadialGradient(400, 300, 0, 400, 300, 500)
  gradient.addColorStop(0, '#1a1a3e')
  gradient.addColorStop(1, '#0a0a1a')
  ctx.fillStyle = gradient
  ctx.fillRect(0, 0, props.canvasWidth, props.canvasHeight)

  for (let i = 0; i < 60; i++) {
    ctx.fillStyle = `rgba(255, 255, 255, ${0.02 + Math.random() * 0.04})`
    ctx.beginPath()
    ctx.arc(
      10 + Math.random() * (props.canvasWidth - 20),
      10 + Math.random() * (props.canvasHeight - 20),
      0.5 + Math.random() * 1, 0, Math.PI * 2
    )
    ctx.fill()
  }

  for (const hazard of props.hazards) {
    const g = ctx.createRadialGradient(
      hazard.x - hazard.size * 0.3, hazard.y - hazard.size * 0.3, 0,
      hazard.x, hazard.y, hazard.size
    )
    g.addColorStop(0, '#ff6677')
    g.addColorStop(1, '#bb0022')
    ctx.fillStyle = g
    ctx.beginPath()
    ctx.arc(hazard.x, hazard.y, hazard.size, 0, Math.PI * 2)
    ctx.fill()
  }

  const pg = ctx.createRadialGradient(
    props.playerX - 4, props.playerY - 4, 0,
    props.playerX, props.playerY, playerSize / 2
  )
  pg.addColorStop(0, '#88ffcc')
  pg.addColorStop(0.6, '#00cc77')
  pg.addColorStop(1, '#007744')
  ctx.fillStyle = pg
  ctx.beginPath()
  ctx.arc(props.playerX, props.playerY, playerSize / 2, 0, Math.PI * 2)
  ctx.fill()

  ctx.beginPath()
  ctx.arc(props.playerX, props.playerY, playerSize / 2 + 3, 0, Math.PI * 2)
  ctx.strokeStyle = 'rgba(0, 255, 136, 0.3)'
  ctx.lineWidth = 2
  ctx.stroke()

  animFrameId = requestAnimationFrame(gameLoop)
}

onMounted(() => {
  window.addEventListener('keydown', handleKeyDown)
  window.addEventListener('keyup', handleKeyUp)
  animFrameId = requestAnimationFrame(gameLoop)
})

onUnmounted(() => {
  window.removeEventListener('keydown', handleKeyDown)
  window.removeEventListener('keyup', handleKeyUp)
  if (animFrameId !== null) {
    cancelAnimationFrame(animFrameId)
  }
})
</script>

<template>
  <canvas
    ref="canvasRef"
    :width="canvasWidth"
    :height="canvasHeight"
    class="game-board"
  />
</template>

<style scoped lang="scss">
$color-border: #00ff88;

.game-board {
  display: block;
  border: 2px solid $color-border;
  box-shadow: 0 0 20px rgba($color-border, 0.3), inset 0 0 20px rgba($color-border, 0.05);
  border-radius: 4px;
  cursor: crosshair;
}
</style>
