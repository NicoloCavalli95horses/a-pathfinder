<template>
  <nav v-if="isDesktop">
    <Toggle v-model:active="show.indexes" @click="setToggles('indexes')">Show indexes</Toggle>
    <Toggle v-model:active="show.coordinates" @click="setToggles('coordinates')">Show coordinates</Toggle>
    <Toggle v-model:active="show.diagonals">No diagonals</Toggle>
    <Toggle v-model:active="show.f_value" @click="setToggles('f_value')">Show neighbors F value</Toggle>
    <Toggle v-model:active="show.auto">Autocomplete</Toggle>
  </nav>
  <Board
    class="board"
    :next="next"
    :size="size"
    :shuffle="shuffle"
    :show_f="show.f_value"
    :show_coordinates="show.coordinates"
    :prevent_diagonals="show.diagonals"
    :tot_obstacles="tot_obstacles"
    :show_indexes="show.indexes"
    :autocomplete="show.auto"
    @loaded="show.f_value = true"
    @solved="onSolved"
    @nosolution="onNosolution"
  />

  <template v-if="solved || !solvable">
    <div class="fixed-center">
      <h2 v-if="solved">Solved in {{ steps.length }} steps</h2>
      <h2 v-else>No solution found</h2>
    </div>
  </template>

  <div class="fixed-bottom-right">
    <Btn class="t-22" :def="true" text="next" :disabled="solved || !solvable" @click="next++" />
    <Btn class="t-22" text="shuffle" @click="() => { shuffle++; solved = false; solvable = true; }" />
  </div>

  <div v-if="tot_plays" class="fixed-top-right">
    solved: {{ (tot_solved *100 / tot_plays).toFixed(2) }}% <br>
    avg.steps: {{ avg_steps.toFixed(2) }}
  </div>
</template>

<script setup>
//===========================
// Imports
//===========================
import { 
  ref,
  computed,
  reactive,
} from 'vue';

import Btn    from '../components/Btn.vue';
import Board  from '../components/Board.vue';
import Toggle from '../components/Toggle.vue';

//===========================
// Consts
//===========================
const next          = ref( 0 );
const shuffle       = ref( 0 );
const size          = ref( 16 );
const steps         = ref( [] );
const avg_steps     = ref( 0 );
const solved        = ref( false );
const solvable      = ref( true );
const tot_solved    = ref( 0 );
const tot_plays     = ref( 0 );
const tot_obstacles = ref( Math.round(Math.pow(size.value, 2)/2) );
const tot_steps     = ref( 0 );

const show = reactive({
  auto:        false,
  indexes:     false,
  f_value:     false,
  diagonals:   false,
  coordinates: false,
});

const isDesktop = computed( () => window?.innerWidth > 500 );

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

function onSolved(idx) {
  tot_plays.value++;
  tot_solved.value++;
  steps.value = idx;
  solved.value = true;
  tot_steps.value += idx.length;
  avg_steps.value = tot_steps.value / tot_solved.value;
  show.auto = false;
}

function onNosolution() {
  solvable.value = false;
  tot_plays.value++;
  show.auto = false;
}

</script>

<style lang="scss" scoped>
nav {
  position: absolute;
  display: flex;
  flex-wrap: wrap;
  padding: 22px;
}
.board {
  position: absolute;
  top: 50%;
  left: 50%;
  transform: translate(-50%, -50%);
  width: 100vw;
  height: 100vw;
  max-width: 80vh;
  max-height: 80vh;
  padding: 22px;
}

.fixed-bottom-right {
  position: fixed;
  bottom: 22px;
  right: 22px;
}

.fixed-top-right {
  position: fixed;
  top: 22px;
  right: 22px;
}
.fixed-center {
  position: fixed;
  top: 50%;
  left: 50%;
  transform: translate(-50%, -50%);
}

h2 {
  font-size: 26px;
  white-space: nowrap;
  text-shadow: 2px 2px #222;
}

</style>
