<template>
  <div class="wrapper">
    <template v-for="i in size" :key="i">
      <div v-for="j in size" :key="j" class="cell" />
    </template>
  </div>
</template>

<script setup>
//===========================
// Imports
//===========================
const props = defineProps({
  size: Number,
})

</script>

<style lang="scss" scoped>
.wrapper {
  width: 100%;
  height: 100%;
  margin: 0 auto;
  display: flex;
  flex-wrap: wrap;

  .cell {
    width: calc( 100% / v-bind('props.size'));
    height: calc( 100% / v-bind('props.size'));
    border: 1px solid white;
  }
}
</style>
