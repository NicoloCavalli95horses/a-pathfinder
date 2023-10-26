<template>
  <div class="wrapper">
    <template v-for="(n, i) in size" :key="n">
      <div
        :ref="(el) => (cells_ref[i * size + j] = el)"
        v-for="(m, j) in size"
        :key="m"
        class="cell"
        :class="{
          start: start_index == i * size + j,
          end: end_index == i * size + j,
          active: indexes.includes(i * size + j)
        }"
      >
        <div v-if="Object.keys(cells_rect).length && show_coordinates" class="info">
          <span>x:{{ cells_rect[i * size + j].x / 10 }}</span>
          <span>y:{{ cells_rect[i * size + j].y / 10 }}</span>
        </div>
        <div v-if="show_indexes" class="info">
          <span>{{ i * size + j }}</span>
        </div>
        <div v-if="show_distances" class="info">
          <span>f: {{ distances[i * size + j].f.toFixed(2) }}</span>
          <span>g: {{ distances[i * size + j].g.toFixed(2) }}</span>
          <span>h: {{ distances[i * size + j].h.toFixed(2) }}</span>
        </div>
      </div>
    </template>
  </div>
</template>

<script setup>
//===========================
// Imports
//===========================
import { ref, reactive, computed, nextTick, onMounted, watch } from 'vue'

//===========================
// Props
//===========================
const props = defineProps({
  size: Number,
  show_coordinates: Boolean,
  show_indexes: Boolean,
  show_distances: Boolean,
  next: Number,
  shuffle: Number
})

const emit = defineEmits(['solved'])

//===========================
// Consts
//===========================
const cells_ref = reactive({})
const cells_rect = reactive({})
const start_index = ref(0)
const end_index = ref(randomInt(1, props.size * props.size))
const active_index = ref(start_index.value)
const indexes = ref([])

const distances = computed(() => {
  const obj = {}
  if (!Object.keys(cells_rect).length) {
    return obj
  }
  for (const [key, cell] of Object.entries(cells_rect)) {
    const g = getG(cell)
    const h = getH(cell)
    const f = g + h
    obj[key] = { ...cell, g, h, f, index: key }
  }
  return obj
})

const neighbors = computed(() => {
  const obj = {
    top_left: getTopBottom({ left: true, top: true }),
    top: getTopBottom({ top: true }),
    top_right: getTopBottom({ right: true, top: true }),
    left: getCenter({ left: true }),
    right: getCenter({ right: true }),
    bottom_left: getTopBottom({ left: true, bottom: true }),
    bottom: getTopBottom({ bottom: true }),
    bottom_right: getTopBottom({ right: true, bottom: true })
  }
  return obj
})

//===========================
// Functions
//===========================
function getG(cell) {
  return getDistance({ start: cells_rect[start_index.value], end: cell })
}

function getH(cell) {
  return getDistance({ start: cell, end: cells_rect[end_index.value] })
}

function getDistance({ start, end }) {
  return Math.sqrt(Math.pow(end.x - start.x, 2) + Math.pow(end.y - start.y, 2))
}

function getTopBottom({ left, right, top, bottom } = false) {
  const acc = left ? -1 : right ? 1 : 0
  const dir = top ? -1 : bottom ? 1 : 0
  const i = active_index.value + acc + props.size * dir
  return i >= 0 && i <= Object.keys(distances.value).length ? distances.value[i] : null
}

function getCenter({ left, right } = false) {
  const acc = left ? -1 : right ? 1 : 0
  const i = active_index.value + acc
  return i >= 0 && i <= Object.keys(distances.value).length ? distances.value[i] : null
}

function getIndex() {
  let min = 9999
  let best_index = null
  const valid_neighbors = Object.values(neighbors.value).filter((val) => val != null)
  for (const n of valid_neighbors) {
    if (
      n?.f < min &&
      !indexes.value.includes(parseInt(n?.index)) &&
      n?.index != start_index.value &&
      n?.index != active_index.value
    ) {
      min = n.f
      best_index = parseInt(n.index)
    }
  }
  indexes.value.push(best_index)
  active_index.value = best_index
  if (active_index.value == end_index.value) {
    emit('solved', indexes.value)
  }
}

function randomInt(min, max) {
  return Math.floor(Math.random() * (max - min)) + min
}

function getCellRect() {
  nextTick(() => {
    for (const [i, cell] of Object.entries(cells_ref)) {
      const rect = cell.getBoundingClientRect()
      cells_rect[i] = {
        x: rect.x,
        y: rect.y
      }
    }
  })
}

//===========================
// Watcher
//===========================
watch(
  () => props.next,
  () => {
    getIndex()
  }
)

watch(
  () => props.shuffle,
  () => {
    getCellRect()
    end_index.value = randomInt(1, props.size * props.size)
    indexes.value = []
    active_index.value = start_index.value
  }
)

//===========================
// Life cycle
//===========================
onMounted(() => {
  getCellRect()
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
      background-color: var(--color-primary);
    }
    &.end {
      background-color: var(--color-secondary);
    }
    &.active {
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
