<template>
  <img alt="Vue logo" src="./assets/pokedexLogo.png">
  <!-- <HelloWorld msg="Welcome to Your Vue.js App"/> -->
  <PokemonsDisplay>Hola</PokemonsDisplay>
</template>

<script>
// import HelloWorld from './components/HelloWorld.vue'
import PokemonsDisplay from './components/PokemonsDisplay.vue';
export default {
  name: 'App',
  components: {
    PokemonsDisplay,
  }
}
</script>

<style>
#app {
  background-color: #FAFCF4;
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
  overflow-x: hidden;
}
</style>
