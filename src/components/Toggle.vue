<template>
  <div class="wrapper">
    <div class="dot-box" @click="() => { active = !active; emit('update') }">
      <div class="dot" :class="{ 'active' : active }" :style="{ 'left' : active ? '50%' : '0%' }"></div>
    </div>
      <slot />
  </div>
</template>

<script setup>
//===========================
// Imports
//===========================
import { ref } from "vue";

//===========================
// Props
//===========================
const emit = defineEmits(['update']);

//===========================
// Consts
//===========================
const active = ref( false );

//===========================
// Life cycle
//===========================
</script>

<style lang="scss" scoped>
$w: 40px;
$p: 5px;
.wrapper {
  width: max-content;
  display: flex;
  align-items: center;
  .dot-box {
    width: calc(2*$w);
    height: $w;
    background-color: #333;
    border-radius: calc(2*$w);
    position: relative;
    cursor: pointer;
    margin-right: 10px;
    .dot {
      position: absolute;
      top: 0%;
      height: calc(100% - ($p *2));
      width: calc(50% - ($p *2));
      margin: $p;
      background: #fff;
      border-radius: 50%;
      transition-duration: 400ms;
      &.active {
        background-color: var(--color-primary);
      }
    }
  }
}
</style>
