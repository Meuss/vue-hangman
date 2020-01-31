<template>
  <div id="app">
    <Game />
  </div>
</template>

<script>
import Game from './components/Game.vue';

export default {
  name: 'app',
  components: {
    Game,
  },
};
</script>

<style lang="scss">
#app {
  font-family: 'Avenir', Helvetica, Arial, sans-serif;
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
