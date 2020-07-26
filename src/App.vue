<template>
  <div id="app">
    <petView />
    <Background />
  </div>
</template>

<script>
import petView from './components/petView.vue'

export default {
  name: 'App',
  components: {
    petView
  }
}
</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
</style>
