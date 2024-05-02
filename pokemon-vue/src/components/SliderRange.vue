<template>
    <div id="application">
        <input type="range" v-model="minValue" :min="0" :max="maxValue"  class="slider">
        <input type="range" v-model="maxValue" :min="minValue" :max="100" class="slider">
        <p id="minValue">Min Value: <span>{{ minValue }}</span></p>
        <p id="maxValue">Max Value: <span>{{ maxValue }}</span></p>
        <div><button type="submit" class="btn btn-primary rangeSearch" id="searchForRangeButton" @click="showPokemons()">Search</button></div>
    </div>
    <div class="cards">
      <div v-for="pokemon in this.choosedRange" :key="pokemon.name" :class="this.$parent.getPokemonClasses(pokemon)" style="width: 18rem;">
        <h5 class="card-title showId" data-id="{{pokemon.id}}">Number:{{ pokemon.id }}</h5>
        <div :class="{ 'favIcon icons': !this.$parent.checkForFavs(pokemon), 'favIcon favIconCheck icons': this.$parent.checkForFavs(pokemon) }"
          @click="this.$parent.addToFav(pokemon)"> <img class="images" src="../assets/lockIcon.png" alt="" srcset=""> </div>
        <div
          :class="{ 'addTeamIcon icons': !this.$parent.checkForTeam(pokemon), 'addTeamIcon teamIconCheck icons': this.$parent.checkForTeam(pokemon) }"
          @click="this.$parent.addToTeam(pokemon)" />
        <img class="card-img-top" :src="pokemon.sprites.front_default" alt="Card image cap">
        <div class="card-body">
          <h5 class="card-title">{{ pokemon.name }}</h5>
          <p>Types: {{ this.$parent.getPokemonTypes(pokemon) }}</p>
        </div>
      </div>
    </div>
    
</template>
<script>
export default{
    data() {
        return {
            minValue: 0,
            maxValue: 100,
            pokemons: [],
            choosedRange: [],
            div: {},
        }
    }, 
    methods: {
        showPokemons() {
          let divApp = document.getElementById('application')
          divApp.style.display = 'none'
          this.div = divApp
          this.pokemons = this.$parent.pokemons
          this.choosedRange = this.pokemons.filter(pokemon =>
            pokemon.id <= this.maxValue && pokemon.id >= this.minValue
          )
        }
    }
}
</script>
<style>
  #application {
    display: block;
    z-index: 999;
    border-radius: 20px;
    padding: 14px;
    left: 250px;
    color: white;
    top: 220px;
    position: absolute;
    background-color: #000000d9;
  }
  .rangeSearch {
    z-index: 200;
  }
  #minValue, #maxValue {
    margin: 0;
  }
  #minValue {
    left: 39px;
    position: absolute;
  }
  #maxValue {
    left: 60px;
    position: relative;
  }
  .slider-container {
    width: 80%;
    margin: 50px auto;
  }

  .slider {
    margin-right: 10px;
    -webkit-appearance: none;
    width: 43%;
    height: 15px;
    border-radius: 5px;
    background: #d3d3d3;
    outline: none;
    opacity: 0.7;
    transition: opacity .2s;
  }

  .slider::-webkit-slider-thumb {
    -webkit-appearance: none;
    appearance: none;
    width: 25px;
    height: 25px;
    border-radius: 50%;
    background: #4CAF50;
    cursor: pointer;
  }

  .slider::-moz-range-thumb {
    width: 25px;
    height: 25px;
    border-radius: 50%;
    background: #4CAF50;
    cursor: pointer;
  }
</style>