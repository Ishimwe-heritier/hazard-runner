<script setup lang="ts">
import { computed } from 'vue'

const props = defineProps<{
  score: number
  health: number
  survivalTime: number
  difficulty: number
}>()

const healthPercent = computed(() => `${Math.max(0, props.health)}%`)

const healthColor = computed(() => {
  if (props.health > 60) return '#00ff88'
  if (props.health > 30) return '#ffaa00'
  return '#ff3355'
})

const formattedTime = computed(() => {
  const mins = Math.floor(props.survivalTime / 60)
  const secs = props.survivalTime % 60
  return `${mins}:${secs.toString().padStart(2, '0')}`
})
</script>

<template>
  <div class="hud">
    <div class="hud__item">
      <span class="hud__label">Score</span>
      <span class="hud__value hud__value--score">{{ score }}</span>
    </div>
    <div class="hud__item hud__item--health">
      <span class="hud__label">Health</span>
      <div class="hud__health-bar">
        <div
          class="hud__health-fill"
          :style="{ width: healthPercent, backgroundColor: healthColor }"
        />
      </div>
      <span class="hud__value">{{ health }}</span>
    </div>
    <div class="hud__item">
      <span class="hud__label">Time</span>
      <span class="hud__value">{{ formattedTime }}</span>
    </div>
    <div class="hud__item">
      <span class="hud__label">Level</span>
      <span class="hud__value">{{ difficulty }}</span>
    </div>
  </div>
</template>

<style scoped lang="scss">
$color-primary: #00ff88;
$color-warning: #ffaa00;
$color-danger: #ff3355;
$color-hud-bg: rgba(10, 10, 26, 0.85);

@mixin glass-panel {
  background: $color-hud-bg;
  backdrop-filter: blur(4px);
  border-radius: 8px;
  padding: 10px 16px;
}

.hud {
  display: flex;
  gap: 16px;
  padding: 12px 16px;
  margin-bottom: 8px;
  @include glass-panel;

  &__item {
    display: flex;
    align-items: center;
    gap: 8px;

    &--health {
      flex: 1;
    }
  }

  &__label {
    font-size: 11px;
    text-transform: uppercase;
    letter-spacing: 1px;
    color: rgba(224, 224, 224, 0.6);
    font-weight: 600;
  }

  &__value {
    font-size: 18px;
    font-weight: 700;
    font-variant-numeric: tabular-nums;

    &--score {
      color: $color-primary;
    }
  }

  &__health-bar {
    width: 120px;
    height: 14px;
    background: rgba(255, 255, 255, 0.1);
    border-radius: 7px;
    overflow: hidden;
  }

  &__health-fill {
    height: 100%;
    border-radius: 7px;
    transition: width 0.3s ease, background-color 0.3s ease;
  }
}
</style>
