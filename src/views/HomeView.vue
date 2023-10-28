<template>
  <div class="main">
    <nav>
      <Toggle v-model:active="show.indexes" @click="setToggles('indexes')">Indexes</Toggle>
      <Toggle v-model:active="show.f_value" @click="setToggles('f_value')">F value</Toggle>
    </nav>

    <div class="board-wrapper">
      <Board
        class="board"
        :next="next"
        :size="size"
        :shuffle="shuffle"
        :show_f="show.f_value"
        :show_indexes="show.indexes"
        @solved="idx => { indexes = idx; solved = true; }" />
      <template v-if="solved">
        <div class="c-text info">
          <p>Solved in: {{ indexes.length }} steps</p>
        </div>
      </template>
    </div>

    <div class="fixed-right">
      <Btn class="t-22" :def="true" text="next" :disabled="solved" @click="next++" />
      <Btn class="t-22" text="shuffle" @click="() => { shuffle++; solved = false; }" />
    </div>
  </div>
</template>

<script setup>
//===========================
// Imports
//===========================
import { 
  ref,
  reactive
} from 'vue';

import Btn    from '../components/Btn.vue';
import Board  from '../components/Board.vue';
import Toggle from '../components/Toggle.vue';

//===========================
// Consts
//===========================
const next    = ref( 0 )
const shuffle = ref( 0 )
const size    = ref( 30 )
const indexes = ref( [] )
const solved  = ref( false )

const show = reactive({
  indexes: false,
  f_value: false,
})

//===========================
// Functions
//===========================
function setToggles(value) {
  for (const key in show) {
    if (key != value && key != 'neighborn') {
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
      max-width: 70vh;
      max-height: 70vh;
      min-width: 230px;
      min-height: 230px;
      width: 50vw;
      height: 50vw;
    }
    margin: 12px;
  }
  nav {
    margin-top: 44px;
    width: 100%;
    display: flex;
  }
  .fixed-right {
    position: fixed;
    bottom: 22px;
    right: 22px;
  }
}
</style>
