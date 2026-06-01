<script setup lang="ts">
defineProps<{
  score: number
  highScore: number
}>()

const emit = defineEmits<{
  restart: []
}>()
</script>

<template>
  <div class="overlay">
    <div class="overlay__panel">
      <h1 class="overlay__title">Game Over</h1>
      <div class="overlay__stats">
        <div class="overlay__stat">
          <span class="overlay__stat-label">Score</span>
          <span class="overlay__stat-value overlay__stat-value--score">{{ score }}</span>
        </div>
        <div class="overlay__stat">
          <span class="overlay__stat-label">Best</span>
          <span class="overlay__stat-value">{{ highScore }}</span>
        </div>
      </div>
      <button class="overlay__btn" @click="emit('restart')">
        Play Again
      </button>
      <p class="overlay__hint">Press Space or click to restart</p>
    </div>
  </div>
</template>

<style scoped lang="scss">
$color-primary: #00ff88;
$color-bg: rgba(0, 0, 0, 0.75);

@mixin glowing-border($color, $size: 2px) {
  border: $size solid $color;
  box-shadow: 0 0 15px rgba($color, 0.4);
}

.overlay {
  position: absolute;
  inset: 0;
  display: flex;
  align-items: center;
  justify-content: center;
  background: $color-bg;
  backdrop-filter: blur(6px);
  z-index: 10;

  &__panel {
    text-align: center;
    padding: 40px 60px;
    @include glowing-border($color-primary);
    border-radius: 12px;
    background: rgba(10, 10, 26, 0.95);
  }

  &__title {
    font-size: 42px;
    color: $color-primary;
    margin-bottom: 24px;
    letter-spacing: 3px;
    text-transform: uppercase;
    text-shadow: 0 0 20px rgba($color-primary, 0.5);
  }

  &__stats {
    display: flex;
    gap: 40px;
    justify-content: center;
    margin-bottom: 32px;
  }

  &__stat {
    display: flex;
    flex-direction: column;
    align-items: center;
    gap: 4px;
  }

  &__stat-label {
    font-size: 12px;
    text-transform: uppercase;
    letter-spacing: 1px;
    color: rgba(224, 224, 224, 0.5);
  }

  &__stat-value {
    font-size: 36px;
    font-weight: 700;

    &--score {
      color: $color-primary;
    }
  }

  &__btn {
    padding: 14px 48px;
    font-size: 18px;
    font-weight: 700;
    cursor: pointer;
    border: none;
    border-radius: 8px;
    color: #0a0a1a;
    background: $color-primary;
    letter-spacing: 1px;
    text-transform: uppercase;
    transition: transform 0.15s, box-shadow 0.15s;

    &:hover {
      transform: scale(1.05);
      box-shadow: 0 0 20px rgba($color-primary, 0.6);
    }

    &:active {
      transform: scale(0.97);
    }
  }

  &__hint {
    margin-top: 12px;
    font-size: 12px;
    color: rgba(224, 224, 224, 0.4);
  }
}
</style>
