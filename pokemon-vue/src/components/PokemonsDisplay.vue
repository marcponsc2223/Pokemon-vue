<template>
  <div>
    <div v-if="!pokemons">Loading Pokemons...</div>
    <div v-else >
      <!-- class="card" -->
      <div class="favButtonSearch"><button type="button" class="btn btn-primary" @click="displayFavs()">Search for Favs</button></div>
      <div class="allButtonSearch"><button type="button" class="btn btn-primary" @click="displayAll()">Search for All</button></div>
      <div v-if="!fav ||favPokemons.length <= 0" class="cards">
        <div v-for="pokemon in this.pokemons" :key="pokemon.name" :class="
          {
          'fire card': isFire(pokemon), 
          'aqua card': isAqua(pokemon), 
          'grass card': isGrass(pokemon), 
          'poison card': isPoison(pokemon), 
          'electric card': isElectric(pokemon), 
          'ground card': isGround(pokemon), 
          'psychic card': isPsychic(pokemon),
          'dragon card': isDragon(pokemon),
          'normal card': isNormal(pokemon),
          'bug card': isBug(pokemon),
          'fairy card': isFairy(pokemon),  
          'fighting card': isFight(pokemon),
          'fly card': isFly(pokemon)  
          }" 
          style="width: 18rem;" >
          <h5 class="card-title">Id: {{pokemon.id}}</h5>
          <div :class="
          {
            'favIcon': !checkForFavs(pokemon),
            'favIcon favIconCheck': checkForFavs(pokemon)

          }" 
          @click="addToFav(pokemon)"></div>
          <img class="card-img-top" :src="pokemon.sprites.front_default" alt="Card image cap" >
            <div class="card-body">
              <h5 class="card-title">{{pokemon.name}}</h5>
              <p >Types: {{pokemon.types.map(type => type.type.name)}}</p>
              <!-- <p class="card-text">{{}}</p> -->
            </div>
        </div>
      </div>
      <div v-else class="cards">
        <div v-for="pokemon in this.favPokemons" :key="pokemon.name" :class="
          {
          'fire card': isFire(pokemon), 
          'aqua card': isAqua(pokemon), 
          'grass card': isGrass(pokemon), 
          'poison card': isPoison(pokemon), 
          'electric card': isElectric(pokemon), 
          'ground card': isGround(pokemon), 
          'psychic card': isPsychic(pokemon),
          'dragon card': isDragon(pokemon),
          'normal card': isNormal(pokemon),
          'bug card': isBug(pokemon),
          'fairy card': isFairy(pokemon),  
          'fighting card': isFight(pokemon),
          'fly card': isFly(pokemon)  
          }" 
          style="width: 18rem;" >
          <h5 class="card-title">Id: {{pokemon.id}}</h5>
          <div class="favIcon favIconCheck"></div>
          <img class="card-img-top" :src="pokemon.sprites.front_default" alt="Card image cap" >
            <div class="card-body">
              <h5 class="card-title">{{pokemon.name}}</h5>
              <p >Types: {{pokemon.types.map(type => type.type.name)}}</p>
              <!-- <p class="card-text">{{}}</p> -->
            </div>
        </div>
      </div>
    </div>
  </div>
