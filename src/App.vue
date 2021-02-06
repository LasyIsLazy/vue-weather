<template>
  <div id="app">
    <Index :sensor="[116.397128,39.916527]"/>
  </div>
</template>

<script>
import Index from './components/WeatherComp.vue/Index.vue'

export default {
  name: 'App',
  components: {
    Index
  }
}
</script>

<style lang="less">
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
  width: 400px;
}
</style>
