<template>
  <div>
    <div v-if="!pokemons">Loading Pokemons...</div>
    <div v-else >
      <!-- class="card" -->
      <div class="favButtonSearch"><button type="button" class="btn btn-primary" @click="displayFavs()">Search for Favs</button></div>
      <div class="allButtonSearch"><button type="button" class="btn btn-primary" @click="displayAll()">Search for All</button></div>
      <div class="teamButtonSearch"><button type="button" class="btn btn-primary" @click="displayTeam()">Search for team</button></div>
      <div v-if="!fav ||favPokemons.length <= 0" class="cards">
        <div v-for="pokemon in pokemons" :key="pokemon.name" :class="getPokemonClasses(pokemon)" style="width: 18rem;">
          <h5 class="card-title">Id: {{ pokemon.id }}</h5>
          <div :class="{ 'favIcon icons': !checkForFavs(pokemon), 'favIcon favIconCheck icons': checkForFavs(pokemon) }" @click="addToFav(pokemon)"> <img class="images" src="../assets/lockIcon.png" alt="" srcset=""> </div>
          <div class="icons addTeamIcon" @click="addToTeam(pokemon)"></div>
          <!-- <p>Add to team 1</p> -->
          <img class="card-img-top" :src="pokemon.sprites.front_default" alt="Card image cap">
          <div class="card-body">
            <h5 class="card-title">{{ pokemon.name }}</h5>
            <p>Types: {{ getPokemonTypes(pokemon) }}</p>
          </div>
        </div>
      </div>
      <div v-else class="cards">
        <div v-for="pokemon in this.favPokemons" :key="pokemon.name" :class="getPokemonClasses(pokemon)" style="width: 18rem;">
          <h5 class="card-title">Id: {{ pokemon.id }}</h5>
          <img class="card-img-top" :src="pokemon.sprites.front_default" alt="Card image cap">
          <div class="card-body">
            <h5 class="card-title">{{ pokemon.name }}</h5>
            <p>Types: {{ getPokemonTypes(pokemon) }}</p>
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
        team: [],
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
      addToTeam(pokemon) {
        this.team.push(pokemon)
        let teamIcon = document.querySelector('.addTeamIcon Icon[data-id="' + pokemon.id + '"]');
        console.log('hola');
        if (teamIcon) {
          teamIcon.classList.add('teamIconCheck');
        }
        
      },
      displayFavs() {
        this.fav = true
      },
      displayAll() {
        this.fav = false
      },
      displayTeam() {
        this.fav = false
      },
      getPokemonClasses(pokemon) {
      const types = pokemon.types.map(type => type.type.name);
      return {
        'fire card': types.includes('fire'),
        'aqua card': types.includes('water'),
        'grass card': types.includes('grass'),
        'poison card': types.includes('poison'),
        'electric card': types.includes('electric'),
        'ground card': types.includes('ground'),
        'psychic card': types.includes('psychic'),
        'dragon card': types.includes('dragon'),
        'normal card': types.includes('normal'),
        'bug card': types.includes('bug'),
        'fairy card': types.includes('fairy'),
        'fighting card': types.includes('fighting'),
        'fly card': types.includes('flying')
      };
    },
    getPokemonTypes(pokemon) {
      return pokemon.types.map(type => type.type.name).join(', ');
    }
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
    .images {
      width: 30px;
    }
    .icons {
      background-color: grey;
      position: absolute;
      border-radius: 100%;
      width: 34px;
      height: 36px;
      transition: 0.5s;
    }
    .favIcon {
      right: 14px !important;
    }
    .addTeamIcon {
      background-color: unset;
      width: 42px;
      background-repeat: no-repeat;
      background-size: 37px;
      background-image: url('../assets/addToTeam.svg');
      left: 14px !important;
    }
    .teamIconCheck {
      background-image: url('../assets/team.png') !important;
    }
    .favIcon:hover, .addTeamIcon:hover {
      cursor: pointer;
      transform: scale(1.05);
    }
    .favButtonSearch, .allButtonSearch, .teamButtonSearch {
      top: 200px;
      position: absolute;
    }
    .favButtonSearch {
      left: 80px;
    }
    .allButtonSearch {
      right: 80px;
    }
    .teamButtonSearch {
      right: 310px;

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