</template>
<script>
export default {
    data() {
      return {
        title: "PokemonsDisplay",
        pokemons: [],
        fav: false,
        favPokemons: [],
        checkFavs: null,
      };
    },
    methods: {  
    fetchData() {
      fetch('https://pokeapi.co/api/v2/pokemon/?limit=151')
        .then(response => response.json())
        .then(data => {
          const pokemonPromises = data.results.map(pokemon => {
            return fetch(pokemon.url)
              .then(response => response.json());
          });
          // Promise para iterar sobre todos los datos
          Promise.all(pokemonPromises)
            .then(pokemonDataArray => {
              this.pokemons = pokemonDataArray;
            });
        });
    },
    checkForFavs(pokemon) {
      for (const i of this.favPokemons) {
        if (pokemon.name === i.name) {
            // this.checkFavs = true
            return true;
        } else {
          this.checkFavs = false
        }
      }
      return this.checkFavs
    },
    addToFav(pokemon) {
      // Remover el pokemon si ya existe en favPokemons
      let index = this.favPokemons.findIndex(p => p.name === pokemon.name);
      if (index !== -1) {
        this.favPokemons.splice(index, 1);
      }
      // Agregar el pokemon a favPokemons y aplicar la clase 'favIconCheck' al icono
      this.favPokemons.push(pokemon);
      let favIcon = document.querySelector('.fav Icon[data-id="' + pokemon.id + '"]');
      if (favIcon) {
        favIcon.classList.add('favIconCheck');
      }
    },
    displayFavs() {
      this.fav = true
    },
    displayAll() {
      this.fav = false
    },
    isFire(pokemon) {
      // El metodo some es para verificar si al menos un elemento cumple con la condicion.
      return pokemon.types.some(type => type.type.name === 'fire')
    },
    isAqua(pokemon) {
      return pokemon.types.some(type => type.type.name === 'water')
    },
    isGrass(pokemon) {
      return pokemon.types.some(type => type.type.name === 'grass')
    },
    isPoison(pokemon) {
      return pokemon.types.some(type => type.type.name === 'poison')
    },
    isElectric(pokemon) {
      return pokemon.types.some(type => type.type.name === 'electric')
    },
    isGround(pokemon) {
      return pokemon.types.some(type => type.type.name === 'ground')
    },
    isPsychic(pokemon) {
      return pokemon.types.some(type => type.type.name === 'psychic')
    },
    isDragon(pokemon) {
      return pokemon.types.some(type => type.type.name === 'dragon')
    },
    isNormal(pokemon) {
      return pokemon.types.some(type => type.type.name === 'normal')
    },
    isBug(pokemon) {
      return pokemon.types.some(type => type.type.name === 'bug')
    },
    isFairy(pokemon) {
      return pokemon.types.some(type => type.type.name === 'fairy')
    },
    isFly(pokemon) {
      return pokemon.types.some(type => type.type.name === 'flying')
    },
    isFight(pokemon) {
      return pokemon.types.some(type => type.type.name === 'fighting')
    },
    },
    mounted() {
      this.fetchData();
    },
  };

</script>
<style >
    .cards {
      display: ruby;
    }
    .card {
      margin: 10px;
      padding: 10px;
      border-radius: 20px;
    }
    .favIconCheck {
      background-color: yellow !important;
    }
    .favIcon {
      right: 14px;
      position: absolute;
      background-color: grey;
      border-radius: 100%;
      width: 30px;
      height: 30px;
      transition: 0.5s;
    }
    .favIcon:hover {
      cursor: pointer;
      transform: scale(1.05);
    }
    .favButtonSearch {
      left: 80px;
      top: 200px;
      position: absolute;
    }
    .allButtonSearch {
      right: 80px;
      top: 200px;
      position: absolute;
    }
    .grass {
      background-color: #a2dba2;
      border: 3px solid green;
    }
    .fire {
      background-color: #dba2a2;
      border: 3px solid red;
    }
    .aqua {
      background-color: #a2b9db;
      border: 3px solid blue;
    }
    .poison {
      background-color: #dba2d8;
      border: 3px solid rebeccapurple;
    }
    .electric {
      background-color: #dadba2;
      border: 3px solid yellow;
    }
    .ground {
      background-color: #a07f7f;
      border: 3px solid brown;
    }
    .psychic {
      background-color: #d087ec;
      border: 3px solid rgb(128, 0, 255);
    }
    .dragon {
      background-color: #dbb7a2;
      border: 3px solid #DE7339;
    }
    .normal {
      background-color: #b4b3b2;
      border: 3px solid #3f3f3f;
    }
    .bug {
      background-color: #76db62;
      border: 3px solid #2bff60;
    }
    .fairy {
      background-color: #d87cc9;
      border: 3px solid #ad26be;
    }
    .fly {
      background-color: #a9cce9;
      border: 3px solid #2b99ff;
    }
    .fighting {
      background-color: #dcf18d;
      border: 3px solid #d8ff2b;
    }
</style>