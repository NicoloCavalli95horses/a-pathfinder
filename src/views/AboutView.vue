<template>
  <div class="wrapper">
    <h2>Project description</h2>
    <p>Three main variables are considered in computing the shortest path:</p>
    <br />
    <ul>
      <li><code>G:</code> distance from the start node to the current node</li>
      <li>
        <code>H:</code> distance from the current node to the target node (destination). It's an
        optimistic measure that does not consider obstacles
      </li>
      <li><code>F: G + H</code> The lower the F cost for a step, the more attractive is</li>
    </ul>
  </div>
</template>

<script setup></script>

<style lang="scss" scoped>
.wrapper {
  display: flex;
  flex-direction: column;
  padding: 22px;
  margin-top: 52px;
  text-align: left;
  ul {
    margin-left: 22px;
  }
}
</style>
