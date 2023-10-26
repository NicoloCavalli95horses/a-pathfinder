<template>
  <div class="input-wrapper">
    <input
      type="range"
      :min="min"
      :max="max"
      @mouseup="emit('value', parseInt(current))"
      @pointerup="emit('value', parseInt(current))"
      v-model="current"
    />
    <div class="value-wrapper" :style="{ left: offset }">
      <div class="cursor"></div>
    </div>
  </div>
</template>

<script setup>
// ==============================
// Import
// ==============================
import { computed, ref } from 'vue'

// ==============================
// Props, emits, variables
// ==============================
const props = defineProps({
  min: Number,
  max: Number,
  start: Number
})

const emit = defineEmits(['value'])
const current = ref(props.start)

// ==============================
// Functions
// ==============================

/**
 *  Normalize <input type="range">
 *  val = (( x - min ) / ( max - min ))  // range from 0 to 1
 */
const offset = computed(() => {
  return Math.round(((current.value - props.min) / (props.max - props.min)) * 100) + '%'
})
</script>

<style lang="scss" scoped>
$spanSize: 50px;
$spanBorder: 2px;
$arrowSize: 8px;
$t-duration: 200ms;

.input-wrapper {
  position: relative;
  overflow: visible;
  margin-top: 30px;

  input[type='range'] {
    cursor: pointer;
    width: 100%;
    height: 20px;
    appearance: none;
    background: #444;
    border-radius: 10px;
    z-index: 1;

    &::-webkit-slider-thumb {
      appearance: none;
      width: 48px;
      height: 48px;
      cursor: pointer;
      z-index: 2;
      position: relative;
    }
  }

  .value-wrapper {
    position: absolute;
    top: 0;
    .cursor {
      display: inline-block;
      width: 20px;
      height: 20px;
      background-color: var(--color-primary);
      border-radius: 50%;
      transition-duration: $t-duration;
    }
  }
}
</style>
