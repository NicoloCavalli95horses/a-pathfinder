<template>
  <div class="wrapper">
    <template v-for="(n,i) in size" :key="n">
      <div
        :ref="el => cells_ref[i.toString() + j.toString()] = el"
        v-for="(m, j) in size"
        :key="m"
        class="cell"
        :class="{
          'start' : start_cell == i.toString() + j.toString(),
          'end' : end_cell == i.toString() + j.toString()
          }"
      >
      <div v-if="Object.keys(cells_rect).length && show_coordinates" class="info">
        <span>x:{{ cells_rect[i.toString() + j.toString()].x / 10 }}</span>
        <span>y:{{ cells_rect[i.toString() + j.toString()].y / 10 }}</span>
      </div>
      <div v-if="show_indexes" class="info">
        <span>{{ i }}</span>
        <span>{{ j }}</span>
      </div>
      </div>
    </template>
  </div>
</template>

<script setup>
//===========================
// Imports
//===========================
import {
  ref,
  reactive,
  nextTick,
  onMounted,
} from "vue";

//===========================
// Props
//===========================
const props = defineProps({
  size: Number,
  show_coordinates: Boolean,
  show_indexes: Boolean,
})

//===========================
// Consts
//===========================
const cells_ref  = reactive({});
const cells_rect = reactive({});

const start_cell = ref('01');
const end_cell = ref('10');

//===========================
// Life cycle
//===========================
onMounted(() => {
  nextTick(() => {
    for ( const [i, cell] of Object.entries( cells_ref ) ) {
      cells_rect[i] = cell.getBoundingClientRect();
    }
  })
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
    &.start {
      background-color: var(--color-primary);
    }
    &.end {
      background-color: var(--color-secondary);
    }
    .info {
      display: grid;
      place-content: center;
      width: 100%;
      height: 100%;
      font-family: monospace;
      font-size: 12px;
    }
  }
}
</style>
