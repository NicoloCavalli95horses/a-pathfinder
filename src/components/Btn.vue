<template>
  <button :class="{ disabled: disabled, active: def }">{{ text }}</button>
</template>

<script setup>
// ==============================
// Props
// ==============================
defineProps({
  text: String,
  def: Boolean,
  disabled: Boolean
})
</script>

<style lang="scss" scoped>
button {
  width: max-content;
  text-decoration: none;
  border: none;
  padding: 1rem 2rem;
  border-radius: 25px;
  letter-spacing: 0.4rem;
  cursor: pointer;
  transition-duration: 400ms;
  background-color: transparent;
  text-transform: uppercase;
  user-select: none;
  color: #eee;

  &:hover {
    transition-duration: 400ms;
    filter: brightness(150%);
  }
  &.disabled {
    opacity: 0.5;
    touch-action: none;
    pointer-events: none;
    &:hover {
      filter: none;
    }
  }
  &.active {
    background-color: var(--color-primary);
    color: #222;
  }
}
</style>
