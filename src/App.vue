<template>
  <div id="app">
    <HotEntry />
  </div>
</template>

<script>
import HotEntry from './components/HotEntry.vue'

export default {
  name: 'app',
  components: {
    HotEntry
  }
}
</script>

<style>
#app {
  font-family: 'Avenir', Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
}
</style>
