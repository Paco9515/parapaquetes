<template>
  <Header/>
  <Listado/>
</template>

<script>
  import Listado from './pages/Listado.vue'
  import Header from './pages/Header.vue'


  export default {
    name: 'App',
    components: {
    Listado,
    Header
}
  }
</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  width: 80%;
  margin-left: 10%;
  color: #2c3e50;
}
</style>
