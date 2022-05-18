<template>
  <div class="loading">
    <tableApp/>
  </div>
</template>

<script>
import tableApp from './components/table.vue'

export default {
  name: 'App',
  components: {
    tableApp
  }
}
</script>

<style>
body{font:1.2em/1.5 sans-serif}
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
.loading::after{content:"https://raw.githubusercontent.com/pcm-dpc/COVID-19/master/dati-json/dpc-covid19-ita-andamento-nazionale.json is loading. Please wait"}
</style>
