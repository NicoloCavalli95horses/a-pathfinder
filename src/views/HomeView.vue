
<template>
  <div class="chessboard-wrapper">
    <Board :size="10" />
  </div>
</template>

<script setup>
import Board from '../components/Board.vue';

//===========================
// Imports
//===========================
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

</style>