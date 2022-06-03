<template>
  <Header/>
  <ItemsList/>
</template>

<script>
  import ItemsList from './pages/itemsList.vue'
  import Header from './pages/Header.vue'


  export default {
    name: 'App',
    components: {
      ItemsList,
      Header,
    }
  }
</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  color: #2c3e50;
}
</style>
