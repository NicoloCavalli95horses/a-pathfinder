<template>
  <div class="main">
    <aside>
      <div class="toggles">
        <Toggle
          v-model:active="show.coordinates"
          :disabled="size > 10"
          @click="setToggles('coordinates')"
        >
          {{ show.coordinates ? 'Hide coordinates' : 'Show coordinates' }}
        </Toggle>
        <Toggle v-model:active="show.indexes" :disabled="size > 15" @click="setToggles('indexes')">
          {{ show.indexes ? 'Hide indexes' : 'Show indexes' }}
        </Toggle>
        <Toggle
          v-model:active="show.distances"
          :disabled="size > 7"
          @click="setToggles('distances')"
        >
          {{ show.indexes ? 'Hide distances' : 'Show distances' }}
        </Toggle>
      </div>
      <div class="range">
        <span>Board size</span>
        <InputRange class="grow" :min="4" :max="30" :start="size" @value="(v) => (size = v)" />
      </div>
    </aside>

    <div class="board-wrapper">
      <Board
        class="board"
        :next="next"
        :size="size"
        :shuffle="shuffle"
        :show_coordinates="show.coordinates"
        :show_indexes="show.indexes"
        :show_distances="show.distances"
        @solved="
          (idx) => {
            indexes = idx
            solved = true
          }
        "
      />
      <template v-if="solved">
        <div class="c-text info">
          <p>Solved in: {{ indexes.length }} steps</p>
          <p>Steps: {{ indexes }}</p>
        </div>
      </template>
    </div>

    <div class="fixed-right">
      <Btn class="t-22" :def="true" text="next" :disabled="solved" @click="next++" />
      <Btn
        class="t-22"
        text="shuffle"
        @click="
          () => {
            shuffle++
            solved = false
          }
        "
      />
    </div>
  </div>
</template>

<script setup>
//===========================
// Imports
//===========================
import { ref, reactive } from 'vue'

import Btn from '../components/Btn.vue'
import Board from '../components/Board.vue'
import Toggle from '../components/Toggle.vue'
import InputRange from '../components/InputRange.vue'

//===========================
// Consts
//===========================
const next = ref(0)
const shuffle = ref(0)
const size = ref(7)
const indexes = ref([])
const solved = ref(false)

const show = reactive({
  indexes: false,
  distances: false,
  coordinates: false
})

//===========================
// Functions
//===========================
function setToggles(value) {
  for (const key in show) {
    if (key != value) {
      show[key] = false
    }
  }
}
</script>

<style lang="scss" scoped>
.main {
  width: 100%;
  height: 100vh;
  padding: 22px;

  .board-wrapper {
    .board {
      max-width: 400px;
      max-height: 400px;
      min-width: 230px;
      min-height: 230px;
      width: 50vw;
      height: 50vw;
    }
    margin: 22px;
  }
  aside {
    margin-top: 44px;
    width: 100%;
    .toggles {
      width: 100%;
      display: flex;
      align-items: center;
      justify-content: space-evenly;
      flex-wrap: wrap;
    }
    .range {
      width: 100%;
      display: flex;
      align-items: center;
      justify-content: center;
      span {
        margin-right: 42px;
      }
      .grow {
        max-width: 600px;
        flex-grow: 1;
      }
    }
  }
  .fixed-right {
    position: fixed;
    bottom: 22px;
    right: 22px;
  }
}
</style>
