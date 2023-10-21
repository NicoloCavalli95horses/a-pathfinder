
<template>
  <aside>
    <Toggle @update="show.coordinates = !show.coordinates">
      {{show.coordinates ? 'Hide coordinates' : 'Show coordinates'}}
    </Toggle>
    <Toggle class="t-22" @update="show.indexes = !show.indexes">
      {{show.indexes ? 'Hide indexes' : 'Show indexes'}}
    </Toggle>
  </aside>
  <div class="chessboard-wrapper">
    <Board
     :size="5"
     :show_coordinates="show.coordinates"
     :show_indexes="show.indexes"
    />
  </div>
</template>

<script setup>
//===========================
// Imports
//===========================
import Toggle from '../components/Toggle.vue';
import Board from '../components/Board.vue';

import { reactive } from 'vue';

//===========================
// Consts
//===========================
const show = reactive({
  indexes: false,
  coordinates: false,
});


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