<template>
  <Header/>
  <OrdersList/>
</template>

<script>
  import OrdersList from './pages/OrdersList.vue'
  import Header from './pages/Header.vue'


  export default {
    name: 'App',
    components: {
      OrdersList,
      Header
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
