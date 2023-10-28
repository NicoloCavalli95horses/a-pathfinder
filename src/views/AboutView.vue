<template>
  <div class="wrapper">
    <h2>Project description</h2>
    <p>The A* algorithm is an informed search algorithm, or a best-first search, meaning that it is formulated in terms of weighted graphs: starting from a specific starting node of a graph, it aims to find a path to the given goal node having the smallest cost (least distance travelled, shortest time, etc.). It does this by maintaining a tree of paths originating at the start node and extending those paths one edge at a time until its termination criterion is satisfied. Three main variables are considered in computing the shortest path:</p>
    <ul>
      <li><code>G</code>distance from the start node to the current node</li>
      <li>
        <code>H</code>distance from the current node to the target node (destination). It's an
        optimistic measure that does not consider obstacles
      </li>
      <li><code>F</code>G + H</li>
    </ul>
    <p>The lower the F cost for a step, the more attractive the cell is</p>
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
    li {
      margin: 8px 0;
    }
  }
  code {
    background-color: #333;
    padding: 6px 8px;
    border-radius: 10px;
    text-align: center;
    margin-right: 6px;
  }
}
</style>
