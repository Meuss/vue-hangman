<template>
  <Game />
</template>

<script setup>
import Game from './components/Game.vue';

// This starter template is using Vue 3 experimental <script setup> SFCs
// Check out https://github.com/vuejs/rfcs/blob/master/active-rfcs/0040-script-setup.md
</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  margin-top: 60px;
}
* {
  box-sizing: border-box;
}
body {
  background-color: #34495e;
  color: #fff;
  display: flex;
  flex-direction: column;
  align-items: center;
  margin: 0;
  overflow: hidden;
}
.figure-container {
  fill: transparent;
  stroke: #fff;
  stroke-width: 4px;
  stroke-linecap: round;
}
.figure-part {
  display: none;
}
</style>
