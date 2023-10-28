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
          'trampled': indexes.includes(i * size + j),
          'obstacle' : obstacles.includes(i * size + j),
          'active' : active_index == (i * size + j)
        }"
      >
        <div v-if="show_indexes && !obstacles.includes(i * size + j)" class="info"> <span>{{ i * size + j }}</span> </div>
        <div v-if="show_coordinates && neighbors[(i * size + j).toString()]?.x" class="info">
          <span>x:{{ neighbors[(i * size + j).toString()].x.toFixed(1) }}</span>
          <span>y:{{ neighbors[(i * size + j).toString()].y.toFixed(1) }}</span>
        </div>
        <div v-if="show_f && neighbors[(i * size + j).toString()]?.f && !indexes.includes(i * size + j)" class="info">
          <span>{{ (neighbors[(i * size + j).toString()].f / 10).toFixed(1) }}</span>
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
  tot_obstacles: Number,
  show_indexes: Boolean,
  prevent_diagonals: Boolean,
  show_coordinates: Boolean,
})

const emit = defineEmits([
  'solved',
  'loaded',
  'nosolution',
])

// ===========================
// Consts
//===========================
const cells_ref    = reactive( {} );
const start_index  = ref( null );
const active_index = ref( null );
const end_index    = ref( null );
const indexes      = ref( [] );
const obstacles    = ref( [] );


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
  const top          = active_index.value - props.size;
  const top_left     = active_index.value - props.size - 1;
  const top_right    = active_index.value - props.size + 1;
  const left         = active_index.value - 1;
  const right        = active_index.value + 1;
  const bottom       = active_index.value + props.size;
  const bottom_left  = active_index.value + props.size - 1;
  const bottom_right = active_index.value + props.size + 1;

  return {
    [top_left]: function() {
      if (
        props.prevent_diagonals ||
        obstacles.value.includes(top_left) ||
        active_index.value < props.size ||
        !(active_index.value % props.size)
      ) { 
        return null;
      }
      return getNeighborObj(top_left);
    }(),
    [top]: function() {
      if ( 
        obstacles.value.includes(top) ||
        active_index.value < props.size
       ) {
        return null;
      }
      return getNeighborObj(top);
    }(),
    [top_right]: function() {
      if (
        props.prevent_diagonals ||
        obstacles.value.includes(top_right) ||
        active_index.value < props.size ||
        !((active_index.value + 1) % props.size)
      ) {
        return null;
      }
      return getNeighborObj(top_right);
    }(),
    [left]: function() {
      if (
        obstacles.value.includes(left) ||
        !(active_index.value % props.size)
      ) {
        return null;
      }
      return getNeighborObj(left);
    }(),
    [right]: function() {
      if ( 
        obstacles.value.includes(right) ||
        !((active_index.value + 1) % props.size)
      ) {
        return null;
      }
      return getNeighborObj(right);
    }(),
    [bottom_left]: function() {
      if (
        props.prevent_diagonals ||
        obstacles.value.includes(bottom_left) ||
        !(active_index.value % props.size) ||
        active_index.value >= (Math.pow(props.size, 2) - props.size)
      ) {
        return null;
      }
      return getNeighborObj(bottom_left);
    }(),
    [bottom]: function() {
      if (
        obstacles.value.includes(bottom) ||
        active_index.value >= (Math.pow(props.size, 2) - props.size)
      ) {
        return null;
      }
      return getNeighborObj(bottom);
    }(),
    [bottom_right]: function() {
      if (
        props.prevent_diagonals ||
        obstacles.value.includes(bottom_right) ||
        !((active_index.value + 1) % props.size) ||
        active_index.value >= (Math.pow(props.size, 2) - props.size)
      ) {
        return null;
      }
      return getNeighborObj(bottom_right);
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
  return { g, h, f, x, y, index };
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

  if ( !valid_neighbors.length ) {
    emit('nosolution');
  }

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

function setRandomObstacles() {
  const set = new Set();
  const pickRandom = () => {
    const random = randomInt(1, Math.pow(props.size,2));
    (random != start_index.value && random != end_index.value ) ? set.add( random ) : pickRandom();
  }

  for ( let i=0; i < props.tot_obstacles; i++ ) {
    pickRandom();
  }
  return Array.from( set );
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
  obstacles.value    = setRandomObstacles();
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
    obstacles.value    = setRandomObstacles();
    emit('loaded');
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
    border: 1px solid #222;
    border-radius: 4px;
    &.trampled {
      background-color: lightgreen;
    }
    &.active {
      animation: bounce;
      animation-iteration-count: infinite;
      animation-duration: 800ms;
      animation-direction: alternate-reverse;
      background-color: lightgreen;
    }
    &.start {
      background-color: green;
    }
    &.end {
      background-color: brown;
      &.active {
        background-color: green;
      }
    }
    &.obstacle {
      background-color: #444;
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


@keyframes bounce {
  from {
    opacity: 0.5;
  }
  to {
    opacity: 1;
  }
}
</style>
