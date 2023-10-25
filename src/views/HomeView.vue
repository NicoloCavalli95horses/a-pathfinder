
<template>
  <aside>
    <Toggle v-model:active="show.coordinates" @click="setToggles('coordinates')">
      {{show.coordinates ? 'Hide coordinates' : 'Show coordinates'}}
    </Toggle>
    <Toggle class="t-22" v-model:active="show.indexes" @click="setToggles('indexes')">
      {{show.indexes ? 'Hide indexes' : 'Show indexes'}}
    </Toggle>
    <Toggle class="t-22" v-model:active="show.distances" @click="setToggles('distances')">
      {{show.indexes ? 'Hide distances' : 'Show distances'}}
    </Toggle>
    <Btn class="t-22" :def="true" text="next" :disabled="solved" @click="next++" />
    <Btn class="t-22" text="shuffle" @click="() => { shuffle++; solved = false; }" />
  </aside>
  <div class="chessboard-wrapper">
    <Board
     :next="next"
     :size="size"
     :shuffle="shuffle"
     :show_coordinates="show.coordinates"
     :show_indexes="show.indexes"
     :show_distances="show.distances"
     @solved="(idx) => { indexes = idx; solved = true }"
    />
    <div v-if="solved" class="t-22">Indexes: {{ indexes }}</div>
  </div>
</template>

<script setup>
//===========================
// Imports
//===========================
import { 
  ref,
  reactive,
} from 'vue';

import Btn    from '../components/Btn.vue';
import Board  from '../components/Board.vue';
import Toggle from '../components/Toggle.vue';


//===========================
// Consts
//===========================
const next     = ref( 0 );
const shuffle  = ref( 0 );
const size     = ref( 20 );
const indexes  = ref( [] );
const solved   = ref( false );

const show = reactive({
  indexes:     false,
  distances:   false,
  coordinates: false,
});


//===========================
// Functions
//===========================
function setToggles( value ) {
  for ( const key in show ) {
    if ( key != value ) {
      show[key] = false;
    }
  }
}

</script>

<style lang="scss" scoped>
.chessboard-wrapper {
  position: absolute;
  top: 50%;
  left: 50%;
  transform: translate(-50%, -50%);
  width: 600px;
  height: 600px;
}
aside {
  position: fixed;
  left: 22px;
  top: 50%;
  transform: translate(0, -50%);
  width: 100%;
}

</style>