<template>
  <div class="wrapper">
    <template v-for="(n, i) in size" :key="n">
      <div
        :ref="el => (cells_ref[i * size + j] = el)"
        v-for="(m, j) in size"
        :key="m"
        class="cell"
        :class="{
          'start': start_index == (i * size + j),
          'end': end_index == (i * size + j),
          'active': indexes.includes(i * size + j),
        }"
      >
        <div v-if="show_indexes" class="info"> <span>{{ i * size + j }}</span> </div>
        <div v-if="show_f && neighbors[(i * size + j).toString()]?.f && !indexes.includes(i * size + j)" class="info">
          <span>{{ (neighbors[(i * size + j).toString()].f / 10).toFixed(2) }}</span>
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
  watch,
  reactive, 
  computed,
  onMounted,
  nextTick,
} from 'vue'

//===========================
// Props
//===========================
const props = defineProps({
  next: Number,
  size: Number,
  shuffle: Number,
  show_f: Boolean,
  show_indexes: Boolean,
})

const emit = defineEmits(['solved'])

// ===========================
// Consts
//===========================
const cells_ref    = reactive( {} );
const start_index  = ref( null );
const active_index = ref( null );
const end_index    = ref( null );
const indexes      = ref( [] );


const start_cell = computed(() => {
  if ( !Object.keys(cells_ref).length ) { return {} };
  const { x, y } = cells_ref[ start_index.value ].getBoundingClientRect();
  return { x, y };
});
const end_cell   = computed(() => {
  if ( !Object.keys(cells_ref).length ) { return {} };
  const { x, y } = cells_ref[ end_index.value ].getBoundingClientRect();
  return { x, y };
});

const neighbors = computed(() => {
  if ( !Object.keys(cells_ref).length ) { 
    return {}
  }
  return {
    [active_index.value - props.size - 1]: function() { // top left
      if ( active_index.value < props.size || !(active_index.value % props.size) ) { 
        return null;
      }
      return getNeighborObj(active_index.value - props.size - 1);
    }(),
    [active_index.value - props.size]: function() { // top
      if ( active_index.value < props.size ) {
        return null;
      }
      return getNeighborObj(active_index.value - props.size);
    }(),
    [active_index.value - props.size + 1]: function() { // top-right
      if ( active_index.value < props.size || !((active_index.value + 1) % props.size) ) {
        return null;
      }
      return getNeighborObj(active_index.value - props.size + 1);
    }(),
    [active_index.value - 1]: function() { // left
      if ( !(active_index.value % props.size) ) {
        return null;
      }
      return getNeighborObj(active_index.value - 1);
    }(),
    [active_index.value + 1]: function() { // right
      if ( !((active_index.value + 1) % props.size) ) {
        return null;
      }
      return getNeighborObj(active_index.value + 1);
    }(),
    [active_index.value + props.size - 1]: function() { // bottom left
      if ( !(active_index.value % props.size) || active_index.value >= (Math.pow(props.size, 2) - props.size) ) {
        return null;
      }
      return getNeighborObj(active_index.value + props.size - 1);
    }(),
    [active_index.value + props.size]: function() { // bottom
      if ( active_index.value >= (Math.pow(props.size, 2) - props.size) ) {
        return null;
      }
      return getNeighborObj(active_index.value + props.size);
    }(),
    [active_index.value + props.size + 1]: function() { // bottom right
      if ( !((active_index.value + 1) % props.size) || active_index.value >= (Math.pow(props.size, 2) - props.size) ) {
        return null;
      }
      return getNeighborObj(active_index.value + props.size + 1);
    }(),
  }
});


//===========================
// Functions
//===========================
function getNeighborObj( index ){
  const { x, y } = cells_ref[ index ].getBoundingClientRect();
  const g = getG({ x, y });
  const h = getH({ x, y });
  const f = g + h;
  return { g, h, f, index };
}

function getG( cell ) {
  return getDistance({ start: start_cell.value, end: cell });
}

function getH( cell ) {
  return getDistance({ start: cell, end: end_cell.value });
}

function getDistance({ start, end }) {
  return Math.sqrt(Math.pow(end.x - start.x, 2) + Math.pow(end.y - start.y, 2));
}

function moveToNextCell() {
  let min = 9999;
  let best_index = null;
  const valid_neighbors = Object.values(neighbors.value).filter((val) => 
    val != null
      && val.index != start_index.value
        && val.index != active_index.value
          && !indexes.value.includes(val.index));

  for (const n of valid_neighbors) {
    if ( n.f < min ) {
      min = n.f;
      best_index = parseInt(n.index);
    }
  }
  indexes.value.push(best_index);
  active_index.value = best_index;
  if (active_index.value == end_index.value) {
    emit('solved', indexes.value)
  }
}

function randomInt(min, max) {
  return Math.floor(Math.random() * (max - min)) + min
}

//===========================
// Watcher
//===========================
watch(() => props.next, () => {
  moveToNextCell();
});

watch(() => props.shuffle, () => {
  start_index.value  = 0;
  active_index.value = start_index.value;
  end_index.value    = randomInt(1, props.size * props.size);
  indexes.value      = [ start_index.value ];
});

//===========================
// Life cycle
//===========================
onMounted(() => {
  nextTick(() => {
    start_index.value  = 0;
    active_index.value = start_index.value;
    end_index.value    = randomInt(1, props.size * props.size);
    indexes.value      = [ start_index.value ];
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
    width: calc(100% / v-bind('props.size'));
    height: calc(100% / v-bind('props.size'));
    border: 1px solid white;
    &.start {
      background-color: lightgreen;
    }
    &.end {
      background-color: var(--color-secondary);
    }
    &.active {
      background-color: lightgreen;
    }
    &.neighbor {
      background-color: var(--color-primary);
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
